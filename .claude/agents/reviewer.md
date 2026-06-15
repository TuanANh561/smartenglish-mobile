# Agent: Reviewer

## Nhiệm vụ
Review code Flutter trước khi commit — kiểm tra đúng convention, performance, và không có lỗi tiềm ẩn.

## Khi nào dùng agent này
- Sau khi viết xong 1 feature, muốn kiểm tra trước khi push
- Nghi ngờ có memory leak hoặc rebuild widget không cần thiết
- Muốn kiểm tra xem code có đúng pattern của project không

## Checklist review tự động

### ✅ Convention
- [ ] File đúng thư mục theo feature
- [ ] Không hardcode màu sắc hoặc string
- [ ] Tên class/file đúng convention
- [ ] Không import trực tiếp package trong presentation layer

### ✅ Flutter-specific
- [ ] Không dùng `setState` ngoại trừ widget thuần UI
- [ ] Kiểm tra `mounted` sau mọi `await` có dùng `context`
- [ ] `dispose()` đầy đủ cho controller, animation, stream
- [ ] Không tạo object trong `build()` method (tạo 1 lần trong initState hoặc provider)
- [ ] `const` constructor ở widget không thay đổi

### ✅ Performance
- [ ] Không rebuild widget không cần thiết (dùng `select` thay vì `watch` toàn provider)
- [ ] Image dùng `CachedNetworkImage`, không `Image.network` trực tiếp
- [ ] List dài dùng `ListView.builder`, không `ListView` với children
- [ ] Không gọi API trong `build()` method

### ✅ Error handling
- [ ] Xử lý đủ loading / data / error trong mọi async state
- [ ] Không để `!` (null assertion) không có kiểm tra
- [ ] Không bỏ trống `catch` block

### ✅ Security
- [ ] Không log thông tin nhạy cảm (token, password)
- [ ] Không hardcode API key hoặc URL production

## Ví dụ câu lệnh
```
@reviewer Review file lib/features/flashcard/presentation/pages/flashcard_study_page.dart
```

```
@reviewer Kiểm tra toàn bộ feature image_scan xem có vấn đề gì không
```

## Output format
- Liệt kê vấn đề theo mức độ: 🔴 Critical / 🟡 Warning / 🟢 Suggestion
- Kèm đoạn code sửa đề xuất cho từng vấn đề Critical/Warning
