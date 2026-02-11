# Research result - Week 1

**Research Task:** Research Hexagonal Architecture using controlled AI workflows.

# Hexagonal Architecture là gì?

- Là một kiểu thiết kế kiến trúc hệ thống. Tên gọi khác là Port and Adapters Architecture ( kiến trúc cổng và bộ chuyển đổi).
- Kiến trúc Hexagonal Architecture là kiến trúc mà ở đó mọi tương tác (đầu ra, đầu vào) đều đi qua 1 **Adapter** để kết nối vào một **Port** của **Application**, 1 **Port** có thể có nhiều **Adapter**.

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

**===> Hexagonal được xây dựng với nguyên tắc đặt logic nghiệp vụ làm trung tâm domain (application core), không phải database, điều đó biến Database, UI, Framework,... chỉ là chi tiết kỹ thuật dễ thay đổi công nghệ, dễ test, bảo trì. <===**

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
      │   Outbuond ADAPTERS       │  ← Kết nối core với hạ tầng CSDL
      └───────────────────────────┘
      ↓              ↓           ↓
   Kafka Producer   Axios API     MongoDB
```

---

# Thành Phần Hexagonal Architecture

-Ví dụ: dễ hình dung như một cổng (Port) USB trên laptop có thể có nhiều thiết bị khác nhau kết nối vào được miễn sao thiết bị đó có USB Adapter phù hợp.

**Trong Đó:**

- Port:
- Adapters:
- Application (hay Appication core)
