# Skill: Tạo Feature Mới

## Mục đích
Tạo đủ boilerplate cho 1 feature mới theo đúng cấu trúc Clean Architecture của project.

## Cách dùng
```
@skill create-feature [tên feature] [mô tả ngắn]
```

Ví dụ:
```
@skill create-feature flashcard "Màn hình học flashcard SRS, gồm deck list và study mode"
```

## Các file sẽ được tạo tự động

```
lib/features/[feature]/
├── data/
│   ├── models/
│   │   └── [feature]_model.dart         # fromJson/toJson với freezed
│   └── repositories/
│       ├── [feature]_repository.dart    # abstract interface
│       └── [feature]_repository_impl.dart
├── domain/
│   └── entities/
│       └── [feature]_entity.dart        # pure Dart object
└── presentation/
    ├── providers/
    │   └── [feature]_provider.dart      # Riverpod notifier
    ├── pages/
    │   └── [feature]_page.dart          # main page
    └── widgets/
        └── [feature]_widget.dart        # widget con chính
```

## Template nội dung từng file

### Model (data/models/)
```dart
import 'package:freezed_annotation/freezed_annotation.dart';
part '[feature]_model.freezed.dart';
part '[feature]_model.g.dart';

@freezed
class [Feature]Model with _$[Feature]Model {
  const factory [Feature]Model({
    required String id,
    // TODO: thêm fields theo API response
  }) = _[Feature]Model;

  factory [Feature]Model.fromJson(Map<String, dynamic> json)
      => _$[Feature]ModelFromJson(json);
}
```

### Repository (data/repositories/)
```dart
abstract class [Feature]Repository {
  Future<Either<AppException, List<[Feature]Model>>> getAll();
  // TODO: thêm methods theo yêu cầu
}
```

### Provider (presentation/providers/)
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

### Page (presentation/pages/)
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
        loading: () => const LoadingOverlay(),
        error: (e, _) => ErrorView(onRetry: () => ref.read([feature]NotifierProvider.notifier).load()),
        data: (data) => _buildContent(data),
      ),
    );
  }

  Widget _buildContent([Feature]State data) {
    // TODO: implement
    return const SizedBox.shrink();
  }
}
```

## Sau khi tạo file
1. Chạy: `dart run build_runner build --delete-conflicting-outputs`
2. Đăng ký route mới trong `core/constants/routes.dart`
3. Đăng ký provider trong `core/di/providers.dart` (nếu dùng DI manual)
