# DANH SÁCH CÂU HỎI BẢO VỆ ĐỒ ÁN - HỆ THỐNG E-DENGUE

> **Hệ thống quản lý và giám sát dịch sốt xuất huyết Dengue**
>
> - **Frontend:** React 18 + TypeScript + MobX + Ant Design + Vite
> - **Backend:** Java 17 + Spring Boot 3 + JHipster 8 + MongoDB + JWT
> - **File Storage:** SeaweedFS | **Cache:** Redis (Redisson)

---

## Mục lục

- [I. Câu hỏi tổng quan về dự án](#i-câu-hỏi-tổng-quan-về-dự-án)
- [II. Câu hỏi về kiến trúc hệ thống](#ii-câu-hỏi-về-kiến-trúc-hệ-thống)
- [III. Câu hỏi về Frontend](#iii-câu-hỏi-về-frontend)
- [IV. Câu hỏi về Backend](#iv-câu-hỏi-về-backend)
- [V. Câu hỏi về thiết kế CSDL](#v-câu-hỏi-về-thiết-kế-csdl)
- [VI. Câu hỏi về bảo mật](#vi-câu-hỏi-về-bảo-mật)
- [VII. Câu hỏi về hiệu năng & triển khai](#vii-câu-hỏi-về-hiệu-năng--triển-khai)
- [VIII. Câu hỏi về Design Pattern](#viii-câu-hỏi-về-design-pattern)
- [IX. Câu hỏi kỹ thuật nâng cao](#ix-câu-hỏi-kỹ-thuật-nâng-cao)
- [X. Câu hỏi phản biện](#x-câu-hỏi-phản-biện)

---

## I. Câu hỏi tổng quan về dự án

### Câu 1: Đồ án của em giải quyết vấn đề gì? Tại sao chọn đề tài này?

**Trả lời:** Đồ án xây dựng hệ thống **E-Dengue** - quản lý và giám sát dịch sốt xuất huyết Dengue. Hệ thống giúp cơ quan y tế theo dõi ca bệnh, ổ dịch, phân tích xu hướng dịch bệnh, đánh giá nguy cơ và cảnh báo sớm. Đề tài được chọn vì sốt xuất huyết là vấn đề y tế công cộng nghiêm trọng ở Việt Nam, cần có công cụ số hóa để quản lý hiệu quả.

---

### Câu 2: Hệ thống có những chức năng chính nào?

**Trả lời:**

- **Quản lý ca bệnh (Detail Case):** Nhập, cập nhật, theo dõi từng ca sốt xuất huyết
- **Quản lý ổ dịch (Outbreak):** Theo dõi, báo cáo các ổ dịch
- **Giám sát vector truyền bệnh (Infection Vector):** Theo dõi muỗi, bọ gậy
- **Dashboard:** Biểu đồ thống kê, xu hướng dịch bệnh
- **Đánh giá nguy cơ (Risk Assessment):** Bộ câu hỏi đánh giá nguy cơ dịch bệnh
- **Cảnh báo sớm (Early Forecast):** Dự báo xu hướng dịch
- **Quản lý bài viết (Post):** Tin tức, thông báo phòng chống dịch
- **Quản lý master data:** Tỉnh/thành, quận/huyện, phường/xã, thôn/ấp
- **Quản lý người dùng & phân quyền:** Hệ thống RBAC

---

### Câu 3: Ai là người sử dụng hệ thống?

**Trả lời:** Hệ thống phục vụ nhiều đối tượng:

- **Cán bộ y tế:** Nhập liệu ca bệnh, ổ dịch, báo cáo
- **Quản lý y tế cấp huyện/tỉnh:** Xem thống kê, dashboard, báo cáo tổng hợp
- **Admin hệ thống:** Quản lý người dùng, phân quyền, cấu hình
- **Người dân:** Truy cập Landing Page để đọc tin tức, đánh giá nguy cơ

---

## II. Câu hỏi về kiến trúc hệ thống

### Câu 4: Kiến trúc tổng thể của hệ thống là gì?

**Trả lời:** Hệ thống sử dụng kiến trúc **Client-Server** với mô hình **SPA (Single Page Application)**:

- **Frontend:** React 18 (SPA) chạy trên Vite, giao tiếp với backend qua REST API
- **Backend:** Spring Boot 3 (Java 17) cung cấp REST API, được tạo bằng JHipster 8
- **Database:** MongoDB - cơ sở dữ liệu NoSQL
- **File Storage:** SeaweedFS - lưu trữ file phân tán
- **Cache:** Redis (Redisson) cho caching và token blacklist

---

### Câu 5: Tại sao chọn MongoDB thay vì SQL truyền thống?

**Trả lời:**

- Dữ liệu dịch tễ có cấu trúc **linh hoạt**, các ca bệnh có thể có thuộc tính khác nhau → phù hợp với document model
- MongoDB hỗ trợ **GeoJSON** native cho dữ liệu tọa độ địa lý (theo dõi vị trí ca bệnh, ổ dịch trên bản đồ)
- **Nested documents** giúp lưu trữ thông tin phức tạp (địa chỉ nhiều cấp: tỉnh/huyện/xã/thôn) mà không cần JOIN nhiều bảng
- **Schema flexibility** phù hợp khi yêu cầu nghiệp vụ thay đổi thường xuyên
- Hiệu năng tốt cho **read-heavy workload** (dashboard, báo cáo)

---

### Câu 6: Tại sao chọn React mà không phải Vue hay Angular?

**Trả lời:**

- React có **hệ sinh thái lớn nhất**, nhiều thư viện hỗ trợ (Ant Design, Recharts, OpenLayers...)
- **Component-based architecture** giúp tái sử dụng code hiệu quả
- **Virtual DOM** cho hiệu năng render tốt
- Cộng đồng lớn, dễ tìm tài liệu và hỗ trợ
- TypeScript integration tốt, giúp phát hiện lỗi sớm

---

### Câu 7: Tại sao dùng MobX thay vì Redux?

**Trả lời:**

- MobX có **cú pháp đơn giản hơn** Redux, ít boilerplate code
- **Reactive programming model** - tự động cập nhật UI khi state thay đổi thông qua `observable` và `observer`
- Dự án có **50+ store** - MobX cho phép tổ chức store theo module dễ dàng hơn
- Hiệu năng tốt nhờ **fine-grained reactivity** (chỉ render lại component thực sự thay đổi)
- Phù hợp với dự án CRUD-heavy như E-Dengue

---

## III. Câu hỏi về Frontend

### Câu 8: Cấu trúc thư mục frontend được tổ chức như thế nào?

**Trả lời:** Tổ chức theo **feature-based structure**:

```
frontend/src/
├── core/          → Code framework dùng chung (base classes, hooks, layout, utils)
├── pages/         → Các trang theo chức năng (Dashboard, EpidemicMonitoring, MasterData...)
├── components/    → Components tái sử dụng (CRUD, forms, table, chart, map...)
├── stores/        → MobX stores theo module (50+ stores)
├── api/           → API service được auto-generate từ Swagger
├── Router/        → Cấu hình routing (appRouters, authRouter, landingRoutes)
├── translations/  → File đa ngôn ngữ i18n (JSON)
├── App.tsx        → Root component
└── index.tsx      → Entry point
```

---

### Câu 9: Giải thích cơ chế lazy loading và code splitting trong dự án?

**Trả lời:** Sử dụng `React.lazy()` kết hợp `Suspense` để lazy load các page component:

```typescript
lazyComponent: lazy(() => import('@pages/Dashboard'))
```

- Mỗi trang chỉ được tải khi user navigate đến → **giảm bundle size ban đầu**
- Vite tự động tạo **separate chunk** cho mỗi lazy-loaded component
- Cấu hình output file naming với hash để hỗ trợ **cache busting**: `assets/[name].[hash].js`

---

### Câu 10: CommonListStore là gì và hoạt động ra sao?

**Trả lời:** `CommonListStore` là **base class cho tất cả store quản lý danh sách** trong dự án, cung cấp:

- `searchDataState` - State lưu trữ filter/search
- `currentPageResult` - Kết quả phân trang (items + totalCount)
- `createOrUpdateModal` - State quản lý modal tạo/sửa
- Actions: `refreshData()`, `openModal()`, `closeModal()`, `removeEntity()`

Tất cả **50+ store** đều extend từ class này → đảm bảo **consistency** và **giảm code trùng lặp**.

---

### Câu 11: Hệ thống xử lý authentication ở frontend như thế nào?

**Trả lời:**

1. User đăng nhập → gọi API `/api/auth-plugin/auth/login` với username/password
2. Server trả về `accessToken` + `refreshToken` (JWT)
3. Token được lưu vào `localStorage`
4. Mỗi request API, Axios interceptor tự động gắn `Authorization: Bearer {token}` vào header
5. Khi token hết hạn (401), interceptor tự động gọi **refresh token** để lấy token mới
6. Nếu refresh token cũng hết hạn → redirect về trang login
7. Hỗ trợ **CAPTCHA** sau N lần đăng nhập thất bại (đếm bằng Dexie.js/IndexedDB)

---

### Câu 12: Phân quyền ở frontend hoạt động thế nào?

**Trả lời:**

- Khi bootstrap app, gọi API lấy thông tin session gồm `listPermission` của user
- Mỗi route có thuộc tính `permission` (ví dụ: `epidemicMonitoring.dengueCase.GetPaged`)
- Component `ProtectedRoute` kiểm tra quyền trước khi render trang
- Nếu user không có quyền → hiển thị trang "Không có quyền truy cập"
- Các button/action cũng được ẩn/hiện dựa trên permission

---

### Câu 13: Tại sao dùng cả Ant Design lẫn Tailwind CSS?

**Trả lời:**

- **Ant Design** cung cấp bộ **UI component** hoàn chỉnh (Form, Table, Modal, DatePicker...) → tiết kiệm thời gian phát triển
- **Tailwind CSS** dùng cho **custom layout** và **utility styling** nhanh → không cần viết CSS riêng cho mỗi trường hợp
- Kết hợp hai framework giúp vừa có component sẵn, vừa linh hoạt trong styling

---

### Câu 14: Giải thích cách generate API service tự động?

**Trả lời:** Sử dụng thư viện `swagger-axios-codegen`:

- Backend expose **Swagger/OpenAPI spec**
- Chạy script `yarn gen-api-java` → đọc spec và sinh ra TypeScript service classes
- Mỗi API endpoint được sinh thành method tương ứng với đúng type input/output
- Lưu tại `src/api/` → import và sử dụng trực tiếp trong store/component
- **Lợi ích:** type-safe, đồng bộ với backend, giảm lỗi do gõ sai URL/param

---

### Câu 15: Dự án hỗ trợ đa ngôn ngữ (i18n) như thế nào?

**Trả lời:**

- Sử dụng `i18next` + `react-i18next`
- File dịch JSON lưu tại `src/translations/{lang}/{namespace}.json`
- Trong component sử dụng hook `useTranslation()` hoặc component `<Trans>`
- Header `Accept-Language` được gắn vào mỗi request API để backend trả về message đúng ngôn ngữ
- Hỗ trợ chuyển đổi ngôn ngữ runtime qua custom event

---

### Câu 16: Bản đồ (Map) trong hệ thống được hiện thực thế nào?

**Trả lời:**

- Sử dụng thư viện **OpenLayers (ol)** v10 - thư viện bản đồ GIS mã nguồn mở
- Hiển thị vị trí ca bệnh, ổ dịch trên bản đồ Việt Nam
- Dữ liệu tọa độ lưu dạng **GeoJSON** trong MongoDB (`GeoJsonPoint`, `GeoJsonPolygon`)
- Hỗ trợ zoom, pan, cluster các điểm gần nhau
- Tích hợp trong các trang giám sát dịch bệnh

---

## IV. Câu hỏi về Backend

### Câu 17: Cấu trúc backend được tổ chức thế nào?

**Trả lời:** Tổ chức theo **modular layered architecture**:

```
backend/src/main/java/com/ord/
├── core/                → Framework dùng chung (base CRUD, DTO, exception, utils...)
├── modules/
│   ├── admin/           → Module quản trị (User, Role, Permission)
│   ├── application/     → Module nghiệp vụ chính (Dengue Case, Outbreak, Dashboard...)
│   └── masterData/      → Module dữ liệu nền (Province, District, Commune...)
└── myapp/               → Entry point, config, security
```

Mỗi module có 4 layer: **API (Controller) → Service → Repository → Domain (Entity)**

---

### Câu 18: JHipster là gì và vai trò trong dự án?

**Trả lời:** JHipster là **code generator** cho ứng dụng Spring Boot:

- Sinh ra **boilerplate code**: Security config, JWT setup, exception handling, actuator...
- Cung cấp **best practices** cho Spring Boot project structure
- Đi kèm **Mongock** cho database migration
- Cấu hình sẵn **Docker, CI/CD, monitoring**
- Trong dự án dùng JHipster 8.9.0, giúp tiết kiệm thời gian setup ban đầu, tập trung vào nghiệp vụ

---

### Câu 19: Giải thích pattern BaseCrudApi và cách hoạt động?

**Trả lời:** `BaseCrudApi` là **generic abstract class** cung cấp CRUD operations chuẩn:

```java
BaseCrudApi<TEntity, TKey, TRepository, TPagedInputDto, TPagedOutputDto, TGetByIdDto, TCreateOrUpdateDto>
```

- Tự động cung cấp các endpoint: `get-paged`, `get-by-id`, `create`, `update`, `delete`, `export`
- Controller chỉ cần extend và chỉ định entity type → có đầy đủ CRUD
- Sử dụng **Generic/Template Method Pattern** → giảm code lặp cho **43+ controller**
- Override method cụ thể khi cần customize logic

---

### Câu 20: Cơ chế tìm kiếm nâng cao (Advanced Search) hoạt động thế nào?

**Trả lời:** Sử dụng `CriteriaBuilder` - một fluent API tự xây dựng:

```java
CriteriaBuilder.build()
  .whereIfNotNull(provinceId, "address.level1Id")
  .whereFullTextSearch(keyword, "patientName", "code")
  .where(new SearchCriteria("status", SearchOperation.EQUAL, "CONFIRMED"))
```

- Hỗ trợ các operation: `EQUAL`, `LIKE`, `GREATER_THAN`, `LESS_THAN`, `IN`, `BETWEEN`...
- Tự động bỏ qua điều kiện null (`whereIfNotNull`)
- Hỗ trợ **full-text search** trên nhiều field
- Kết hợp với Spring Data MongoDB `Criteria` để tạo query

---

### Câu 21: Bảo mật JWT trong hệ thống hoạt động thế nào?

**Trả lời:**

1. **Login:** User gửi credentials → server xác thực → trả JWT (accessToken + refreshToken)
2. **JWT Claims:** Chứa `user_id`, `username`, `email`, `authorities`, `token_id`, `change_password_time`
3. **Mã hóa:** HS256 (HMAC-SHA256) với Base64 secret key
4. **Thời hạn:** 24h (normal) hoặc 30 ngày (remember me)
5. **Token Refresh:** Khi access token hết hạn, dùng refresh token lấy token mới
6. **Token Blacklist:** Lưu trên Redis, kiểm tra mỗi request → hỗ trợ revoke token (logout)
7. **Security Filter Chain:** `CORS → Stateless session → Bearer token validation → Permission check`

---

### Câu 22: Annotation @Permission hoạt động thế nào?

**Trả lời:**

- `@Permission` là **custom annotation** gắn lên method controller
- `PermissionInterceptor` (HandlerInterceptor) chặn request trước khi vào controller
- Đọc annotation `@Permission("policy.name")` trên method
- So sánh với `listPermission` của user hiện tại trong session
- Nếu user có quyền → cho phép tiếp tục; không có → trả **403 Forbidden**
- **Super Admin** (level đặc biệt) được bypass tất cả permission check

---

### Câu 23: Database migration được xử lý thế nào?

**Trả lời:**

- Sử dụng **Mongock** - framework migration cho MongoDB
- File migration: `InitialSetupMigration` tại `config/dbmigrations/`
- Khi ứng dụng khởi động, Mongock quét package và chạy các migration chưa thực hiện
- Migration ban đầu tạo admin user mặc định với password hash
- MongoDB index được tạo tự động qua `@Indexed` annotation trên entity

---

### Câu 24: Giải thích cách xử lý file upload với SeaweedFS?

**Trả lời:**

- **SeaweedFS** là hệ thống lưu trữ file phân tán (distributed file system)
- Kiến trúc: **Master** (quản lý) → **Volume** (lưu trữ) → **Filer** (quản lý metadata)
- Flow upload: `Client → Backend API /api/file → SeaweedFS Filer → lưu vào Volume`
- File metadata lưu trong MongoDB (`FileUploadEntity`)
- Hỗ trợ file tối đa **100MB** (cấu hình `spring.servlet.multipart.max-file-size`)
- Deploy bằng Docker Compose với 3 service: master, volume, filer

---

### Câu 25: Cách xử lý lỗi (Error Handling) trong backend?

**Trả lời:**

- **`OrdBusinessException`** - Exception nghiệp vụ tùy chỉnh, kế thừa `ErrorResponseException`
- Fluent API: `.withCode("ERROR_CODE").withProperty("field", value)`
- **`ExceptionTranslator`** - Global exception handler, chuyển exception thành HTTP response
- Response format chuẩn:

```json
{
  "isSuccessful": false,
  "code": "400",
  "errorMessage": "Technical error message",
  "errorTranslate": "User-friendly translated message",
  "result": null
}
```

- Validation error trả về chi tiết từng field lỗi
- Hỗ trợ đa ngôn ngữ cho error message (`errorTranslate`)

---

## V. Câu hỏi về thiết kế CSDL

### Câu 26: Thiết kế entity cho ca bệnh (DetailCase) gồm những gì?

**Trả lời:** `DetailCaseEntity` gồm:

- **Thông tin bệnh nhân:** Tên, tuổi, giới tính, CMND, SĐT
- **Thông tin ca bệnh:** Mã ca, ngày phát hiện, ngày nhập viện, triệu chứng, chẩn đoán
- **Địa chỉ:** Dạng nested document với 4 cấp (tỉnh/huyện/xã/thôn) sử dụng Address model
- **Tọa độ GIS:** `GeoJsonPoint` cho vị trí chính xác trên bản đồ
- **Audit fields:** `createdBy`, `createdDate`, `lastModifiedBy`, `lastModifiedDate` (kế thừa `FullAuditEntity`)

---

### Câu 27: Tại sao dùng GeoJSON trong MongoDB?

**Trả lời:**

- MongoDB hỗ trợ **native GeoJSON** types và **geospatial indexes**
- Cho phép query theo **khoảng cách** (tìm ca bệnh trong bán kính X km)
- Hỗ trợ query theo **polygon** (tìm ca bệnh trong ranh giới hành chính)
- Tích hợp trực tiếp với OpenLayers ở frontend để hiển thị bản đồ
- Types sử dụng: `GeoJsonPoint` (vị trí ca bệnh), `GeoJsonPolygon` (ranh giới ổ dịch), `GeoJsonMultiPolygon`

---

### Câu 28: Mô hình địa chỉ nhiều cấp được thiết kế như thế nào?

**Trả lời:** Sử dụng **Address model** dạng nested document:

```
Address {
  level0Id, level0Name  → Quốc gia
  level1Id, level1Name  → Tỉnh/Thành phố
  level2Id, level2Name  → Quận/Huyện
  level3Id, level3Name  → Phường/Xã
}
```

- Lưu cả ID lẫn Name → **tránh JOIN** khi hiển thị
- Master data riêng cho từng cấp: Province, District, Commune, Hamlet
- Cho phép filter báo cáo theo từng cấp hành chính

---

## VI. Câu hỏi về bảo mật

### Câu 29: Hệ thống có những biện pháp bảo mật nào?

**Trả lời:**

| Biện pháp | Chi tiết |
|-----------|----------|
| **Authentication** | JWT token-based với refresh token mechanism |
| **Authorization** | Permission-based access control (RBAC) với `@Permission` annotation |
| **CAPTCHA** | Bắt buộc sau N lần đăng nhập thất bại |
| **Token Blacklist** | Redis-based, hỗ trợ logout/revoke token |
| **Password Security** | Hash bằng BCrypt, password blacklist, log lịch sử đổi mật khẩu |
| **XSS Protection** | DOMPurify sanitize HTML ở frontend, header `X-XSS-Protection` |
| **CORS** | Chỉ cho phép origin cụ thể |
| **CSRF** | Disabled (vì dùng JWT stateless, không dùng cookie-based auth) |
| **CSP** | Content Security Policy: chỉ cho phép script từ cùng origin |
| **HTTPS/TLS** | Hỗ trợ cấu hình TLS cho production |

---

### Câu 30: Tại sao disable CSRF khi dùng JWT?

**Trả lời:** CSRF attack lợi dụng **cookie tự động gửi** theo request. Hệ thống dùng JWT lưu trong `localStorage` và gắn vào header `Authorization` → browser **không tự động gửi** → CSRF attack không thể thực hiện. Do đó disable CSRF là an toàn và đúng practice cho stateless JWT authentication.

---

### Câu 31: Giải thích cơ chế refresh token?

**Trả lời:**

1. Khi login, server trả về `accessToken` (ngắn hạn, 24h) + `refreshToken` (dài hạn, 30 ngày)
2. Client dùng accessToken cho mỗi API call
3. Khi accessToken hết hạn (server trả 401), client tự động gọi endpoint refresh
4. Server xác thực refreshToken, nếu hợp lệ → cấp cặp token mới
5. Client cập nhật token mới, retry request ban đầu
6. Nếu refreshToken cũng hết hạn → redirect về login

> **Mục đích:** Cân bằng giữa bảo mật (token ngắn hạn) và trải nghiệm (không phải login lại thường xuyên)

---

## VII. Câu hỏi về hiệu năng & triển khai

### Câu 32: Dự án tối ưu hiệu năng như thế nào?

**Trả lời:**

- **Frontend:** Lazy loading/code splitting, MobX fine-grained reactivity, Vite tree-shaking, asset hash caching
- **Backend:** Redis caching, MongoDB auto-indexing, response compression (gzip cho >1KB)
- **Server:** Undertow (thay Tomcat) - nhẹ và hiệu năng cao hơn
- **Database:** MongoDB index tự động tạo, GeoSpatial index cho query bản đồ

---

### Câu 33: Hệ thống được triển khai (deploy) như thế nào?

**Trả lời:**

- **Frontend:** `vite build` → static files → deploy lên web server (Nginx)
- **Backend:** `mvn package` → JAR file → chạy bằng `java -jar`
- **Database & Services:** Docker Compose cho MongoDB, SeaweedFS (master + volume + filer)
- **Profiles:** `dev` (development), `prod` (production), `tls` (HTTPS)
- **Monitoring:** Spring Boot Actuator endpoints (`/management/health`, `/management/info`)

---

### Câu 34: Hệ thống hỗ trợ offline không?

**Trả lời:** Hệ thống có **Dexie.js** (IndexedDB wrapper) ở frontend:

- Lưu trữ sync state giữa local và server
- Đếm số lần đăng nhập thất bại cho CAPTCHA
- Database được tạo riêng cho mỗi user: `MyShopPos-{userId}`
- Có cơ chế `handleSyncDataDexieDb` trong response interceptor để đồng bộ dữ liệu

---

## VIII. Câu hỏi về Design Pattern

### Câu 35: Những design pattern nào được sử dụng trong dự án?

**Trả lời:**

| Pattern | Vị trí áp dụng |
|---------|----------------|
| **Generic/Template Method** | `BaseCrudApi`, `CommonListStore` - CRUD operations chuẩn |
| **Repository Pattern** | Spring Data MongoDB repositories (35+ repos) |
| **DTO Pattern** | Tách biệt API input/output với entity |
| **Factory Pattern** | `AppFactory` - service locator trung tâm |
| **Builder Pattern** | `CriteriaBuilder` - fluent API cho search criteria |
| **Interceptor Pattern** | `PermissionInterceptor`, Axios interceptors |
| **Observer Pattern** | MobX `observable`/`observer` cho reactive UI |
| **Strategy Pattern** | `SearchOperation` enum, export strategies |
| **Singleton Pattern** | MobX stores (một instance duy nhất) |
| **HOC Pattern** | Higher-Order Components cho cross-cutting concerns |

---

## IX. Câu hỏi kỹ thuật nâng cao

### Câu 36: Giải thích Axios interceptor trong dự án?

**Trả lời:**

- **Request interceptor:** Gắn headers (`Authorization`, `Accept-Language`, `x-client-id`, `x-shop-current`) vào mọi request
- **Response interceptor (success):** Xử lý sync data với Dexie.js
- **Response interceptor (error):**
  - `401` → Tự động refresh token, retry request
  - `403` → Hiển thị thông báo "Không có quyền" với mã lỗi tương ứng
  - Khác → Hiển thị error message

---

### Câu 37: Export Excel/PDF được hiện thực thế nào?

**Trả lời:**

- **Backend:** Apache POI cho Excel, iText 7 cho PDF, docx4j cho Word, JODConverter cho chuyển đổi LibreOffice
- **Frontend:** Thư viện `xlsx` cho Excel import/export, `jspdf` + `html2canvas` cho PDF, `recharts-to-png` cho xuất biểu đồ
- Hỗ trợ cả export từ server (data lớn) và client (biểu đồ, báo cáo đơn giản)

---

### Câu 38: Dashboard được xây dựng như thế nào?

**Trả lời:**

- Sử dụng **Recharts** + **Chart.js** cho biểu đồ (Line, Bar, Stacked, Area)
- Layout dùng **react-grid-layout** cho phép kéo thả, resize widget
- Dữ liệu từ `DashboardApiResource` với các API thống kê tổng hợp
- Các loại card: phân bổ ca bệnh, so sánh, mật độ, quan hệ, ổ dịch theo vùng
- Hỗ trợ xuất biểu đồ thành PNG (`recharts-to-png`)

---

### Câu 39: Đường cong dịch tễ (Normal Curve) được tính toán thế nào?

**Trả lời:**

- Entity `NormalCurveEntity` lưu dữ liệu đường cong bình thường của dịch
- Service `NormalCurveServiceImpl` tính toán và so sánh số ca thực tế với đường cong bình thường
- Giúp phát hiện **bất thường dịch tễ** khi số ca vượt ngưỡng bình thường
- Hiển thị trên Dashboard dưới dạng biểu đồ so sánh

---

### Câu 40: Hệ thống thông báo (Notification) hoạt động thế nào?

**Trả lời:**

- `SystemNotificationsEntity` lưu thông báo hệ thống
- Có 2 store: `systemNotificationsStore` (tất cả) và `systemNotificationsReadStore` (đã đọc)
- `reloadNotiStore` trigger reload khi có thông báo mới
- Frontend dùng `react-toastify` cho toast notification
- Backend gửi email thông báo qua Gmail SMTP (`SendEmailService`)

---

## X. Câu hỏi phản biện

### Câu 41: Hạn chế của hệ thống là gì?

**Trả lời:**

- Chưa có **real-time notification** (WebSocket/SSE) - hiện dùng polling
- MongoDB không hỗ trợ **transaction phức tạp** như SQL (dù MongoDB 4.0+ đã hỗ trợ multi-document transaction)
- Chưa có **unit test** đầy đủ cho tất cả service
- SeaweedFS cần quản lý riêng, tăng độ phức tạp vận hành
- Chưa có CI/CD pipeline hoàn chỉnh

---

### Câu 42: Nếu số lượng ca bệnh tăng lên hàng triệu, hệ thống xử lý thế nào?

**Trả lời:**

- **MongoDB:** Hỗ trợ horizontal scaling (sharding) native, có thể phân tán dữ liệu theo khu vực địa lý
- **Backend:** Stateless design → có thể chạy nhiều instance phía sau load balancer
- **Caching:** Redis cache giảm tải database cho các query báo cáo
- **Frontend:** Infinite scroll, phân trang, lazy loading giảm tải client
- **Index:** GeoSpatial index + text index tối ưu query

---

### Câu 43: So sánh giải pháp của em với các hệ thống quản lý dịch bệnh hiện có?

**Trả lời:**

- **Ưu điểm:** Tùy biến cho Việt Nam (địa chỉ 4 cấp hành chính), GIS tích hợp, đánh giá nguy cơ cho người dân, dashboard trực quan
- **Khác biệt:** Web-based (không cần cài đặt), đa ngôn ngữ, phân quyền chi tiết, hỗ trợ offline cơ bản
- **Hạn chế so với:** Hệ thống lớn như DHIS2 có tính năng phong phú hơn, cộng đồng lớn hơn

---

### Câu 44: Tại sao dùng Vite thay vì Create React App (CRA)?

**Trả lời:**

- CRA đã **ngừng maintain** và không được khuyến nghị cho dự án mới
- Vite dùng **ESBuild** (Go-based) cho dev server → khởi động nhanh hơn 10-100x so với Webpack
- **Hot Module Replacement (HMR)** gần như tức thì
- Build production dùng **Rollup** → output tối ưu hơn
- Hỗ trợ **path alias**, PostCSS, Tailwind out of the box

---

### Câu 45: Em đã test hệ thống như thế nào?

**Trả lời:**

- **Backend:** JUnit 5 cho unit test, TestContainers cho integration test với MongoDB thật
- **Frontend:** Có config test trong package.json nhưng chủ yếu test thủ công
- **API Testing:** Swagger UI để test endpoint trực tiếp
- **Manual Testing:** Test chức năng end-to-end qua giao diện

---

## Lời khuyên khi bảo vệ

> **Các câu hỏi thường được hỏi nhiều nhất:**
>
> 1. **Kiến trúc tổng thể** (Câu 4) - Phải vẽ được sơ đồ kiến trúc
> 2. **Authentication/Authorization** (Câu 21, 22) - Giải thích rõ flow JWT
> 3. **Design Pattern** (Câu 35) - Nắm vững pattern nào dùng ở đâu
> 4. **Tại sao chọn công nghệ X** (Câu 5, 6, 7) - So sánh ưu/nhược điểm
> 5. **Hạn chế & hướng phát triển** (Câu 41) - Thể hiện sự trung thực và hiểu biết
