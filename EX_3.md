# Bài 3: Đọc hiểu & Dò lỗi qua Prompt

## 1. Vì sao prompt thô dễ khiến AI bỏ sót lỗi?

Prompt chung chung như **"Mã này bị lỗi gì?"** dễ khiến AI bỏ sót lỗi logic vì:

- Đoạn code không sai cú pháp, vẫn biên dịch bình thường.
- Trình biên dịch Java không cảnh báo lỗi vì vòng lặp và điều kiện đều hợp lệ.
- Lỗi nằm ở logic nghiệp vụ: vòng lặp trong bắt đầu từ `j = i`, khiến phần tử tự so sánh với chính nó.
- Điều kiện `arr[i] == arr[j]` luôn đúng ngay lần đầu khi `j == i`.
- Nếu không có test case cụ thể, AI có thể chỉ nhận xét chung chung về độ phức tạp hoặc cách viết code, nhưng không phát hiện hành vi sai.

Ví dụ với mảng `{1, 2, 3, 4}`, mảng không có phần tử trùng lặp nhưng hàm vẫn trả về `1`. Đây là lỗi logic biên, không phải lỗi compile-time.

## 2. Nội dung Prompt tối ưu mới

```text
Hãy đóng vai trò là một Code Auditor chuyên đọc hiểu và dò lỗi logic trong mã Java.

Bối cảnh:
Tôi có một hàm Java dùng để tìm phần tử trùng lặp đầu tiên trong mảng số nguyên. Code không bị lỗi cú pháp, nhưng kết quả chạy sai.

Mã nguồn hiện tại:

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            for (int j = i; j < arr.length; j++) {
                if (arr[i] == arr[j]) {
                    return arr[i];
                }
            }
        }

        return null;
    }
}

Ca kiểm thử cụ thể:
Input: int[] arr = {1, 2, 3, 4}
Kết quả mong đợi: null vì không có phần tử trùng lặp.
Kết quả thực tế: hàm trả về 1.

Yêu cầu:
1. Phân tích nguyên nhân gốc rễ của lỗi logic.
2. Giải thích vì sao vòng lặp trong với j = i làm phần tử tự so sánh với chính nó.
3. Sửa lại mã nguồn bằng cách sử dụng HashSet để phát hiện phần tử trùng lặp.
4. Tối ưu độ phức tạp thời gian từ O(N^2) xuống O(N).
5. Cung cấp mã nguồn Java hoàn chỉnh trong khối code markdown.
6. Giải thích ngắn gọn bằng tiếng Việt.
```

## 3. Mã nguồn Java đã sửa sử dụng HashSet

```java
import java.util.HashSet;
import java.util.Set;

public class DuplicateFinder {

    public static Integer findDuplicate(int[] arr) {
        if (arr == null || arr.length == 0) {
            return null;
        }

        Set<Integer> seenNumbers = new HashSet<>();

        for (int number : arr) {
            if (seenNumbers.contains(number)) {
                return number;
            }

            seenNumbers.add(number);
        }

        return null;
    }
}
```

## 4. Giải thích ngắn

Cách sửa dùng `HashSet` để lưu các số đã gặp. Khi duyệt mảng, nếu một số đã tồn tại trong `seenNumbers`, nghĩa là số đó bị lặp và được trả về ngay.

Độ phức tạp thời gian trung bình là **O(N)** vì mỗi thao tác `contains` và `add` trên `HashSet` thường có độ phức tạp trung bình **O(1)**. Cách này tốt hơn vòng lặp lồng nhau ban đầu có độ phức tạp **O(N^2)**.
