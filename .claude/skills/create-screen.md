# Skill: Tạo Màn Hình (Screen)

## Mục đích
Tạo nhanh 1 màn hình Flutter mới trong feature đã có sẵn, đúng design system.

## Cách dùng
```
@skill create-screen [feature] [tên màn hình] [mô tả]
```

Ví dụ:
```
@skill create-screen flashcard DeckSelectionScreen "Màn hình chọn bộ thẻ để học"
```

## Checklist khi tạo màn hình

### Cấu trúc page bắt buộc
- [ ] `SafeArea` bọc toàn bộ
- [ ] `AppBar` hoặc custom header nếu design đặc biệt
- [ ] Xử lý đủ 3 trạng thái: loading / data / error
- [ ] Dùng màu từ `AppColors`, spacing từ `AppSpacing`
- [ ] `const` cho widget không thay đổi

### Màn hình gọi tính năng AI (image_scan, speaking, chatbot, quiz, writing, roleplay)
- [ ] Dùng `AiLoadingIndicator` thay vì loading thông thường
- [ ] Timeout dài hơn (45–60s) cho request
- [ ] Có nút Hủy trong lúc đang chờ AI xử lý
- [ ] Xử lý riêng case `AI_SERVICE_UNAVAILABLE`
- [ ] Hiển thị số lượt còn lại (Free) TRƯỚC khi gọi API nếu có giới hạn

### Màn hình có list
- Dùng `ListView.builder` hoặc `GridView.builder`
- Có `EmptyView` khi list rỗng
- Có `RefreshIndicator` nếu cần pull-to-refresh

### Màn hình có form
- Dùng `AppTextField` từ `shared/widgets/`
- Validate trước khi submit
- Disable nút submit khi đang loading
- Show error inline dưới field

### Màn hình có Premium gate
```dart
PremiumGateWidget(
  isLocked: !ref.watch(authProvider).isPremium,
  feature: 'AI Roleplay hội thoại',
  child: RoleplayWidget(),
)
```

## Template nhanh

```dart
class [Name]Screen extends ConsumerWidget {
  const [Name]Screen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return Scaffold(
      backgroundColor: AppColors.background,
      appBar: AppBar(
        backgroundColor: AppColors.surface,
        elevation: 0,
        title: Text('[Title]', style: AppTextStyles.headingSmall),
        foregroundColor: AppColors.textPrimary,
      ),
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(AppSpacing.lg),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              // TODO: content
            ],
          ),
        ),
      ),
    );
  }
}
```

## Lưu ý đặc biệt theo loại màn hình

### Màn hình camera (image_scan → gọi /ai/image-recognition)
- Xin permission camera trước khi mở
- Nén ảnh trước khi upload (`imageQuality: 85`)
- Fallback UI nếu permission bị từ chối

### Màn hình ghi âm (speaking → gọi /ai/speaking/score)
- Xin permission microphone
- Visual feedback rõ ràng khi đang ghi (waveform hoặc pulse animation)
- Hiển thị kết quả ở mức tổng quát (điểm + nhận xét text), không vẽ phân tích IPA
  chi tiết từng âm vị (Gemini không chuyên việc này — xem `rules/ai-integration.md`)

### Màn hình chat (chatbot → gọi /ai/chat/message)
- `ScrollController` để auto-scroll xuống khi có tin nhắn mới
- `TextEditingController` dispose đúng cách
- Show typing indicator khi AI đang trả lời (đặc biệt quan trọng vì Gemini qua
  backend có thể mất vài giây)

### Màn hình thanh toán (premium)
- Không lưu thông tin thẻ local
- Luôn verify với backend sau khi thanh toán
- Show loading rõ ràng trong lúc xử lý
