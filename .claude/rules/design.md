# Design Rules — SmartEnglish AI

## Design System nhanh

### Màu sắc (dùng AppColors, không hardcode hex)
```dart
// core/constants/app_colors.dart
primary       = Color(0xFF1F4E79)   // Deep Blue — nút chính, header
accent        = Color(0xFF00B0F0)   // Sky Blue — highlight, icon active
success       = Color(0xFF22C55E)   // xanh lá — đúng, hoàn thành
warning       = Color(0xFFF59E0B)   // cam — cảnh báo, streak
error         = Color(0xFFEF4444)   // đỏ — sai, lỗi
background    = Color(0xFFF8FAFC)   // nền tổng thể
surface       = Color(0xFFFFFFFF)   // nền card
textPrimary   = Color(0xFF1E293B)
textSecondary = Color(0xFF64748B)
divider       = Color(0xFFE2E8F0)
premiumGold   = Color(0xFFF59E0B)   // badge Premium
```

### Spacing (dùng bội số 4)
```dart
// core/constants/app_spacing.dart
xs  = 4.0
sm  = 8.0
md  = 12.0
lg  = 16.0
xl  = 24.0
xxl = 32.0
```

### Border Radius
```dart
// core/constants/app_radius.dart
card   = 16.0
button = 12.0
chip   = 8.0
input  = 12.0
avatar = 999.0  // circle
```

### Typography (Inter)
```dart
headingLarge  = Inter Bold 24px
headingMedium = Inter Bold 20px
headingSmall  = Inter SemiBold 16px
bodyLarge     = Inter Regular 16px
bodyMedium    = Inter Regular 14px
bodySmall     = Inter Regular 12px
label         = Inter Medium 12px
```

---

## Widget conventions

### Card chuẩn
```dart
Container(
  decoration: BoxDecoration(
    color: AppColors.surface,
    borderRadius: BorderRadius.circular(AppRadius.card),
    boxShadow: AppShadows.card,   // shadow nhẹ định nghĩa sẵn
  ),
  padding: EdgeInsets.all(AppSpacing.lg),
)
```

### Nút chính (Primary Button)
- Background: `AppColors.primary`
- Text: trắng, Inter SemiBold 15px
- Height: 52px, full width hoặc rộng vừa đủ
- BorderRadius: 12px
- Loading state: CircularProgressIndicator màu trắng, disable onPressed

### Nút phụ (Outline Button)
- Border: 1.5px `AppColors.primary`
- Text: `AppColors.primary`
- Cùng kích thước nút chính

### Input field
- Background: `Color(0xFFF1F5F9)` (xám nhạt)
- Border: none (không border khi idle), border xanh khi focused
- BorderRadius: 12px
- Prefix icon màu `AppColors.textSecondary`
- Height: 52px

### Bottom Tab Bar
- 5 tab: Home / Learn / AI / Stats / Profile
- Icon + label nhỏ
- Active: `AppColors.primary`, Inactive: `AppColors.textSecondary`
- Nền trắng, shadow nhẹ phía trên

---

## Màn hình Mobile
- Thiết kế cho 375px width (chuẩn iPhone)
- Dùng `SafeArea` tất cả các page
- `SingleChildScrollView` khi nội dung có thể dài
- Không hardcode height — dùng `MediaQuery` hoặc `Flexible/Expanded`

## Màn hình Web Admin (Flutter Web)
- Sidebar trái 240px, nền `AppColors.primary`
- Nội dung chính bên phải, nền `AppColors.background`
- Dùng `LayoutBuilder` để responsive (tablet/desktop)
- Bảng dữ liệu dùng `DataTable` hoặc custom table widget

---

## Một số widget tái sử dụng đã có sẵn (shared/widgets/)
- `AppButton` — primary/outline/text variants
- `AppTextField` — input chuẩn design
- `AppCard` — card với shadow mặc định
- `AppBadge` — chip nhỏ (Free/Premium/CEFR level)
- `PremiumGateWidget` — hiện khi tính năng cần Premium
- `LoadingOverlay` — loading toàn màn hình
- `ErrorView` — màn hình lỗi có nút retry
- `EmptyView` — màn hình trống có illustration
- `XpBanner` — banner thông báo nhận XP

## Animation
- Dùng `Lottie` cho animation phức tạp (confetti, loading AI...)
- Dùng `AnimatedContainer` / `AnimatedOpacity` cho transition đơn giản
- Duration chuẩn: 200ms (nhanh), 350ms (vừa), 500ms (chậm/dramatic)
- Curve: `Curves.easeInOut` mặc định
