# Báo cáo lab Nhóm 09

**Họ tên:** Huỳnh Thái Toàn · **MSSV:** 23120175 · **JDK đã dùng:** OpenJDK 21.0.13.0.3 (Homebrew)

## Bài 1: Giá trị biên

| Ngày trễ | Hạng | Số tiền bạn điền | Cách tính |
|---:|---|---:|---|
| 24 | NORMAL | 48.000đ | 24 × 2.000đ = 48.000đ |
| 25 | NORMAL | 50.000đ | 25 × 2.000đ = 50.000đ |
| 26 | NORMAL | 50.000đ | 26 × 2.000đ = 52.000đ, áp trần 50.000đ |
| 2 | VIP | 0đ | Cả hai ngày đều thuộc thời gian ân hạn |
| 3 | VIP | 1.000đ | (3 − 2) × 1.000đ = 1.000đ |
| 51 | VIP | 49.000đ | (51 − 2) × 1.000đ = 49.000đ |
| 52 | VIP | 50.000đ | (52 − 2) × 1.000đ = 50.000đ |
| 53 | VIP | 50.000đ | (53 − 2) × 1.000đ = 51.000đ, áp trần 50.000đ |

**Vì sao chọn các ngày này mà không chọn ngày 10?**
Các ngày 24, 25 và 26 của NORMAL kiểm tra ngay dưới, đúng tại và ngay trên mốc chạm trần. Với VIP, ngày 2 là ngày ân hạn cuối cùng, ngày 3 bắt đầu tính phí, còn các ngày 51, 52 và 53 kiểm tra ba vị trí quanh mốc chạm trần. Ngày 10 nằm trong khoảng tính phí theo ngày của cả hai hạng nên không kiểm tra được hành vi tại các mốc này.

## Bài 2a: Coverage

| | Branch coverage của `LoanService` |
|---|---|
| Trước khi viết test | 4/6 nhánh (66,67%) |
| Sau khi viết 2 test | 6/6 nhánh (100%) |

**Test `daDatHanMuc` của bạn dùng `countActiveByMemberId` trả về giá trị nào? Vì sao chọn giá trị đó?**
Bản dùng để đo coverage ban đầu trả về `4L` để đi vào nhánh từ chối của điều kiện hiện có. Bản cuối trả về `3L` vì đặc tả yêu cầu từ chối ngay khi thành viên đang mượn đủ 3 cuốn. Test mượn thành công dùng `2L` để kiểm tra ngay dưới giới hạn.

## Bài 2b: Mutation testing

**Lần chạy PIT đầu tiên:** có bao nhiêu mutant sống sót (SURVIVED)? Nằm ở dòng nào, là loại mutant gì?
Có 1 trong 9 mutant sống sót, ở dòng 28 của `LoanService.java`. PIT báo `changed conditional boundary` (`ConditionalsBoundaryMutator`). Thay `> 3` bằng `>= 3` vẫn cho cùng kết quả với hai giá trị đang được kiểm tra là 2 và 4.

**Lỗi cài sẵn nằm ở đâu? Bạn phát hiện ra nó bằng cách nào?**
`LoanService.borrow` dùng `> 3` để kiểm tra hạn mức, nên thành viên đang mượn đúng 3 cuốn vẫn có thể mượn cuốn thứ tư. Sau khi xem mutant còn sống và đối chiếu đặc tả, mình chạy lại `daDatHanMuc` với `3L`. Test thất bại vì code không ném `LoanException` như đặc tả yêu cầu.

**Bạn đã sửa test và code như thế nào?**
Đổi dữ liệu của `daDatHanMuc` từ `4L` sang `3L`, kiểm tra mã `LIMIT_REACHED` và xác nhận không lưu phiếu mượn hay cập nhật sách. Test `conPhiChuaTra` dùng khoản nợ dương và số sách dưới hạn mức để kiểm tra mã `UNPAID_FINE`. Trong code, đổi `> 3` thành `>= 3`.

**Kết quả PIT sau khi sửa** (Killed bao nhiêu / tổng bao nhiêu):
PIT diệt 9/9 mutant (100%), không còn mutant sống sót. `./mvnw clean verify` chạy thành công với 19 test và branch coverage của `LoanService` vẫn đạt 6/6 nhánh.

## Câu hỏi tổng kết
**Coverage 100% có nghĩa là code không còn lỗi không? Hãy giải thích dựa trên những gì bạn thấy trong lab.**
Branch coverage 100% cho biết cả hai kết quả của mỗi điều kiện đã được chạy. Trong lab, hai giá trị 2 và 4 chạy qua cả hai nhánh kiểm tra hạn mức, nhưng vẫn bỏ qua trường hợp đúng 3 cuốn. PIT phát hiện thay đổi ở ranh giới này mà bộ test vẫn cho qua. Thêm trường hợp đúng 3 cuốn giúp kiểm tra hành vi theo đặc tả.
