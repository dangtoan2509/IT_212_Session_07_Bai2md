## Bài 2: Tối ưu Prompt và Sửa lỗi NullPointerException

### Prompt sau khi tối ưu
```
Bạn là một chuyên gia gỡ lỗi Java (Java Debugger).

Ngữ cảnh:
- Mã nguồn gây lỗi nằm trong lớp `PaymentProcessor`.
- Stack Trace thực tế:
  Exception in thread "main" java.lang.NullPointerException: Cannot invoke "java.util.Map.get(Object)" because "this.exchangeRates" is null
      at PaymentProcessor.processPayment(PaymentProcessor.java:7)
      at Main.main(Main.java:8)

Yêu cầu:
- Phân tích nguyên nhân gốc rễ (root cause) của lỗi NullPointerException.
- Đề xuất sửa chữa an toàn nhất cho `PaymentProcessor` bằng cách khởi tạo `exchangeRates` với `ConcurrentHashMap`.
- Giữ nguyên tính đóng gói (encapsulation) của OOP và không thay đổi hành vi bên ngoài lớp.
- Bảo đảm rằng `processPayment` không bị gọi trên `exchangeRates` null và rằng `currency` null/rỗng hoặc tỷ giá không tồn tại được xử lý rõ ràng.

Kết quả mong muốn:
- Mã nguồn Java sửa hoàn chỉnh.
- Giải thích ngắn gọn vì sao lỗi xảy ra và vì sao cách sửa này an toàn.
```

### Mã nguồn Java đã sửa
```java
import java.util.Map;
import java.util.Objects;
import java.util.concurrent.ConcurrentHashMap;

public class PaymentProcessor {

    private final Map<String, Double> exchangeRates;

    public PaymentProcessor() {
        this.exchangeRates = new ConcurrentHashMap<>();
    }

    public PaymentProcessor(Map<String, Double> exchangeRates) {
        this.exchangeRates = new ConcurrentHashMap<>(
                Objects.requireNonNull(exchangeRates, "exchangeRates không được null"));
    }

    public double processPayment(String currency, double amount) {
        if (currency == null || currency.isBlank()) {
            throw new IllegalArgumentException("currency không được null hoặc rỗng.");
        }

        Double rate = exchangeRates.get(currency);
        if (rate == null) {
            throw new IllegalStateException("Không tìm thấy tỷ giá cho currency: " + currency);
        }

        return amount * rate;
    }
}
```

### Giải thích lỗi
- Nguyên nhân gốc rễ là trường `exchangeRates` chưa được khởi tạo nên vẫn là `null`.
- Khi `processPayment` gọi `exchangeRates.get(currency)`, Java ném `NullPointerException` vì đang gọi phương thức trên biến null.
- Sửa bằng `ConcurrentHashMap` khởi tạo trong constructor đảm bảo `exchangeRates` luôn có giá trị hợp lệ.
- Đồng thời, thêm kiểm tra `currency` null/rỗng và kiểm tra `rate == null` giúp tránh lỗi không rõ ràng sau này.
