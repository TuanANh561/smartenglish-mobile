# AI Integration Rules — Gemini API qua Spring Boot

## Nguyên tắc cốt lõi — KHÔNG BAO GIỜ VI PHẠM
> **Flutter app KHÔNG BAO GIỜ gọi trực tiếp Gemini API.**
> API Key của Gemini chỉ tồn tại trong Spring Boot backend (`ai-service`), không xuất hiện
> trong code Flutter, không nằm trong `.env` của client, không log ra console của app.

Nếu thấy bất kỳ đoạn code nào trong Flutter có `generativelanguage.googleapis.com` hoặc
import package `google_generative_ai` — đó là SAI, phải sửa lại để gọi qua Spring Boot.

## Luồng chuẩn cho mọi tính năng AI

```
1. User thao tác trên Flutter (chụp ảnh / gõ câu hỏi / ghi âm...)
2. Flutter gọi REST API của Spring Boot (multipart hoặc JSON)
3. Spring Boot (ai-service) nhận request → gọi Gemini API (server-side, có key)
4. Spring Boot xử lý response từ Gemini → map sang DTO chuẩn → trả về Flutter
5. Flutter parse JSON (model freezed) → hiển thị UI
```

## Vì sao response AI có thể chậm hơn — xử lý UX cho đúng
Gọi Gemini qua backend thường mất 1.5–8 giây tùy độ phức tạp request (đặc biệt ảnh, bài viết dài).
Quy tắc bắt buộc khi build UI cho tính năng AI:

- Luôn có `LoadingOverlay` hoặc skeleton loading riêng, không dùng spinner mặc định khô khan
- Timeout Dio cho riêng nhóm endpoint `/ai/*` set dài hơn: 45–60 giây (so với 30s mặc định)
- Hiển thị thông báo động viên khi đợi lâu, ví dụ:
  ```dart
  // Sau 3s vẫn đang loading → đổi text
  "AI đang phân tích ảnh của bạn..." → "Sắp xong rồi, chờ thêm chút nhé..."
  ```
- Luôn có nút "Hủy" khi đang gọi AI (đặc biệt voice/image — người dùng có thể đổi ý)
- Xử lý riêng error code `AI_SERVICE_UNAVAILABLE` (Gemini lỗi/quá tải phía backend) khác với
  lỗi network thường — show message: "AI đang quá tải, vui lòng thử lại sau ít phút"

## Pattern gọi API AI trong Repository

```dart
// features/image_scan/data/repositories/image_scan_repository_impl.dart
class ImageScanRepositoryImpl implements ImageScanRepository {
  final ApiClient _apiClient;

  @override
  Future<Either<AppException, ScanResult>> analyzeImage(File image) async {
    try {
      final formData = FormData.fromMap({
        'image': await MultipartFile.fromFile(image.path, filename: 'scan.jpg'),
      });

      // Dùng timeout riêng cho AI endpoint — dài hơn bình thường
      final response = await _apiClient.upload(
        '/ai/image-recognition',
        formData,
        timeout: const Duration(seconds: 60),
      );

      return Right(ScanResult.fromJson(response));
    } on DioException catch (e) {
      if (e.response?.data?['code'] == 'AI_SERVICE_UNAVAILABLE') {
        return Left(AppException.aiUnavailable());
      }
      return Left(AppException.fromDio(e));
    }
  }
}
```

## Streaming response (Chatbot) — nếu backend dùng SSE
Nếu Spring Boot dùng Server-Sent Events để forward stream từ Gemini token-by-token:

```dart
// Dùng Dio với ResponseType.stream
final response = await dio.post(
  '/ai/chat/message',
  data: {'conversationId': id, 'message': text},
  options: Options(responseType: ResponseType.stream),
);

final stream = response.data.stream as Stream<List<int>>;
stream.transform(utf8.decoder).listen((chunk) {
  // Parse từng SSE event, append vào message đang hiển thị
  // Cập nhật UI dần dần (typing effect tự nhiên)
});
```
Nếu backend KHÔNG dùng streaming (trả 1 lần toàn bộ response) — đơn giản hơn, chỉ cần
gọi POST bình thường và hiện toàn bộ câu trả lời sau khi nhận, kèm `TypingIndicatorWidget`
trong lúc chờ.

## Giới hạn Free/Premium — luôn kiểm tra TRƯỚC khi gọi AI
Mọi gọi API tới endpoint `/ai/*` đều có thể bị backend trả `LIMIT_EXCEEDED`. Flutter nên:

1. Lấy số lượt còn lại từ `/users/me/stats` hoặc response trước đó, hiển thị UI dạng
   "Còn 7/10 lượt hôm nay" TRƯỚC khi người dùng bấm hành động
2. Nếu biết chắc đã hết lượt (Free) → show `PremiumGateWidget` ngay, không gọi API tốn thời gian
3. Nếu backend trả `LIMIT_EXCEEDED` bất ngờ (vd: 2 tab cùng dùng) → cập nhật lại UI giới hạn

## Mapping tính năng AI cụ thể

### 1. Image Recognition (Nhóm 1)
- Resize/compress ảnh trước khi upload (`imageQuality: 85` trong `image_picker`) để giảm tải
  Gemini xử lý và giảm thời gian upload
- Không gửi ảnh quá 4MB — nén trước nếu cần (`flutter_image_compress`)

### 2. Speaking/Pronunciation (Nhóm 2) — ĐÃ ĐƠN GIẢN HÓA
- Gemini **không chuyên** phân tích phoneme/IPA chi tiết như Azure Speech
- Approach thực tế cho đồ án: ghi âm → backend chuyển audio thành text (Speech-to-Text riêng,
  có thể dùng `google-cloud-speech` Java SDK trong Spring Boot) → gửi transcript + audio metadata
  cho Gemini để Gemini đánh giá tổng quát (độ trôi chảy, từ đọc sai dựa trên so sánh transcript
  vs câu mẫu) — KHÔNG cam kết chấm điểm chính xác từng âm vị
- UI hiển thị: điểm tổng quát + nhận xét bằng văn bản (không vẽ IPA bubble chi tiết từng âm
  như bản thiết kế gốc, trừ khi sau này tích hợp thêm engine chuyên dụng)

### 3. AI Writing Checker (Nhóm 6 cũ → giờ đơn giản gọi qua `/ai/writing/check`)
- Gửi `{ text: "...", type: "essay" }` → Gemini trả lỗi phân loại (spelling/grammar/vocab) +
  điểm ước tính + bản rewrite — Backend cần prompt rõ ràng dạng JSON structured output

### 4. Chatbot & Roleplay
- Backend cần lưu lịch sử hội thoại (context) để gửi kèm Gemini mỗi lượt — không phải trách
  nhiệm của Flutter, Flutter chỉ gửi `conversationId` + tin nhắn mới nhất

## Khi backend dev báo "Gemini trả response không đúng format JSON"
Đây là vấn đề prompt engineering phía Spring Boot (cần ép Gemini trả structured output qua
`responseSchema` hoặc `responseMimeType: application/json`), KHÔNG phải lỗi Flutter.
Nếu app nhận JSON sai format → throw `FormatException` rõ ràng, không crash UI, hiện `ErrorView`
với message "Dữ liệu AI trả về không hợp lệ, vui lòng thử lại".
