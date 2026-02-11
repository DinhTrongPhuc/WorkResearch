# WorkResearch result

**Research Task:** Research Hexagonal Architecture using controlled AI workflows.

# Hexagonal Architecture là gì?

- Là một kiểu thiết kế kiến trúc hệ thống. Tên gọi khác là Port and Adapters Architecture ( kiến trúc cổng và bộ chuyển đổi).

---

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

**===> Hexagonal được xây dựng với nguyên tắc đặt logic nghiệp vụ làm trung tâm (lõi), không phải database, điều đó biến Database, UI, Framework,... chỉ là chi tiết kỹ thuật dễ thay đổi công nghệ, dễ test, bảo trì. <===**

```
        REST API    CLI    GraphQL
             ↓       ↓       ↓
          ┌────────────────────┐
          │   BUSINESS LOGIC   │  ← Trung tâm, độc lập
          │     (Hexagon)      │
          └────────────────────┘
             ↓       ↓       ↓
        MySQL   PostgreSQL  MongoDB
```

---

# Thành Phần Hexagonal Architecture

Trong lục giác (hexagonal), mỗi cạnh chúng ta sử dụng thành phần Port(cổng) và Adapter(bộ chuyển đổi) cho mỗi đầu vào và đầu ra của ứng dụng (application).

- Port:
- Adapters:
- Application:
