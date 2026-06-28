# Tech Defaults — SmartEnglish AI Flutter

## Flutter & Dart version
- Flutter: 3.22+
- Dart: 3.4+
- Null safety: bắt buộc

## Navigation — go_router
```dart
const kRouteHome        = '/home';
const kRouteScan        = '/scan';
const kRouteFlashcard   = '/flashcard';
const kRouteQuiz        = '/quiz';
const kRouteChatbot     = '/chatbot';
const kRouteSpeaking    = '/speaking';
const kRouteReading     = '/reading';
const kRouteProfile     = '/profile';
const kRoutePremium     = '/premium';

context.go(kRouteHome);
context.push(kRouteScan);
context.pop();
```

## State Management — Riverpod
```dart
final topicsProvider = FutureProvider<List<Topic>>((ref) async {
  return ref.read(vocabRepositoryProvider).getTopics();
});

@riverpod
class FlashcardNotifier extends _$FlashcardNotifier {
  @override
  AsyncValue<FlashcardState> build() => const AsyncValue.loading();

  Future<void> loadDeck(String deckId) async { ... }
  void rateCard(SrsRating rating) { ... }
}

class FlashcardPage extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final state = ref.watch(flashcardNotifierProvider);
    return state.when(
      loading: () => const LoadingOverlay(),
      error: (e, _) => ErrorView(onRetry: () => ref.invalidate(...)),
      data: (data) => FlashcardView(state: data),
    );
  }
}
```

## API Client — Dio (kết nối Spring Boot)
```dart
// core/services/api_client.dart
class ApiClient {
  static const _defaultTimeout = Duration(seconds: 30);
  static const _aiTimeout = Duration(seconds: 60); // riêng cho endpoint /ai/*

  Future<T> get<T>(String path, {Map<String, dynamic>? params}) async { ... }
  Future<T> post<T>(String path, {dynamic data, Duration? timeout}) async { ... }
  Future<T> upload<T>(String path, FormData formData, {Duration? timeout}) async { ... }
}

// Interceptor tự động gắn Bearer token (JWT từ Spring Security)
// Interceptor tự refresh token khi 401 (gọi /auth/refresh)
// Interceptor parse lỗi theo chuẩn ProblemDetail (RFC 7807) của Spring Boot
```

## Model — freezed + json_serializable (khớp DTO Spring Boot)
```dart
@freezed
class Word with _$Word {
  const factory Word({
    required String id,
    required String word,
    String? ipaUs,
    required String meaningVi,
    required String partOfSpeech,
    required String cefrLevel,
    @Default([]) List<WordExample> examples,
  }) = _Word;

  factory Word.fromJson(Map<String, dynamic> json) => _$WordFromJson(json);
}
// Lưu ý: Spring Boot (Jackson) mặc định serialize camelCase, khớp tự nhiên với Dart.
// Nếu backend dùng @JsonNaming(SnakeCaseStrategy) thì cần @JsonKey(name: 'snake_case') từng field.

// Sau khi sửa model: dart run build_runner build --delete-conflicting-outputs
```

## Local Storage
```dart
// Token & thông tin nhạy cảm → flutter_secure_storage
final storage = FlutterSecureStorage();
await storage.write(key: 'access_token', value: token);
final token = await storage.read(key: 'access_token');

// Settings không nhạy cảm → shared_preferences
final prefs = await SharedPreferences.getInstance();
prefs.setBool('dark_mode', true);
```

## Xử lý Camera & Image (cho AI Image Recognition)
```dart
final picker = ImagePicker();
final photo = await picker.pickImage(
  source: ImageSource.camera,
  imageQuality: 85,        // nén trước khi gửi để giảm tải Gemini xử lý
  maxWidth: 1600,
);

// Upload lên Spring Boot ai-service (KHÔNG gọi Gemini trực tiếp)
final formData = FormData.fromMap({
  'image': await MultipartFile.fromFile(photo.path, filename: 'scan.jpg'),
});
final result = await apiClient.upload(
  '/ai/image-recognition',
  formData,
  timeout: const Duration(seconds: 60),
);
```

## Ghi âm & Phát âm
```dart
// Ghi âm — package: record
final recorder = AudioRecorder();
await recorder.start(const RecordConfig(encoder: AudioEncoder.aacLc), path: savePath);
final path = await recorder.stop();
// Upload audio file lên /ai/speaking/score (Spring Boot xử lý, không gọi Gemini trực tiếp)

// Phát âm — package: audioplayers
final player = AudioPlayer();
await player.play(UrlSource(word.audioUsUrl));
await player.setPlaybackRate(0.75);
```

## Platform-specific
```dart
if (Platform.isIOS) { ... }
if (Platform.isAndroid) { ... }

final status = await Permission.camera.request();
if (status.isDenied) { /* show dialog hướng dẫn vào Settings */ }
```

## Error Handling (parse ProblemDetail của Spring Boot)
```dart
class AppException {
  final String message;     // lấy từ field "detail"
  final int? statusCode;    // lấy từ "status"
  final String? code;       // lấy từ field custom "code" — 'LIMIT_EXCEEDED', 'PREMIUM_REQUIRED'...

  factory AppException.fromDio(DioException e) {
    final data = e.response?.data;
    return AppException(
      message: data?['detail'] ?? 'Đã có lỗi xảy ra',
      statusCode: data?['status'],
      code: data?['code'],
    );
  }

  factory AppException.aiUnavailable() => AppException(
    message: 'AI đang quá tải, vui lòng thử lại sau ít phút',
    code: 'AI_SERVICE_UNAVAILABLE',
  );
}

// Trong repository:
try {
  final res = await apiClient.post('/ai/quiz/generate', data: params, timeout: const Duration(seconds: 60));
  return Right(QuizModel.fromJson(res));
} on DioException catch (e) {
  return Left(AppException.fromDio(e));
} catch (e) {
  return Left(AppException(message: e.toString()));
}
```

## Checklist trước khi push code
- [ ] Không còn `print()` nào
- [ ] Không hardcode màu/string
- [ ] Xử lý đủ loading/error/empty state
- [ ] Endpoint AI dùng timeout dài hơn (45–60s)
- [ ] Không có API key (Gemini hay bất kỳ) nào trong code Flutter
- [ ] Kiểm tra `mounted` sau async nếu dùng context
- [ ] Chạy `flutter analyze` — không có warning
- [ ] Test nhanh trên cả iOS simulator và Android emulator
