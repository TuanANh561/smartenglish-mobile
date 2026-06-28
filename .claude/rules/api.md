# API Reference — SmartEnglish AI (Spring Boot Microservices)

## Base URL
```
DEV:  http://localhost:8080/api/v1
PROD: https://api.smartenglish.ai/api/v1
```
Cấu hình trong `.env` → `API_BASE_URL`

Nếu backend tách microservice qua API Gateway (Spring Cloud Gateway), mọi request từ Flutter
vẫn chỉ gọi 1 base URL duy nhất (gateway), không cần biết service nào xử lý phía sau.

## Auth Headers
```
Authorization: Bearer <access_token>
Content-Type: application/json
```
Token là JWT do `auth-service` (Spring Boot) cấp, xác thực qua Spring Security.

---

## Quy ước response chuẩn Spring Boot

### Response thành công
Spring Boot thường wrap response theo 1 trong 2 dạng — xác nhận với backend dev dạng nào được dùng:

**Dạng A — trả trực tiếp đối tượng/list:**
```json
{ "id": "uuid", "word": "apple", "ipa": "/ˈæp.əl/" }
```

**Dạng B — wrap chuẩn (khuyến nghị dùng nếu team tự định nghĩa):**
```json
{
  "success": true,
  "data": { "id": "uuid", "word": "apple" },
  "timestamp": "2026-06-27T10:00:00Z"
}
```

### Response lỗi — theo chuẩn RFC 7807 (ProblemDetail, mặc định Spring Boot 3.x)
```json
{
  "type": "https://api.smartenglish.ai/errors/limit-exceeded",
  "title": "Scan limit exceeded",
  "status": 429,
  "detail": "Bạn đã dùng hết 10 lượt quét ảnh hôm nay",
  "instance": "/api/v1/scan/analyze",
  "code": "LIMIT_EXCEEDED",
  "resetAt": "2026-06-28T00:00:00Z"
}
```
Trong Flutter, parse field `code` để xử lý logic (switch theo `AppErrorCode`), dùng `detail`
để hiển thị message cho người dùng (đã là tiếng Việt do backend trả sẵn).

---

## AUTH SERVICE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/auth/register` | Đăng ký email |
| POST | `/auth/login` | Đăng nhập, trả `accessToken` + `refreshToken` |
| POST | `/auth/oauth/google` | Google OAuth |
| POST | `/auth/refresh` | Refresh token (Spring Security filter) |
| POST | `/auth/logout` | Đăng xuất, revoke refresh token |

## USER SERVICE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/users/me` | Thông tin người dùng hiện tại |
| PATCH | `/users/me` | Cập nhật profile |
| GET | `/users/me/stats` | XP, level, streak, badges |
| GET | `/users/me/plan` | Thông tin gói Premium |

## AI SERVICE (ai-service — gọi Gemini phía sau)
> Toàn bộ endpoint dưới đây do `ai-service` xử lý, microservice này gọi Gemini API,
> KHÔNG bao giờ để Flutter gọi trực tiếp Gemini.

| Method | Endpoint | Mô tả | Gemini model dùng |
|--------|----------|-------|---|
| POST | `/ai/image-recognition` | Upload ảnh, nhận diện vật thể + từ vựng | gemini-2.5-flash (vision) |
| POST | `/ai/quiz/generate` | Sinh câu hỏi quiz từ từ vựng user | gemini-2.5-flash |
| POST | `/ai/writing/check` | Kiểm tra & chấm điểm bài viết | gemini-2.5-pro |
| POST | `/ai/speaking/score` | Chấm điểm phát âm (tổng quát) | gemini-2.5-flash |
| POST | `/ai/chat/message` | Gửi tin nhắn chatbot | gemini-2.5-flash |
| POST | `/ai/roleplay/turn` | Gửi lượt thoại trong Roleplay | gemini-2.5-flash |
| POST | `/ai/learning-path/analyze` | Phân tích Placement Test, đề xuất lộ trình | gemini-2.5-flash |

### Request mẫu — Image Recognition
```
POST /ai/image-recognition
Content-Type: multipart/form-data

image: <file>
```
Response:
```json
{
  "scanId": "uuid",
  "objects": [
    {
      "label": "coffee cup",
      "confidence": 0.94,
      "ipa": "/ˈkɒfi kʌp/",
      "meaningVi": "Tách cà phê",
      "examples": ["She drinks from a coffee cup every morning."],
      "boundingBox": { "x": 120, "y": 80, "width": 200, "height": 180 }
    }
  ]
}
```

### Request mẫu — Quiz Generator
```json
POST /ai/quiz/generate
{
  "questionCount": 10,
  "types": ["multiple_choice", "fill_blank"],
  "topicId": "travel",
  "cefrLevel": "B1"
}
```

### Request mẫu — Chat message (có thể stream qua SSE)
```json
POST /ai/chat/message
{
  "conversationId": "uuid",
  "message": "What's the difference between 'make' and 'do'?"
}
```
Nếu backend dùng Server-Sent Events để stream response Gemini theo từng token,
Flutter xử lý bằng `Dio` + `ResponseType.stream`, xem ví dụ ở `rules/ai-integration.md`.

## VOCABULARY & FLASHCARD SERVICE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/vocab/topics` | Danh sách chủ đề |
| GET | `/vocab/words/{wordId}` | Chi tiết một từ |
| GET | `/decks` | Bộ thẻ của user |
| POST | `/decks/{deckId}/cards` | Thêm thẻ vào bộ |
| GET | `/srs/due-today` | Thẻ cần ôn hôm nay |
| POST | `/srs/review` | Gửi kết quả review (rating 0–3) |

## GAMIFICATION SERVICE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/gamification/stats` | XP, level, streak, coins |
| GET | `/gamification/leaderboard` | Bảng xếp hạng tuần |
| GET | `/gamification/daily-challenge` | Thử thách hôm nay |

## PAYMENT SERVICE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/payment/plans` | Danh sách gói Premium |
| POST | `/payment/orders` | Tạo đơn hàng |
| GET | `/payment/orders/history` | Lịch sử giao dịch |

---

## Common Error Codes (field `code` trong ProblemDetail)
| Code | Ý nghĩa | Xử lý phía Flutter |
|------|---------|-------|
| `UNAUTHORIZED` | Token hết hạn/sai | Gọi `/auth/refresh`, nếu fail → logout |
| `PREMIUM_REQUIRED` | Cần gói Premium | Show `PremiumGateWidget` |
| `LIMIT_EXCEEDED` | Vượt giới hạn Free | Show thông báo + nút nâng cấp |
| `AI_SERVICE_UNAVAILABLE` | Gemini API lỗi/timeout phía backend | Show "Thử lại sau", có nút Retry |
| `VALIDATION_ERROR` | Dữ liệu gửi sai (Spring `@Valid` reject) | Hiện lỗi field cụ thể từ response |
| `NOT_FOUND` | Resource không tồn tại | Show ErrorView |
| `INTERNAL_SERVER_ERROR` | Lỗi server | Show retry, log lại để báo backend team |
