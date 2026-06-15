# SmartEnglish AI — Project Brain

## Tổng quan dự án
Ứng dụng học tiếng Anh đa nền tảng tích hợp AI.
- **Mobile**: Flutter (iOS + Android)
- **Web Admin**: Flutter Web (chỉ dùng Chrome để test)
- **Backend**: Node.js/FastAPI (riêng, không nằm trong repo này)

## Kiến trúc thư mục Flutter
```
lib/
├── core/                  # dùng chung toàn app
│   ├── constants/         # màu, text style, spacing, routes
│   ├── theme/             # AppTheme, dark/light
│   ├── utils/             # helpers, extensions
│   └── services/          # API client, storage, auth service
├── features/              # mỗi feature = 1 folder độc lập
│   ├── auth/              # đăng ký, đăng nhập, placement test
│   ├── home/              # dashboard, streak, daily challenge
│   ├── image_scan/        # chụp ảnh, nhận diện AI
│   ├── flashcard/         # SRS flashcard, deck management
│   ├── quiz/              # quiz generator, mock exam
│   ├── speaking/          # pronunciation, roleplay
│   ├── chatbot/           # AI chatbot, writing checker
│   ├── reading/           # reading & listening practice
│   ├── learning_path/     # personalized path, placement
│   ├── gamification/      # XP, badges, leaderboard, shop
│   └── analytics/         # dashboard, streak, notifications
├── shared/                # widgets dùng lại nhiều nơi
│   ├── widgets/
│   └── models/
└── main.dart
```

## Pattern bắt buộc: Feature-first + Clean Architecture
Mỗi feature có cấu trúc:
```
features/[feature_name]/
├── data/
│   ├── models/            # JSON serializable models
│   └── repositories/      # gọi API, trả về Either<Failure, Data>
├── domain/
│   └── entities/          # pure Dart objects
├── presentation/
│   ├── bloc/              # hoặc riverpod provider
│   ├── pages/
│   └── widgets/
```

## State Management
- **Riverpod** (flutter_riverpod + hooks_riverpod)
- Không dùng setState ngoại trừ widget thuần UI đơn giản
- Mỗi provider đặt trong file riêng trong thư mục `bloc/` hoặc `providers/`

## Design System
- Primary: `#1F4E79` (Deep Blue)
- Accent: `#00B0F0` (Sky Blue)
- Success: `#22C55E` | Warning: `#F59E0B` | Error: `#EF4444`
- Background: `#F8FAFC` | Surface: `#FFFFFF`
- Border Radius: 16px (card), 12px (button), 8px (chip)
- Font: Google Fonts — **Inter**
- Spacing: bội số của 4 (4, 8, 12, 16, 24, 32)

## API & Environment
- Base URL lưu trong `.env` (dùng flutter_dotenv), không hardcode
- Mọi API call qua `ApiClient` singleton trong `core/services/`
- Token lưu trong `flutter_secure_storage`
- Xem chi tiết endpoint → `rules/api.md`

## Packages chính (pubspec.yaml)
```yaml
flutter_riverpod, hooks_riverpod, riverpod_annotation
go_router                  # navigation
dio                        # HTTP client
flutter_secure_storage     # lưu token
shared_preferences         # settings nhẹ
google_fonts               # Inter font
cached_network_image       # ảnh cache
image_picker               # chụp/chọn ảnh
record + audioplayers      # ghi âm & phát âm
lottie                     # animation
fl_chart                   # biểu đồ analytics
flutter_dotenv             # env variables
freezed + json_serializable # model generation
```

## Quy tắc viết code → xem `rules/workflow.md`
## UI/UX conventions → xem `rules/design.md`
## Kỹ thuật Flutter nâng cao → xem `rules/tech-defaults.md`
