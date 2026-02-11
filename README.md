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
