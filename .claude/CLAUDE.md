# SmartEnglish AI — Project Brain

## Tổng quan dự án
Ứng dụng học tiếng Anh tích hợp AI, dành cho Mobile (iOS + Android).
- **Mobile**: Flutter
- **Backend**: Spring Boot Microservices (repo riêng, không nằm trong repo Flutter này)
- **AI Engine**: Google Gemini API (1 API key dùng cho hầu hết tính năng AI)

## Vai trò của Gemini trong hệ thống
Gemini **không được gọi trực tiếp từ Flutter app**. Luồng đúng là:
```
Flutter App → Spring Boot Microservice (ai-service) → Gemini API → trả kết quả → Flutter
```
Lý do bắt buộc đi qua Backend:
- Bảo vệ API Key (không bao giờ để Gemini key trong code Flutter hoặc `.env` của client)
- Backend kiểm soát được giới hạn Free/Premium (rate limit theo user)
- Backend có thể cache, log, retry khi Gemini lỗi
- Dễ đổi sang model khác sau này mà không cần update app

→ Flutter chỉ gọi REST API của Spring Boot (`/api/v1/...`), không bao giờ import Gemini SDK trực tiếp.

## Các tính năng AI dùng Gemini (qua Backend)
| Tính năng | Gemini dùng cho | Model gợi ý |
|---|---|---|
| AI Image Recognition | Nhận diện vật thể trong ảnh (multimodal vision) | `gemini-2.5-flash` hoặc `gemini-2.5-pro` |
| AI Chatbot | Hỏi đáp ngữ pháp, từ vựng | `gemini-2.5-flash` |
| AI Writing Checker | Chấm điểm bài viết, sửa lỗi, rewrite | `gemini-2.5-pro` (cần reasoning tốt hơn) |
| AI Quiz Generator | Sinh câu hỏi từ kho từ vựng | `gemini-2.5-flash` |
| AI Roleplay | Hội thoại nhiều lượt (multi-turn) | `gemini-2.5-flash` |
| AI Personalized Learning Path | Phân tích kết quả test, đề xuất lộ trình | `gemini-2.5-flash` |
| AI Speaking/Pronunciation | Chấm điểm phát âm tổng quát (đơn giản hóa, không phân tích IPA chi tiết từng âm vì Gemini không chuyên về việc này) | `gemini-2.5-flash` + Speech-to-Text riêng nếu cần |

> Ghi chú: Phần Speaking đơn giản hóa so với bản spec ban đầu — không cam kết phân tích IPA chi tiết
> từng âm tiết (cần engine chuyên dụng như Azure Speech). Gemini chỉ chấm điểm tổng quát + góp ý chung.

## Kiến trúc thư mục Flutter
```
lib/
├── core/
│   ├── constants/         # màu, text style, spacing, routes
│   ├── theme/             # AppTheme, dark/light
│   ├── utils/             # helpers, extensions
│   └── services/          # API client, storage, auth service
├── features/
│   ├── auth/
│   ├── home/
│   ├── image_scan/        # gọi Spring Boot endpoint /ai/image-recognition
│   ├── flashcard/
│   ├── quiz/               # gọi Spring Boot endpoint /ai/quiz-generator
│   ├── speaking/           # gọi Spring Boot endpoint /ai/speaking-score
│   ├── chatbot/            # gọi Spring Boot endpoint /ai/chat (có thể SSE/WebSocket)
│   ├── reading/
│   ├── learning_path/      # gọi Spring Boot endpoint /ai/learning-path
│   ├── gamification/
│   └── analytics/
├── shared/
│   ├── widgets/
│   └── models/
└── main.dart
```

## Pattern bắt buộc: Feature-first + Clean Architecture
```
features/[feature_name]/
├── data/
│   ├── models/            # JSON serializable models — PHẢI khớp DTO của Spring Boot
│   └── repositories/      # gọi API, trả về Either<Failure, Data>
├── domain/
│   └── entities/
├── presentation/
│   ├── bloc/              # hoặc Riverpod provider
│   ├── pages/
│   └── widgets/
```

## State Management
- **Riverpod** (flutter_riverpod + hooks_riverpod)
- Không dùng setState ngoại trừ widget thuần UI đơn giản

## Design System
- Primary: `#1F4E79` (Deep Blue)
- Accent: `#00B0F0` (Sky Blue)
- Success: `#22C55E` | Warning: `#F59E0B` | Error: `#EF4444`
- Background: `#F8FAFC` | Surface: `#FFFFFF`
- Border Radius: 16px (card), 12px (button), 8px (chip)
- Font: Google Fonts — **Inter**
- Spacing: bội số của 4 (4, 8, 12, 16, 24, 32)

## Giao tiếp với Backend (Spring Boot Microservices)
- Base URL trong `.env`, KHÔNG hardcode
- Mọi gọi API qua `ApiClient` (Dio) trong `core/services/`
- Token JWT lưu trong `flutter_secure_storage`
- Spring Boot trả lỗi theo format chuẩn `ProblemDetail` (RFC 7807) → xem `rules/api.md`
- Các service liên quan AI có thể phản hồi chậm hơn (gọi Gemini) → luôn show loading rõ ràng,
  timeout dài hơn bình thường (45–60s) cho riêng các endpoint AI

## Packages chính (pubspec.yaml)
```yaml
flutter_riverpod, hooks_riverpod, riverpod_annotation
go_router
dio
flutter_secure_storage
shared_preferences
google_fonts
cached_network_image
image_picker
record + audioplayers
lottie
fl_chart
flutter_dotenv
freezed + json_serializable
```

## Quy tắc viết code → `rules/workflow.md`
## UI/UX conventions → `rules/design.md`
## Kỹ thuật Flutter nâng cao → `rules/tech-defaults.md`
## API endpoints (Spring Boot) → `rules/api.md`
## Quy ước tích hợp Gemini AI → `rules/ai-integration.md`
