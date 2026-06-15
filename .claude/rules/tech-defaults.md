# Tech Defaults — SmartEnglish AI Flutter

## Flutter & Dart version
- Flutter: 3.22+
- Dart: 3.4+
- Null safety: bắt buộc, không dùng `!` bừa bãi

## Navigation — go_router
```dart
// core/constants/routes.dart
// Tất cả route name là const String
const kRouteHome        = '/home';
const kRouteScan        = '/scan';
const kRouteFlashcard   = '/flashcard';
const kRouteFlashcardStudy = '/flashcard/study';
const kRouteQuiz        = '/quiz';
const kRouteChatbot     = '/chatbot';
const kRouteSpeaking    = '/speaking';
const kRouteReading     = '/reading';
const kRouteProfile     = '/profile';
const kRoutePremium     = '/premium';
const kRouteAdminDashboard = '/admin/dashboard';

// Navigate:
context.go(kRouteHome);
context.push(kRouteScan);
context.pop();
```

## State Management — Riverpod
```dart
// Provider đơn giản (read-only)
final topicsProvider = FutureProvider<List<Topic>>((ref) async {
  return ref.read(vocabRepositoryProvider).getTopics();
});

// Notifier cho state có thể thay đổi
@riverpod
class FlashcardNotifier extends _$FlashcardNotifier {
  @override
  AsyncValue<FlashcardState> build() => const AsyncValue.loading();

  Future<void> loadDeck(String deckId) async { ... }
  void rateCard(SrsRating rating) { ... }
}

// Dùng trong widget (ConsumerWidget hoặc HookConsumerWidget)
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

## API Client — Dio
```dart
// core/services/api_client.dart
class ApiClient {
  static const _timeout = Duration(seconds: 30);

  // GET
  Future<T> get<T>(String path, {Map<String, dynamic>? params}) async { ... }
  // POST
  Future<T> post<T>(String path, {dynamic data}) async { ... }
  // Upload file (ảnh, audio)
  Future<T> upload<T>(String path, FormData formData) async { ... }
}

// Interceptor tự động gắn Bearer token
// Interceptor tự refresh token khi 401
// Log request/response trong debug mode
```

## Model — freezed + json_serializable
```dart
@freezed
class Word with _$Word {
  const factory Word({
    required String id,
    required String word,
    String? ipaUs,
    String? ipaUk,
    required String meaningVi,
    required String partOfSpeech,
    required String cefrLevel,
    @Default([]) List<WordExample> examples,
  }) = _Word;

  factory Word.fromJson(Map<String, dynamic> json) => _$WordFromJson(json);
}
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
prefs.getString('tts_voice') ?? 'en-US';
```

## Xử lý Camera & Image
```dart
// features/image_scan — dùng image_picker
final picker = ImagePicker();
// Chụp trực tiếp
final photo = await picker.pickImage(source: ImageSource.camera, imageQuality: 85);
// Từ thư viện
final photo = await picker.pickImage(source: ImageSource.gallery);

// Upload lên API
final formData = FormData.fromMap({
  'image': await MultipartFile.fromFile(photo.path, filename: 'scan.jpg'),
});
final result = await apiClient.upload('/scan/analyze', formData);
```

## Ghi âm & Phát âm
```dart
// Ghi âm — package: record
final recorder = AudioRecorder();
await recorder.start(const RecordConfig(encoder: AudioEncoder.aacLc), path: savePath);
final path = await recorder.stop();

// Phát âm — package: audioplayers
final player = AudioPlayer();
await player.play(UrlSource(word.audioUsUrl));
await player.setPlaybackRate(0.75); // slow mode
```

## Platform-specific
```dart
// Kiểm tra platform
if (Platform.isIOS) { ... }
if (Platform.isAndroid) { ... }
if (kIsWeb) { ... }  // Web Admin

// Permissions (camera, microphone) — package: permission_handler
final status = await Permission.camera.request();
if (status.isDenied) { // show dialog hướng dẫn vào Settings }
```

## Error Handling
```dart
// Tất cả lỗi API đều map về AppException
class AppException {
  final String message;
  final int? statusCode;
  final String? code;   // 'LIMIT_EXCEEDED', 'PREMIUM_REQUIRED', ...
}

// Trong repository:
try {
  final res = await apiClient.post('/quiz/generate', data: params);
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
- [ ] Kiểm tra `mounted` sau async nếu dùng context
- [ ] Chạy `flutter analyze` — không có warning
- [ ] Test nhanh trên cả iOS simulator và Android emulator
