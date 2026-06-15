# Workflow Rules — SmartEnglish AI

## Nguyên tắc khi viết code

### 1. Trước khi làm bất cứ thứ gì
- Đọc `CLAUDE.md` để nhớ kiến trúc tổng thể
- Nếu sửa feature đã có → đọc file trong feature đó trước, không đoán mò
- Nếu tạo feature mới → tạo đúng cấu trúc `data/domain/presentation/`

### 2. Tạo file mới
- Luôn đặt file đúng thư mục theo feature
- Tên file: `snake_case.dart`
- Tên class: `PascalCase`
- Mỗi file chỉ chứa 1 class chính (trừ model nhỏ đi kèm)

### 3. Thứ tự làm 1 feature mới
```
1. Tạo entity (domain/entities/)
2. Tạo model + fromJson/toJson (data/models/)
3. Tạo repository interface + implementation (data/repositories/)
4. Tạo provider/notifier (presentation/bloc/)
5. Tạo page + widgets (presentation/pages/, presentation/widgets/)
6. Đăng ký route trong core/constants/routes.dart
```

### 4. Khi gọi API
- Luôn dùng `ApiClient` trong `core/services/api_client.dart`
- Wrap kết quả bằng `Either<Failure, T>` hoặc `AsyncValue<T>` (Riverpod)
- Xử lý đủ 3 case: loading / data / error — không bỏ sót error case
- Timeout mặc định: 30 giây

### 5. Không được làm
- ❌ Không hardcode màu sắc — dùng `AppColors.primary` từ `core/constants/`
- ❌ Không hardcode string — dùng `AppStrings` hoặc const
- ❌ Không để `print()` trong code production — dùng `debugPrint()` hoặc logger
- ❌ Không dùng `BuildContext` sau `await` mà không kiểm tra `mounted`
- ❌ Không setState trong StatelessWidget
- ❌ Không import trực tiếp package trong feature — phải qua service/repository

### 6. Khi tôi yêu cầu "làm màn hình X"
Hiểu là phải tạo đủ: page + widget con + provider + kết nối API (nếu có).
Không tạo UI xong rồi để data hardcode mà không báo.

### 7. Commit message convention
```
feat: thêm màn hình quiz generator
fix: sửa lỗi phát âm không ghi âm được trên iOS
refactor: tách widget FlashcardItem ra file riêng
chore: cập nhật pubspec.yaml thêm package fl_chart
```

### 8. Khi gặp lỗi build
- Chạy `flutter clean && flutter pub get` trước
- Nếu lỗi liên quan đến code generation: `dart run build_runner build --delete-conflicting-outputs`
- Báo lỗi cụ thể, không đoán — đọc stack trace

### 9. Free/Premium logic
- Mọi tính năng Premium phải kiểm tra qua `AuthProvider.isPremium`
- Nếu Free và vượt giới hạn → show `PremiumGateWidget` (đã có sẵn trong `shared/widgets/`)
- Không viết logic Premium rải rác khắp nơi

### 10. Quản lý context & token (Claude Code)
- Nếu task lớn → chia nhỏ thành subtask, làm từng bước
- Sau mỗi feature hoàn thành → tóm tắt đã làm gì, file nào thay đổi
- Nếu không chắc → hỏi trước, không tự đoán rồi viết sai hướng
