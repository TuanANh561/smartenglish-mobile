# API Reference — SmartEnglish AI

## Base URL
```
DEV:  http://localhost:3000/api/v1
PROD: https://api.smartenglish.ai/v1
```
Cấu hình trong `.env` → `API_BASE_URL`

## Auth Headers
```
Authorization: Bearer <access_token>
Content-Type: application/json
```

---

## AUTH
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/auth/register` | Đăng ký email |
| POST | `/auth/login` | Đăng nhập |
| POST | `/auth/oauth/google` | Google OAuth |
| POST | `/auth/oauth/apple` | Apple OAuth |
| POST | `/auth/refresh` | Refresh token |
| POST | `/auth/logout` | Đăng xuất |
| POST | `/auth/forgot-password` | Quên mật khẩu |

## USER & PROFILE
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/users/me` | Lấy thông tin người dùng hiện tại |
| PATCH | `/users/me` | Cập nhật profile |
| GET | `/users/me/stats` | XP, level, streak, badges |
| GET | `/users/me/plan` | Thông tin gói Premium |

## IMAGE SCAN
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/scan/analyze` | Upload ảnh, nhận từ vựng (multipart/form-data) |
| GET | `/scan/history` | Lịch sử ảnh đã quét |
| GET | `/scan/daily-usage` | Số lượt còn lại hôm nay (Free) |
| POST | `/scan/{scanId}/save-word` | Lưu từ vào SRS |

Request scan: `{ image: File }`
Response: `{ scanId, objects: [{ label, ipa, meaningVi, audioUrl, boundingBox, examples? }] }`

## VOCABULARY & FLASHCARD
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/vocab/topics` | Danh sách chủ đề |
| GET | `/vocab/topics/{topicId}/words` | Từ vựng theo chủ đề |
| GET | `/vocab/words/{wordId}` | Chi tiết một từ |
| GET | `/vocab/search?q=` | Tìm kiếm từ điển |
| GET | `/decks` | Danh sách bộ thẻ của user |
| POST | `/decks` | Tạo bộ thẻ mới |
| GET | `/decks/{deckId}/cards` | Thẻ trong bộ |
| POST | `/decks/{deckId}/cards` | Thêm thẻ vào bộ |
| DELETE | `/decks/{deckId}/cards/{cardId}` | Xóa thẻ |
| GET | `/srs/due-today` | Thẻ cần ôn hôm nay |
| POST | `/srs/review` | Gửi kết quả review (rating: 0-3) |

## QUIZ
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/quiz/generate` | Tạo đề quiz AI |
| GET | `/quiz/{quizId}` | Lấy đề quiz |
| POST | `/quiz/{quizId}/attempt` | Bắt đầu làm bài |
| POST | `/quiz/attempts/{attemptId}/answer` | Gửi đáp án từng câu |
| POST | `/quiz/attempts/{attemptId}/submit` | Nộp bài |
| GET | `/quiz/attempts/{attemptId}/result` | Kết quả chi tiết |
| GET | `/quiz/history` | Lịch sử làm bài |

Request generate: `{ questionCount: 10, types: ['multiple_choice','fill_blank'], topicId?, cefrLevel? }`

## SPEAKING
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/speaking/pronunciation` | Upload audio, nhận điểm phát âm |
| GET | `/speaking/roleplay/scenarios` | Danh sách kịch bản hội thoại |
| POST | `/speaking/roleplay/sessions` | Bắt đầu session roleplay |
| POST | `/speaking/roleplay/sessions/{id}/turn` | Gửi lượt thoại (audio) |
| POST | `/speaking/roleplay/sessions/{id}/end` | Kết thúc session |

Request pronunciation: `{ audio: File, wordId?, targetText, targetIpa? }` (multipart)
Response: `{ overallScore, accuracyScore, stressScore, intonationScore, phonemeErrors[], feedback[] }`

## CHATBOT & WRITING
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/chat/conversations` | Tạo cuộc trò chuyện mới |
| POST | `/chat/conversations/{id}/messages` | Gửi tin nhắn |
| GET | `/chat/conversations/{id}/messages` | Lịch sử tin nhắn |
| GET | `/chat/daily-usage` | Số tin nhắn còn lại (Free) |
| POST | `/writing/check` | Kiểm tra bài viết |
| GET | `/writing/submissions/{id}` | Lấy kết quả kiểm tra |

## READING & LISTENING
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/content/articles` | Danh sách bài đọc (filter: level, topic) |
| GET | `/content/articles/{id}` | Chi tiết bài đọc + transcript |
| GET | `/content/audio` | Danh sách bài nghe |
| GET | `/content/audio/{id}` | Chi tiết bài nghe + karaoke sync |
| POST | `/content/{id}/complete` | Đánh dấu hoàn thành bài |

## LEARNING PATH
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| POST | `/placement/submit` | Nộp bài kiểm tra đầu vào |
| GET | `/placement/result` | Kết quả & phân tích lỗ hổng |
| GET | `/learning-path/today` | Kế hoạch học hôm nay |
| GET | `/learning-path/overview` | Lộ trình tổng thể |

## GAMIFICATION
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/gamification/stats` | XP, level, streak, coins, league |
| GET | `/gamification/badges` | Danh sách badges (earned + locked) |
| GET | `/gamification/leaderboard` | Bảng xếp hạng phòng tuần này |
| GET | `/gamification/daily-challenge` | Thử thách hôm nay |
| POST | `/gamification/shop/purchase` | Mua item trong Coin Shop |
| GET | `/gamification/shop/items` | Danh sách vật phẩm cửa hàng |

## ANALYTICS
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/analytics/overview` | Tổng quan tiến độ |
| GET | `/analytics/daily-stats?days=30` | Thống kê theo ngày |
| GET | `/analytics/streak-calendar` | Dữ liệu heatmap calendar |

## PAYMENT
| Method | Endpoint | Mô tả |
|--------|----------|-------|
| GET | `/payment/plans` | Danh sách gói Premium |
| POST | `/payment/orders` | Tạo đơn hàng |
| POST | `/payment/orders/{id}/verify` | Xác nhận thanh toán |
| GET | `/payment/orders/history` | Lịch sử giao dịch |

---

## Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "LIMIT_EXCEEDED",
    "message": "Bạn đã dùng hết 10 lượt quét hôm nay",
    "details": { "resetAt": "2025-01-15T00:00:00Z" }
  }
}
```

## Common Error Codes
| Code | Ý nghĩa | Xử lý |
|------|---------|-------|
| `UNAUTHORIZED` | Token hết hạn | Refresh token tự động |
| `PREMIUM_REQUIRED` | Cần gói Premium | Show PremiumGateWidget |
| `LIMIT_EXCEEDED` | Vượt giới hạn Free | Show thông báo + nâng cấp |
| `NOT_FOUND` | Resource không tồn tại | Show ErrorView |
| `VALIDATION_ERROR` | Dữ liệu gửi sai | Show lỗi trên form |
| `SERVER_ERROR` | Lỗi server | Show retry |
