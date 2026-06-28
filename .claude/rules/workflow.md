# Workflow Rules — SmartEnglish AI

## Nguyên tắc khi viết code

### 1. Trước khi làm bất cứ thứ gì
- Đọc `CLAUDE.md` để nhớ kiến trúc tổng thể (Flutter + Spring Boot Microservices + Gemini)
- Nếu task liên quan tính năng AI → đọc thêm `rules/ai-integration.md` trước khi code
- Nếu sửa feature đã có → đọc file trong feature đó trước, không đoán mò
- Nếu tạo feature mới → tạo đúng cấu trúc `data/domain/presentation/`

### 2. Tạo file mới
- Đặt file đúng thư mục theo feature
- Tên file: `snake_case.dart` · Tên class: `PascalCase`
- Mỗi file chỉ chứa 1 class chính (trừ model nhỏ đi kèm)
- Model JSON phải đặt tên field khớp với DTO Spring Boot trả về (thường là `camelCase`,
  xác nhận với backend dev nếu không chắc — Spring Boot mặc định Jackson serialize camelCase)

### 3. Thứ tự làm 1 feature mới
```
1. Tạo entity (domain/entities/)
2. Tạo model + fromJson/toJson (data/models/) — khớp DTO Spring Boot
3. Tạo repository interface + implementation (data/repositories/)
4. Tạo provider/notifier (presentation/bloc/)
5. Tạo page + widgets (presentation/pages/, presentation/widgets/)
6. Đăng ký route trong core/constants/routes.dart
```

### 4. Khi gọi API
- Luôn dùng `ApiClient` trong `core/services/api_client.dart`
- Wrap kết quả bằng `Either<Failure, T>` hoặc `AsyncValue<T>` (Riverpod)
- Xử lý đủ 3 case: loading / data / error
- Timeout mặc định: 30 giây — **riêng endpoint `/ai/*` dùng timeout 45–60 giây** (Gemini xử lý
  có thể chậm hơn API thường)
- Parse lỗi theo chuẩn `ProblemDetail` (RFC 7807) của Spring Boot — xem `rules/api.md`

### 5. Không được làm
- ❌ Không hardcode màu sắc — dùng `AppColors.primary`
- ❌ Không hardcode string — dùng `AppStrings` hoặc const
- ❌ Không để `print()` trong code production — dùng `debugPrint()`
- ❌ Không dùng `BuildContext` sau `await` mà không kiểm tra `mounted`
- ❌ Không setState trong StatelessWidget
- ❌ **Không bao giờ gọi Gemini API trực tiếp từ Flutter** — luôn qua Spring Boot backend
- ❌ Không lưu hoặc log API key (Gemini, hay bất kỳ key nào) trong code Flutter

### 6. Khi tôi yêu cầu "làm màn hình X"
Hiểu là phải tạo đủ: page + widget con + provider + kết nối API thật (Spring Boot endpoint).
Không tạo UI xong rồi để data hardcode mà không báo. Nếu backend chưa có endpoint đó,
báo rõ và đề xuất mock data tạm thời + TODO comment.

### 7. Commit message convention
```
feat: thêm màn hình quiz generator
fix: sửa lỗi phát âm không ghi âm được trên iOS
refactor: tách widget FlashcardItem ra file riêng
chore: cập nhật pubspec.yaml thêm package fl_chart
```

### 8. Khi gặp lỗi build
- `flutter clean && flutter pub get` trước
- Lỗi code generation: `dart run build_runner build --delete-conflicting-outputs`
- Lỗi gọi API: kiểm tra trước xem backend Spring Boot có đang chạy không (`localhost:8080`),
  kiểm tra log response thật trước khi đoán lỗi ở Flutter

### 9. Free/Premium logic
- Mọi tính năng Premium kiểm tra qua `AuthProvider.isPremium`
- Backend cũng kiểm tra lại (không tin tưởng hoàn toàn phía client) — nếu Free vượt giới hạn,
  backend trả `LIMIT_EXCEEDED`, Flutter hiển thị `PremiumGateWidget`
- Không viết logic Premium rải rác khắp nơi

### 10. Quản lý context & token (Claude Code)
- Task lớn → chia nhỏ thành subtask, làm từng bước
- Sau mỗi feature hoàn thành → tóm tắt đã làm gì, file nào thay đổi
- Nếu không chắc về hợp đồng API (response shape) với Spring Boot → hỏi trước, không tự đoán
