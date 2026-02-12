# Research result - Week 1

**Research Task:** Research Hexagonal Architecture using controlled AI workflows.

# Hexagonal Architecture là gì?

- Là một kiểu thiết kế kiến trúc hệ thống. Tên gọi khác là Port and Adapters Architecture ( kiến trúc cổng và bộ chuyển đổi).
- Kiến trúc Hexagonal Architecture là kiến trúc mà ở đó mọi tương tác (đầu ra, đầu vào) đều đi qua 1 **Adapter** để kết nối vào một **Port** của **Application**, 1 **Port** có thể có nhiều **Adapter**.
- **Ví dụ**: dễ hình dung như một cổng (Port) USB trên laptop có thể có nhiều thiết bị khác nhau kết nối vào được miễn sao thiết bị đó có USB Adapter phù hợp.

  ***

- **Lý do ra đời**: cải tiến từ Layered Architecture (Kiến trúc phân tầng truyền thống) giải quyết vấn đề của mẫu kiến trúc này.

```
Layered Architecture:
UI Layer
   ↓
Business Layer
   ↓
Data Access Layer
   ↓
DATABASE (Ở dưới cùng, điều khiển mọi thứ)
```

**Hệ quả:** thiết kế xoay quanh database -> logic nghiệp vụ (business) phụ thuộc vào cấu trúc -> thay đổi database sẽ dẫn tới phải sửa nhiều tầng cấu trúc (tầng trên phụ thuộc tầng dưới) -> Không thể test Business Layer mà không có Database, khó thay đổi công nghệ.

**===> Hexagonal Architecture tập trung vào Application Core thay vì Database hay UI Framework. Business logic được tách biệt khỏi các chi tiết kỹ thuật thông qua Ports & Adapters, giúp hệ thống dễ test, dễ bảo trì và dễ thay đổi công nghệ <===** _ Nguyên tắc cốt lõi của kiến trúc _

```
     REST API    CLI    GraphQL
          ↓       ↓       ↓
  ┌─────────────────────────┐
  │   Inbound ADAPTERS      │  ← chuyển request(ngoài) vào core
  └───────────┬─────────────┘
              ↓
      ┌──────────────┐
      │Inbound PORTS │
      │  (Interfaces)│
      └──────┬───────┘
             ↓
   ┌────────────────────┐
   │   BUSINESS LOGIC   │  ← Trung tâm, độc lập
   │   (Application)    │
   └────────────────────┘
             ↓
      ┌──────────────┐
      │Outbound PORTS│
      │  (Interfaces)│
      └──────┬───────┘
             ↓
      ┌───────────────────────────┐
      │   Outbound ADAPTERS       │  ← Kết nối core với hạ tầng
      └───────────────────────────┘
      ↓              ↓           ↓
   Kafka Producer   Axios API     MongoDB
```

---

# Thành Phần Hexagonal Architecture

- **Port**: giống như một lối vào (entry point), nhiệm vụ của nó là định nghĩa một **Interface** (contract) chuẩn để các tác nhân bên ngoài có thể giao tiếp được với **application**, mà ko cần quan tâm xem cái gì sẽ triển khai (implement).
- **Adapters**: là nơi triển khai công nghệ (MongoDB, Firebase, PostgreSQL, REST API controller,... ) tuân theo quy tắc vào cửa của **Port** (implement interface) để được tương tác với **application**.  
  -> có thể tạo ra bao nhiêu Adapter cũng được cho cùng một Port.
- **Application (hay Appication core)**: là nơi chứa tất cả logic nghiệp vụ (Service, Domain models,Use cases,...) - được thể hiện thông qua các khái niệm như Aggregate, Entity, và Value Object.

# Sample:

- PORT:

```
// NotificationPort.ts
export interface NotificationPort {
  send(message: string): void;
}
```

- Adapter (Email)

```
// EmailAdapter.ts
import { NotificationPort } from "./NotificationPort";

export class EmailAdapter implements NotificationPort {
  send(message: string): void {
    console.log("📧 Send EMAIL:", message);
  }
}
```

- Adapter (SMS)

```
// SmsAdapter.ts
import { NotificationPort } from "./NotificationPort";

export class SmsAdapter implements NotificationPort {
  send(message: string): void {
    console.log("📱 Send SMS:", message);
  }
}
```

- Application

```
import { NotificationPort } from "./NotificationPort";

export class App {
  constructor(private notifier: NotificationPort) {}

  run() {
    this.notifier.send("Hello world!");
  }
}
```

### Main:

```
import { App } from "./App";
import { EmailAdapter } from "./EmailAdapter";
// import { SmsAdapter } from "./SmsAdapter";

const notifier = new EmailAdapter(); // đổi adapter sang SmsAdapter
const app = new App(notifier);

app.run();
```

### Kết quả:

- Email Adapter: 📧 Send EMAIL: Hello world!
- SMS Adapter: 📱 Send SMS: Hello world!

# Pros and Cons (ưu nhược điểm của hexagonal architecture)

A/ Ưu Điểm - lợi thế từ việc tách biệt logic business

1. Dễ dàng cho kiểm thử. - **testability**

- Tái sử dụng logic, test độc lập Business logic với Mock Adapter thay vì phải cài đặt, kết nối database và api thật.

2. Dễ dàng cho việc thay đổi công nghệ, bảo trì - **maintainability**

- Thay đổi công nghệ framework, đổi dịch vụ REST API sang GraphQl hoặc database Mongodb,Mysql,PostgreSQL,.. chỉ cần thay đổi hoặc tạo một Adapter mới.
- Phân chia rõ logic nghiệp vụ và chi tiết kỹ thuậ, thay đổi ở một chỗ ko ảnh hưởng chỗ khác và code dễ đọc, dễ hiểu hơn.

3. Tính linh hoạt cao - **flexability**

- Thêm tính năng mới chỉ cần thêm port và adapter.
- Thay đổi linh hoạt các adapter và port để ghi dữ liệu vào nguồn khác hoặc để kết nối với một port của application khác

B/ Nhược Điểm

1. **Complexity**:

- Tăng độ phức tạp cho mã nguồn, nhiều file và folder hơn, thiết kế ban đầu khó khăn hơn. Phức tạp với người mới.

2. **Running Locally**:

- Một ứng dụng với nhiều thành phần application chạy độc lập, gây khó khăn khi chạy local.

3. **Performance**:

- Một ứng dụng với nhiều 'Hexagonal' liên kết với nhau khi request gây ra độ trễ lớn đặc biệt với các API liên kết giữa các 'Hexagonal'.

# Khi nào Áp dụng Hexagonal Architecture

=> Tóm gọn: tùy thuộc cốt lõi vào quy mô và độ trưởng thành của dự án.

=> Cụ Thể:

**Nên**

- Ứng dụng lớn nhiều đầu vào, đầu ra - kết nối với dịch vụ bên ngoài hoặc nhiều nguồn dữ liệu: Sàn thương mại điện tử, ứng dụng ngân hàng,...

- Ứng dụng đa nền tảng - cùng nghiệp vụ nhiều cách truy cập khác nhau: Web, mobile, desktop,...

- Ứng dụng phát triển lâu dài - công nghệ thay đổi theo thời gian, cần bảo trì và mở rộng liên tục: Hệ thống quản lý doanh nghiệp, trường học,...

- Hệ Thống microserver - hệ thống nhiều service nhỏ,hoạt động độc lập cần ranh giới rõ ràng.

**Không nên || cân nhắc kỹ**

- Ứng dụng nhỏ với CRUD cơ bản.

- Ứng dụng ngắn hạn

- Ứng dụng đã hoàn thiện tương đối từ lâu nhưng ko triển khai kiến trúc lục giác.
