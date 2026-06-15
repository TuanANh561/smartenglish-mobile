# Skill: Tạo Màn Hình (Screen)

## Mục đích
Tạo nhanh 1 màn hình Flutter mới trong feature đã có sẵn, đúng design system.

## Cách dùng
```
@skill create-screen [feature] [tên màn hình] [mô tả]
```

Ví dụ:
```
@skill create-screen flashcard DeckSelectionScreen "Màn hình chọn bộ thẻ để học, hiển thị grid 2 cột, mỗi card hiển thị tên deck, số thẻ và badge số thẻ cần ôn hôm nay"
```

## Checklist khi tạo màn hình

### Cấu trúc page bắt buộc
- [ ] `SafeArea` bọc toàn bộ
- [ ] `AppBar` hoặc custom header nếu design đặc biệt
- [ ] Xử lý đủ 3 trạng thái: loading / data / error
- [ ] Dùng màu từ `AppColors`, spacing từ `AppSpacing`
- [ ] `const` cho widget không thay đổi

### Màn hình có list
- Dùng `ListView.builder` hoặc `GridView.builder`
- Có `EmptyView` khi list rỗng
- Có `RefreshIndicator` nếu cần pull-to-refresh

### Màn hình có form
- Dùng `AppTextField` từ `shared/widgets/`
- Validate trước khi submit
- Disable nút submit khi đang loading
- Show error inline dưới field, không dùng snackbar cho validation

### Màn hình có Premium gate
```dart
// Bọc tính năng Premium:
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

### Màn hình camera (image_scan)
- Xin permission camera trước khi mở
- Fallback UI nếu permission bị từ chối

### Màn hình ghi âm (speaking)
- Xin permission microphone
- Visual feedback rõ ràng khi đang ghi (waveform hoặc pulse animation)
- Nút bấm giữ để nói (Push-to-talk) hoặc toggle

### Màn hình chat (chatbot)
- `ScrollController` để auto-scroll xuống khi có tin nhắn mới
- `TextEditingController` dispose đúng cách
- Show typing indicator khi AI đang trả lời

### Màn hình thanh toán (premium)
- Không lưu thông tin thẻ local
- Luôn verify với backend sau khi thanh toán
- Show loading rõ ràng trong lúc xử lý
