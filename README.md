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
- **Application (hay Appication core)**: là nơi chứa tất cả logic nghiệp vụ (Service, Domain models, Use cases,...) - được thể hiện thông qua các khái niệm như Aggregate, Entity, và Value Object.

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

**A/ Ưu Điểm - lợi thế từ việc tách biệt logic business**

1. Dễ dàng cho kiểm thử. - **testability**

- Tái sử dụng logic, test độc lập Business logic với Mock Adapter thay vì phải cài đặt, kết nối database và api thật.

2. Dễ dàng cho việc thay đổi công nghệ, bảo trì - **maintainability**

- Thay đổi công nghệ framework, đổi dịch vụ REST API sang GraphQl hoặc database Mongodb,Mysql,PostgreSQL,.. chỉ cần thay đổi hoặc tạo một Adapter mới.
- Phân chia rõ logic nghiệp vụ và chi tiết kỹ thuậ, thay đổi ở một chỗ ko ảnh hưởng chỗ khác và code dễ đọc, dễ hiểu hơn.

3. Tính linh hoạt cao - **flexability**

- Thêm tính năng mới chỉ cần thêm port và adapter.
- Thay đổi linh hoạt các adapter và port để ghi dữ liệu vào nguồn khác hoặc để kết nối với một port của application khác

**B/ Nhược Điểm**

1. **Complexity**:

- Tăng độ phức tạp cho mã nguồn, nhiều file và folder hơn, thiết kế ban đầu khó khăn hơn. Phức tạp với người mới.

2. **Running Locally**:

- Một ứng dụng với nhiều thành phần application, nhiều module chạy độc lập, nhièu bộ chuyển đổi quá trình biên dịch, chạy thử nghiệm, xây dựng tất cả các mô-đun cùng nhau và khởi động toàn bộ dự án sẽ mất rất nhiều thời gian.

3. **Performance**:

- Một ứng dụng với nhiều 'Hexagonal' liên kết với nhau khi request gây ra độ trễ lớn đặc biệt với các 'Hexagonal' liên kết bằng API.

# Khi nào Áp dụng Hexagonal Architecture

=> Tóm gọn: tùy thuộc cốt lõi vào quy mô và độ trưởng thành của dự án.

=> Cụ Thể:

**Nên**: Trường hợp - tình huống

- Ứng dụng lớn nhiều đầu vào, đầu ra - kết nối với dịch vụ bên ngoài hoặc nhiều nguồn dữ liệu: Sàn thương mại điện tử, ứng dụng ngân hàng,...

- Ứng dụng đa nền tảng - cùng nghiệp vụ nhiều cách truy cập khác nhau: Web, mobile, desktop,...

- Ứng dụng phát triển lâu dài - công nghệ thay đổi theo thời gian, cần bảo trì và mở rộng liên tục: Hệ thống quản lý doanh nghiệp, trường học,...

- Hệ Thống microserver - hệ thống nhiều service nhỏ,hoạt động độc lập cần ranh giới rõ ràng.

**Không nên || cân nhắc kỹ**:

- Ứng dụng nhỏ với CRUD cơ bản.

- Ứng dụng ngắn hạn

- Ứng dụng đã hoàn thiện tương đối từ lâu nhưng ko triển khai kiến trúc lục giác.

# Các mẫu kiến trúc thay thế Hexagonal

- **Ngữ cảnh**: dự án phát triển ứng dụng lớn, lâu dài, cần khả năng bảo trì và kiểm thử cao, phát triển đa nền tảng.

=> Phù hợp:

- Hexagonal Architecture
- Clean Architecture
- Onion Architecture
- Vertical Slice Architecture

=> Ma Trận So Sánh 4 Kiến Trúc Phần Mềm:

| Tiêu Chí                               | Hexagonal Architecture                                                                                     | Clean Architecture                                                                                 | Onion Architecture                                                                                  | Vertical Slice Architecture                                                       |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| **Cấu trúc tổ chức**                   | 3 thành phần: Application Core (Hexagon), Ports (Interfaces), Adapters (Implementations)                   | 4 tầng đồng tâm: Entities → Use Cases → Interface Adapters → Frameworks & Drivers                  | 4 tầng đồng tâm: Domain Model → Domain Services → Application Services → Infrastructure             | Tổ chức theo features/slices, mỗi slice chứa tất cả logic từ UI đến database      |
| **Phụ thuộc (dependence)**             | Từ ngoài vào trong: Adapters phụ thuộc Ports, Ports phụ thuộc Core                                         | Từ ngoài vào trong: Mỗi tầng chỉ phụ thuộc vào tầng bên trong                                      | Từ ngoài vào trong: Infrastructure phụ thuộc Domain Model                                           | Không có phụ thuộc giữa các slices, mỗi slice độc lập                             |
| **Trọng tâm thiết kế**                 | Tập trung vào **tách biệt business logic khỏi external systems** thông qua ports và adapters               | Tập trung vào **tách biệt Use Cases và Entities**, framework independence                          | Tập trung vào **Domain Model là trung tâm**, toàn bộ xoay quanh domain                              | Tập trung vào **features/use cases cụ thể**, mỗi feature là một đơn vị hoàn chỉnh |
| **Business Logic**                     | Nằm trong Hexagon (Core), chứa domain entities và application services                                     | Tách thành 2 tầng: Entities (enterprise rules) và Use Cases (application rules)                    | Nằm ở Domain Model (innermost layer), có thể có Domain Services bổ sung                             | Nằm trong mỗi feature slice, không tách biệt thành layer riêng                    |
| **Cơ chế tách phụ thuộc công nghệ**    | Sử dụng **Ports (interfaces)** để định nghĩa contracts, Adapters implement ports cho từng công nghệ cụ thể | Sử dụng **Dependency Inversion Principle**, tầng trong định nghĩa interfaces, tầng ngoài implement | Sử dụng **abstraction ở mỗi tầng**, Infrastructure layer implement interfaces từ Application/Domain | Mỗi slice tự định nghĩa dependencies riêng, không share abstractions giữa slices  |
| **Tính linh hoạt (flexibility)**       | Rất cao - Dễ dàng thay đổi hoặc thêm adapters mới mà không ảnh hưởng core logic                            | Cao - Framework-independent, dễ thay đổi công nghệ bên ngoài                                       | Cao - Domain-centric, dễ thay đổi infrastructure                                                    | Trung bình - Linh hoạt trong mỗi slice nhưng khó share/reuse code giữa slices     |
| **Khả năng kiểm thử (testability)**    | Rất cao - Core logic test hoàn toàn độc lập bằng mock adapters                                             | Rất cao - Mỗi layer test độc lập, Use Cases test mà không cần frameworks                           | Rất cao - Domain Model test thuần túy không phụ thuộc infrastructure                                | Cao - Mỗi slice test như một unit hoàn chỉnh, thường test theo kiểu integration   |
| **Khả năng bảo trì (maintainability)** | Cao - Ranh giới rõ ràng giữa business logic và technical concerns                                          | Rất cao - Structure chặt chẽ, dễ navigate code                                                     | Cao - Domain model ổn định, thay đổi infrastructure không ảnh hưởng domain                          | Trung bình - Dễ maintain từng feature nhưng có thể bị duplicate logic             |
| **Độ phức tạp (complexity)**           | Cao - Cần hiểu ports/adapters pattern, dependency inversion                                                | Rất cao - 4 tầng với nhiều rules, steep learning curve                                             | Rất cao - Cần hiểu DDD concepts, domain modeling                                                    | Trung bình - Đơn giản hơn các kiến trúc khác nhưng thiếu structure tổng thể       |
| **Tái sử dụng (code reuse)**           | Cao - Shared ports và adapters có thể dùng lại                                                             | Cao - Entities và Use Cases được share                                                             | Cao - Domain Model và Domain Services được share                                                    | Thấp - Mỗi slice tự lo, ít share code giữa slices                                 |
| **Ưu điểm**                            | Tối đa flexibility với external dependencies, dễ swap implementations                                      | Balance tốt nhất, structure rõ ràng, tài liệu phong phú                                            | Domain model mạnh mẽ, separation of concerns tốt nhất                                               | Development speed nhanh nhất, low coupling giữa features                          |
| **Nhược điểm**                         | Có thể phức tạp cho app đơn giản, nhiều abstractions                                                       | Nhiều boilerplate, over-engineering cho app nhỏ                                                    | Learning curve dốc nhất, cần DDD expertise                                                          | Code duplication, khó enforce consistency, thiếu shared patterns                  |
| **Khi nào dùng**                       | Khi có nhiều external integrations, cần thay đổi công nghệ thường xuyên                                    | Khi cần structure chuẩn mực cho enterprise app dài hạn                                             | Khi domain complexity là core challenge, có domain experts                                          | Khi speed to market quan trọng nhất, team nhỏ, requirements thay đổi nhanh        |
| **Khi nào KHÔNG dùng**                 | CRUD đơn giản, prototype cần nhanh                                                                         | Startup MVP, team thiếu kinh nghiệm                                                                | App domain đơn giản, không có business complexity                                                   | Enterprise app cần strict governance, team lớn cần consistency                    |

---

- Cấu trúc code:

```
HEXAGONAL:                    CLEAN:
/core                         /entities
/ports                        /use-cases
/adapters                     /adapters
  /driving (UI, API)            /frameworks
  /driven (DB, Email)

ONION:                        VERTICAL SLICE:
/domain                       /features
  /model                        /create-order (all logic)
  /services                     /cancel-order (all logic)
/application                    /update-customer (all logic)
/infrastructure
```
