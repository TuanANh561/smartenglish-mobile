<div align="center">

<img src="https://img.shields.io/badge/Flutter-3.22+-02569B?style=for-the-badge&logo=flutter&logoColor=white" />
<img src="https://img.shields.io/badge/Dart-3.4+-0175C2?style=for-the-badge&logo=dart&logoColor=white" />
<img src="https://img.shields.io/badge/Platform-iOS%20%7C%20Android-1F4E79?style=for-the-badge&logo=flutter&logoColor=white" />
<img src="https://img.shields.io/badge/Status-In%20Development-F59E0B?style=for-the-badge" />

# 📱 SmartEnglish AI — Mobile App

**Ứng dụng học tiếng Anh thông minh tích hợp Trí tuệ Nhân tạo**

*Học từ vựng qua ảnh chụp · Luyện phát âm với AI · Ghi nhớ bằng Flashcard SRS · Luyện thi IELTS / TOEIC*

[Tính năng](#-tính-năng) · [Kiến trúc](#-kiến-trúc) · [Cài đặt](#-cài-đặt-và-chạy-dự-án) · [Cấu trúc thư mục](#-cấu-trúc-thư-mục) · [Nhóm thực hiện](#-nhóm-thực-hiện)

</div>

-----

> **Repo này** là ứng dụng Mobile (iOS & Android) dành cho người học, xây dựng bằng Flutter.
> Các repo liên quan trong cùng hệ thống:
> 
> - 🖥️ [smartenglish-admin](https://github.com/...) — Web Admin Dashboard (React)
> - ⚙️ [smartenglish-backend](https://github.com/...) — Backend API (Node.js / FastAPI)

-----

## 🎯 Giới thiệu

**SmartEnglish AI** là đồ án tốt nghiệp ngành Công nghệ Thông tin, hướng đến xây dựng một ứng dụng học tiếng Anh toàn diện trên điện thoại — nơi trí tuệ nhân tạo không chỉ là tính năng phụ mà là cốt lõi của toàn bộ trải nghiệm học tập.

Thay vì học theo kiểu truyền thống, ứng dụng cho phép người học **chụp ảnh bất kỳ vật thể nào xung quanh** để học từ vựng ngay lập tức, **nói chuyện trực tiếp với AI** để luyện phát âm và hội thoại, ôn tập từ vựng theo lịch khoa học tự động và theo dõi toàn bộ tiến trình học qua dashboard thống kê trực quan.

-----

## ✨ Tính năng

### 📷 Nhóm 1 — Học qua Hình ảnh (AI Image Recognition)

Chụp ảnh hoặc tải ảnh từ thư viện. AI nhận diện vật thể và trả về từ vựng tiếng Anh kèm phiên âm IPA, nghĩa tiếng Việt, câu ví dụ và phát âm chuẩn bản ngữ. Đây là tính năng nổi bật nhất của ứng dụng — tận dụng camera điện thoại để biến mọi khoảnh khắc trong cuộc sống thành bài học từ vựng.

### 🗣️ Nhóm 2 — Luyện Phát âm & Nói (AI Speaking Practice)

Ghi âm giọng đọc → AI phân tích và chấm điểm phát âm từng âm tiết theo chuẩn IPA, chỉ ra lỗi cụ thể và hướng dẫn cách sửa. Tính năng AI Roleplay cho phép luyện hội thoại thời gian thực với AI đóng vai người bản ngữ theo các kịch bản thực tế (đặt món, phỏng vấn xin việc, hỏi đường…).

### 📖 Nhóm 3 — Flashcard & Spaced Repetition (SRS)

Hệ thống thẻ ghi nhớ áp dụng thuật toán **SuperMemo-2** — tự động tính toán thời điểm cần ôn lại từng từ để tránh quên, giúp từ vựng đi vào trí nhớ dài hạn hiệu quả hơn học thuộc lòng thông thường.

### 🧠 Nhóm 4 — AI Quiz Generator

AI tự động sinh câu hỏi kiểm tra từ kho từ vựng của người dùng, ưu tiên các từ đang yếu. Hỗ trợ 6 dạng bài: trắc nghiệm, điền từ, ghép nối, sắp xếp câu, chính tả, nghe-chọn. Gói Premium có mô phỏng đề thi IELTS / TOEIC chuẩn format.

### 🤖 Nhóm 5 — AI Chatbot & Writing Checker

Chatbot giải đáp ngữ pháp 24/7, tự động phát hiện lỗi khi người dùng viết tiếng Anh trong chat. Writing Checker chấm điểm bài viết theo tiêu chí IELTS và cung cấp bản viết lại được tối ưu hóa.

### 👂 Nhóm 6 — Reading & Listening Practice

Kho 500+ bài đọc và bài nghe phân cấp A1–B2. Tính năng chạm vào từ để tra nghĩa ngay mà không rời bài, transcript chạy chữ đồng bộ theo âm thanh (karaoke style).

### 🗺️ Nhóm 7 — AI Personalized Learning Path

Kiểm tra đầu vào 15 câu để xác định trình độ, phân tích lỗ hổng kiến thức (Knowledge Gap) và đề xuất lộ trình học cá nhân hóa, cập nhật tự động mỗi ngày theo kết quả thực tế.

### 🏆 Nhóm 8 — Gamification

Hệ thống điểm XP, 50 cấp độ từ Beginner đến Legend, 50+ huy hiệu thành tích, bảng xếp hạng tuần theo giải đấu (League), thử thách hàng ngày và cửa hàng đổi vật phẩm bằng điểm tích lũy.

### 📊 Nhóm 9 — Learning Analytics

Dashboard thống kê tiến độ học, biểu đồ Radar 5 kỹ năng, heatmap chuỗi ngày học, thông báo nhắc nhở thông minh phân tích đúng giờ người dùng hay học nhất.

-----

## 💎 Mô hình Freemium

|Tính năng                      |🆓 Miễn phí        |⭐ Premium     |
|-------------------------------|------------------|--------------|
|Quét ảnh nhận diện             |10 lượt / ngày    |Không giới hạn|
|Flashcard SRS                  |20 thẻ / ngày     |Không giới hạn|
|AI Chat                        |20 tin nhắn / ngày|Không giới hạn|
|Phân tích phát âm câu dài + IPA|❌                 |✅             |
|AI Roleplay hội thoại          |❌                 |✅             |
|AI Writing Checker             |❌                 |✅             |
|Lộ trình học cá nhân hóa       |❌                 |✅             |
|Mock Exam IELTS / TOEIC        |❌                 |✅             |
|Quảng cáo                      |Có                |Không         |

-----

## 🏗️ Kiến trúc

Ứng dụng này là **Mobile Client** trong hệ thống SmartEnglish AI gồm 3 repo độc lập:

```
┌─────────────────────┐     ┌─────────────────────┐
│   📱 Mobile App      │     │  🖥️  Web Admin       │
│  (repo này)          │     │  (React — repo khác) │
│  Flutter 3.22+       │     │  Quản lý hệ thống    │
│  iOS & Android       │     │  cho admin/giáo viên │
└──────────┬──────────┘     └──────────┬──────────┘
           │                           │
           └──────────┬────────────────┘
                      │ REST API / WebSocket
                      ▼
        ┌─────────────────────────────┐
        │   ⚙️  Backend API           │
        │  (Spring boot / Microservice)        │
        │  (repo riêng)               │
        └──────────┬──────────────────┘
                   │
       ┌───────────┴───────────────────────┐
       │                                   │
  ┌────▼────┐                    ┌─────────▼──────────┐
  │PostgreSQL│                   │    AI Services      │
  │ MongoDB  │                   │  Google Vision API  │
  │  Redis   │                   │  Azure Speech API   │
  └──────────┘                   │  OpenAI GPT-4 API   │
                                 │  Google TTS         │
                                 └────────────────────-┘
```

### Công nghệ sử dụng (repo này)

|Thành phần      |Công nghệ                                     |
|----------------|----------------------------------------------|
|Framework       |Flutter 3.22+ / Dart 3.4+                     |
|State Management|Riverpod + Hooks                              |
|Navigation      |go_router                                     |
|HTTP Client     |Dio                                           |
|Local Storage   |flutter_secure_storage, shared_preferences    |
|Code Generation |freezed, json_serializable, riverpod_generator|
|Charts          |fl_chart                                      |
|Animation       |Lottie                                        |
|Camera / Gallery|image_picker                                  |
|Audio Record    |record                                        |
|Audio Play      |audioplayers                                  |
|Environment     |flutter_dotenv                                |

-----

## 📁 Cấu trúc thư mục

```
lib/
├── core/                        # Dùng chung toàn app
│   ├── constants/               # Màu sắc, typography, spacing, routes
│   ├── theme/                   # AppTheme (light / dark)
│   ├── services/                # ApiClient, AuthService, StorageService
│   └── utils/                   # Extensions, helpers, formatters
│
├── features/                    # Mỗi tính năng = 1 folder độc lập
│   ├── auth/                    # Đăng ký, đăng nhập, Placement Test
│   ├── home/                    # Dashboard, Daily Challenge, Streak
│   ├── image_scan/              # Camera, AI nhận diện, kết quả scan
│   ├── flashcard/               # Deck management, SRS study session
│   ├── quiz/                    # Quiz setup, câu hỏi, kết quả
│   ├── speaking/                # Phát âm, Roleplay hội thoại
│   ├── chatbot/                 # AI Chat, Writing Checker
│   ├── reading/                 # Bài đọc, Tap-to-Translate
│   ├── listening/               # Bài nghe, transcript karaoke
│   ├── learning_path/           # Lộ trình học, Knowledge Gap
│   ├── gamification/            # XP, Badges, Leaderboard, Coin Shop
│   ├── analytics/               # Dashboard tiến độ, biểu đồ
│   ├── premium/                 # Màn hình nâng cấp, thanh toán
│   └── profile/                 # Hồ sơ, cài đặt
│
└── shared/                      # Widgets & models dùng nhiều nơi
    ├── widgets/                 # AppButton, AppCard, AppTextField...
    └── models/                  # Shared data models
```

Mỗi feature theo cấu trúc Clean Architecture:

```
features/[feature]/
├── data/
│   ├── models/          # JSON models (freezed + json_serializable)
│   └── repositories/    # Gọi API, trả về Either<Failure, Data>
├── domain/
│   └── entities/        # Pure Dart objects
└── presentation/
    ├── providers/        # Riverpod notifiers
    ├── pages/            # Màn hình chính
    └── widgets/          # Widget con của feature
```

-----

## 🚀 Cài đặt và chạy dự án

### Yêu cầu

- Flutter SDK `3.22+` — [Hướng dẫn cài đặt](https://docs.flutter.dev/get-started/install)
- Dart `3.4+`
- Xcode 15+ (build iOS, yêu cầu macOS)
- Android Studio hoặc VS Code với Flutter extension
- Thiết bị thật hoặc máy ảo (iOS Simulator / Android Emulator)

### 1. Clone repository

```bash
git clone https://github.com/TuanANh561/smartenglish-mobile.git
cd smartenglish-mobile
```

### 2. Cài đặt dependencies

```bash
flutter pub get
```

### 3. Tạo file `.env`

```bash
cp .env.example .env
```

Điền URL backend vào `.env`:

```env
API_BASE_URL=http://localhost:3000/api/v1
```

### 4. Generate code (freezed, Riverpod)

```bash
dart run build_runner build --delete-conflicting-outputs
```

### 5. Chạy ứng dụng

```bash
# Xem danh sách thiết bị đang kết nối
flutter devices

# Chạy trên iOS Simulator
flutter run -d ios

# Chạy trên Android Emulator hoặc thiết bị thật
flutter run -d android

# Chạy trên Chrome (chỉ dùng để dev/test nhanh, không phải sản phẩm web)
flutter run -d chrome
```

### Lệnh hữu ích khác

```bash
# Phân tích code
flutter analyze

# Dọn dẹp và cài lại
flutter clean && flutter pub get

# Build APK (Android)
flutter build apk --release

# Build App Bundle (Android — dùng để upload Play Store)
flutter build appbundle --release

# Build iOS (cần Xcode, macOS)
flutter build ios --release
```

-----

## 🎨 Design System

|Token       |Giá trị  |Dùng cho                      |
|------------|---------|------------------------------|
|`primary`   |`#1F4E79`|Nút chính, header, icon active|
|`accent`    |`#00B0F0`|Highlight, badge, progress    |
|`success`   |`#22C55E`|Đáp án đúng, hoàn thành       |
|`warning`   |`#F59E0B`|Streak, cảnh báo, Premium     |
|`error`     |`#EF4444`|Đáp án sai, lỗi               |
|`background`|`#F8FAFC`|Nền tổng thể                  |
|`surface`   |`#FFFFFF`|Nền card                      |

Font: **Inter** (Google Fonts) · Border radius card: `16px` · Spacing: bội số `4px`

-----

## 👥 Nhóm thực hiện

|Thành viên     |MSSV|Vai trò                            |
|---------------|----|-----------------------------------|
|Nguyễn Thế Anh |—   |Mobile Developer — Feature chính   |
|Nguyễn Tuấn Anh|—   |Mobile Developer — UI & Integration|

**Giáo viên hướng dẫn:** *(tên cô/thầy)*  
**Trường:** *(Trường Đại Học Công Nghiệp Tp. Hồ Chí Minh - IUH)*  
**Năm:** 2026

-----

## 📄 Tài liệu & Repo liên quan

|                             |Link                                          |
|-----------------------------|----------------------------------------------|
|🖥️ Web Admin Dashboard (React)|[smartenglish-admin](https://github.com/...)  |
|⚙️ Backend API                |[smartenglish-backend](https://github.com/...)|
|🎨 UI Mockups (Figma)         |[Figma Design](https://figma.com/...)         |
|📋 Đặc tả chức năng           |<docs/feature-spec.md>                        |
|🗄️ Database Schema            |<docs/database-schema.sql>                    |

-----

## 📝 Giấy phép

Dự án được thực hiện phục vụ mục đích học thuật (Đồ án Tốt nghiệp).  
Không sử dụng cho mục đích thương mại khi chưa có sự đồng ý của nhóm tác giả.

-----

<div align="center">

Made with ❤️ by SmartEnglish AI Team · 2026

</div>
