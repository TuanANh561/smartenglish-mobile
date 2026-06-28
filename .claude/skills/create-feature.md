# Skill: Tạo Feature Mới

## Mục đích
Tạo đủ boilerplate cho 1 feature mới theo đúng cấu trúc Clean Architecture, gọi API
Spring Boot đúng convention (có thể là endpoint AI hoặc endpoint thường).

## Cách dùng
```
@skill create-feature [tên feature] [mô tả ngắn] [is_ai_feature: true/false]
```

Ví dụ:
```
@skill create-feature flashcard "Màn hình học flashcard SRS" is_ai_feature:false
@skill create-feature writing_checker "Kiểm tra bài viết qua AI" is_ai_feature:true
```

## Các file sẽ được tạo tự động

```
lib/features/[feature]/
├── data/
│   ├── models/
│   │   └── [feature]_model.dart         # fromJson/toJson — khớp DTO Spring Boot
│   └── repositories/
│       ├── [feature]_repository.dart
│       └── [feature]_repository_impl.dart
├── domain/
│   └── entities/
│       └── [feature]_entity.dart
└── presentation/
    ├── providers/
    │   └── [feature]_provider.dart
    ├── pages/
    │   └── [feature]_page.dart
    └── widgets/
        └── [feature]_widget.dart
```

## Template Repository — nếu `is_ai_feature: true`
```dart
abstract class [Feature]Repository {
  Future<Either<AppException, [Feature]Result>> process([Feature]Request request);
}

class [Feature]RepositoryImpl implements [Feature]Repository {
  final ApiClient _apiClient;

  @override
  Future<Either<AppException, [Feature]Result>> process([Feature]Request request) async {
    try {
      // Gọi endpoint /ai/... của Spring Boot — KHÔNG gọi Gemini trực tiếp
      final response = await _apiClient.post(
        '/ai/[feature]',
        data: request.toJson(),
        timeout: const Duration(seconds: 60), // timeout dài cho AI
      );
      return Right([Feature]Result.fromJson(response));
    } on DioException catch (e) {
      if (e.response?.data?['code'] == 'AI_SERVICE_UNAVAILABLE') {
        return Left(AppException.aiUnavailable());
      }
      return Left(AppException.fromDio(e));
    }
  }
}
```

## Template Repository — nếu `is_ai_feature: false`
```dart
abstract class [Feature]Repository {
  Future<Either<AppException, List<[Feature]Model>>> getAll();
}

class [Feature]RepositoryImpl implements [Feature]Repository {
  final ApiClient _apiClient;

  @override
  Future<Either<AppException, List<[Feature]Model>>> getAll() async {
    try {
      final response = await _apiClient.get('/[feature]');
      return Right((response as List).map((e) => [Feature]Model.fromJson(e)).toList());
    } on DioException catch (e) {
      return Left(AppException.fromDio(e));
    }
  }
}
```

## Provider (presentation/providers/)
```dart
@riverpod
class [Feature]Notifier extends _$[Feature]Notifier {
  @override
  AsyncValue<[Feature]State> build() => const AsyncValue.loading();

  Future<void> load() async {
    state = const AsyncValue.loading();
    final result = await ref.read([feature]RepositoryProvider).getAll();
    state = result.fold(
      (error) => AsyncValue.error(error, StackTrace.current),
      (data) => AsyncValue.data([Feature]State(items: data)),
    );
  }
}
```

## Page (presentation/pages/)
```dart
class [Feature]Page extends ConsumerStatefulWidget {
  const [Feature]Page({super.key});

  @override
  ConsumerState<[Feature]Page> createState() => _[Feature]PageState();
}

class _[Feature]PageState extends ConsumerState<[Feature]Page> {
  @override
  void initState() {
    super.initState();
    Future.microtask(() => ref.read([feature]NotifierProvider.notifier).load());
  }

  @override
  Widget build(BuildContext context) {
    final state = ref.watch([feature]NotifierProvider);
    return Scaffold(
      appBar: AppBar(title: const Text('[Feature Title]')),
      body: state.when(
        loading: () => const LoadingOverlay(),  // dùng AiLoadingIndicator nếu is_ai_feature
        error: (e, _) => ErrorView(onRetry: () => ref.read([feature]NotifierProvider.notifier).load()),
        data: (data) => _buildContent(data),
      ),
    );
  }

  Widget _buildContent([Feature]State data) {
    return const SizedBox.shrink();
  }
}
```

## Sau khi tạo file
1. Chạy: `dart run build_runner build --delete-conflicting-outputs`
2. Đăng ký route mới trong `core/constants/routes.dart`
3. Xác nhận với backend dev đúng endpoint path và format response trước khi tích hợp thật
