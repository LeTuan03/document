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
- [XI. Câu hỏi chuyên sâu về bảo mật & lỗ hổng](#xi-câu-hỏi-chuyên-sâu-về-bảo-mật--lỗ-hổng)
- [XII. Câu hỏi hóc búa về Frontend](#xii-câu-hỏi-hóc-búa-về-frontend)
- [XIII. Câu hỏi hóc búa về Backend](#xiii-câu-hỏi-hóc-búa-về-backend)
- [XIV. Câu hỏi về lỗ hổng thiết kế & Trade-off](#xiv-câu-hỏi-về-lỗ-hổng-thiết-kế--trade-off)
- [XV. Câu hỏi tình huống giả định (Scenario-based)](#xv-câu-hỏi-tình-huống-giả-định-scenario-based)

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

---

## XI. Câu hỏi chuyên sâu về bảo mật & lỗ hổng

> **Mức độ: KHÓ** - Thầy cô am hiểu về bảo mật sẽ hỏi những câu này

### Câu 46: JWT dùng HS512 (symmetric key) — tại sao không dùng RS256 (asymmetric key)? Rủi ro là gì?

**Trả lời:**

- **HS512 (HMAC-SHA512):** Dùng **một secret key** duy nhất cho cả sign lẫn verify token
- **RS256 (RSA-SHA256):** Dùng **private key** để sign và **public key** để verify

**Lý do chọn HS512:**
- Hệ thống chỉ có **1 backend server** verify token → không cần tách private/public key
- HS512 **nhanh hơn** RS256 về mặt tính toán
- Cấu hình **đơn giản hơn**, không cần quản lý cặp key

**Rủi ro:**
- Nếu secret key bị lộ → attacker có thể **tự tạo token hợp lệ** (với RS256 thì chỉ lộ public key, không thể tạo token)
- Không thể chia sẻ khả năng verify cho service khác mà không lộ signing key

**Nếu hệ thống mở rộng sang microservices** thì nên chuyển sang RS256 để các service khác chỉ cần public key để verify.

---

### Câu 47: Token lưu trong localStorage — có lỗ hổng bảo mật gì? Tại sao không dùng HttpOnly Cookie?

**Trả lời:**

**Rủi ro của localStorage:**
- Bất kỳ JavaScript nào chạy trên trang đều **đọc được** localStorage → nếu có lỗ XSS, attacker lấy được token
- Không tự động hết hạn khi đóng tab

**Tại sao không dùng HttpOnly Cookie:**
- HttpOnly Cookie **không bị JavaScript đọc** → an toàn hơn với XSS
- Nhưng nếu dùng Cookie → phải **bật lại CSRF protection** vì browser tự động gửi cookie
- CORS configuration phức tạp hơn (credentials: true, sameSite, secure...)
- Frontend và backend có thể ở **khác domain** → cookie cross-domain rất phức tạp

**Biện pháp giảm thiểu đã áp dụng:**
- **DOMPurify** sanitize HTML trước khi render → ngăn XSS
- **Content Security Policy** header: `script-src 'self'` → chặn inline script
- **X-XSS-Protection** header bật
- Access token **thời hạn ngắn** (24h) → giảm thiệt hại nếu bị lộ

**Phương án tốt hơn (nếu cải tiến):** Lưu token trong **memory** (biến JavaScript), dùng refresh token trong HttpOnly Cookie để lấy access token mới khi reload trang.

---

### Câu 48: Hệ thống có lỗ hổng NoSQL Injection không? Giải thích cụ thể.

**Trả lời:**

**Điểm an toàn:**
- Sử dụng **Spring Data MongoDB** với `Criteria` API → tự động parameterize query, không nối string trực tiếp
- `CriteriaBuilder` chuyển giá trị user input thành **parameter** chứ không ghép vào query string

**Điểm cần lưu ý:**
- Trong `AdvSearchHelp`, full-text search dùng **regex** trực tiếp từ user input:
  ```java
  Criteria.where(column).regex(patternSearch, "i");
  ```
- Nếu user gửi pattern phức tạp như `(a+)+$` → có thể gây **ReDoS** (Regular Expression Denial of Service)

**Biện pháp khắc phục:**
- Escape ký tự đặc biệt regex trước khi search: `Pattern.quote(userInput)`
- Hoặc giới hạn độ dài search input
- Hoặc dùng MongoDB text index thay vì regex

---

### Câu 49: CKEditor cho phép nhập HTML tùy ý — có vấn đề XSS không?

**Trả lời:** **Có — đây là lỗ hổng tiềm ẩn nghiêm trọng.**

Cấu hình `htmlSupport` trong CKEditor hiện tại:
```javascript
htmlSupport: {
    allow: [{
        name: /.*/,         // Cho phép MỌI thẻ HTML
        styles: true,       // Cho phép inline style
        classes: [/.*/],    // Cho phép mọi class
        attributes: [/.*/]  // Cho phép MỌI attribute (kể cả onclick, onerror...)
    }]
}
```

**Rủi ro:** User (hoặc attacker) có thể nhập:
- `<img src=x onerror="alert(document.cookie)">` → thực thi JavaScript
- `<script>...</script>` → chạy code tùy ý
- `<a href="javascript:...">` → redirect exploit

**Tại sao DOMPurify không đủ:**
- DOMPurify chỉ được gọi ở **một số nơi** khi hiển thị (`dangerouslySetInnerHTML`)
- Nếu developer quên gọi `sanitizeHtml()` ở bất kỳ chỗ nào → XSS xảy ra
- Nội dung HTML lưu trong database **chưa được sanitize** → "stored XSS"

**Cách khắc phục:**
1. Thu hẹp `htmlSupport.allow` — chỉ cho phép thẻ an toàn (b, i, p, br, ul, li, img, a, table)
2. **Sanitize ở backend** trước khi lưu database (defense in depth)
3. Luôn dùng `sanitizeHtml()` khi render HTML content

---

### Câu 50: Giải thích cụ thể 3 lớp bảo mật trong PermissionInterceptor?

**Trả lời:** `PermissionInterceptor` kiểm tra **3 lớp tuần tự** trước khi cho phép request:

**Lớp 1 — Token Blacklist (Redis):**
```java
// Kiểm tra token đã bị thu hồi chưa
String tokenId = jwtService.getClaimValue("token_id");
if (isTokenBlacklisted(tokenId)) {  // Lookup Redis key: "jwt_blacklist_EDengue:{tokenId}"
    return 401;  // TOKEN_REVOKED
}
```
→ Khi user logout hoặc admin vô hiệu hóa, token_id được thêm vào Redis blacklist.

**Lớp 2 — Password Change Detection:**
```java
// Nếu user đã đổi mật khẩu SAU KHI token được cấp → vô hiệu hóa token cũ
String changePwdInToken = jwtService.getClaimValue("change_password_time");
String changePwdInDb = userInfo.getChangePasswordDateTime();
if (!changePwdInDb.equals(changePwdInToken)) {
    return 401;  // PASSWORD_CHANGED
}
```
→ Khi đổi mật khẩu, `change_password_time` trong DB thay đổi nhưng token vẫn giữ giá trị cũ → mismatch → token bị reject.

**Lớp 3 — Permission Check (@Permission annotation):**
```java
Permission permission = handlerMethod.getMethodAnnotation(Permission.class);
if (permission != null) {
    if (appFactory.isSuperAdmin()) return true;   // Super Admin bypass
    if (!appFactory.checkPermission(permission.value())) {
        return 403;  // FORBIDDEN
    }
}
```
→ Kiểm tra user có quyền cụ thể được khai báo trên method không.

**Tại sao cần cả 3 lớp:** Mỗi lớp xử lý một trường hợp khác nhau — logout tức thì (lớp 1), đổi mật khẩu bắt buộc đăng nhập lại (lớp 2), và phân quyền chi tiết (lớp 3).

---

### Câu 51: XSS trong Map Popup — em có biết lỗ hổng này không?

**Trả lời:** **Có — đây là điểm cần cải thiện.**

Trong `DengueCaseMap.tsx`, popup hiển thị thông tin ca bệnh bằng cách gán `innerHTML` trực tiếp:
```javascript
popupRef.current.innerHTML = `
    <b>${feature.get("name")}</b><br>
    Tổng ca: <b>${feature.get("object")?.totalCase}</b>
`;
```

**Vấn đề:** `feature.get("name")` lấy từ GeoJSON data — nếu dữ liệu bị tamper (ví dụ tên địa điểm chứa `<script>`), sẽ thực thi code.

**Mức độ rủi ro thực tế:** THẤP — vì dữ liệu GeoJSON đến từ **server do ta quản lý**, không phải user input trực tiếp. Nhưng nếu có lỗ hổng ở API nhập liệu → có thể bị khai thác gián tiếp (stored XSS).

**Cách khắc phục:**
- Dùng `textContent` thay `innerHTML` cho text thuần
- Hoặc dùng `DOMPurify.sanitize()` trước khi gán innerHTML
- Hoặc dùng React component để render popup thay vì thao tác DOM trực tiếp

---

## XII. Câu hỏi hóc búa về Frontend

> **Mức độ: KHÓ** - Thầy cô chuyên về Frontend/React sẽ hỏi

### Câu 52: Khi 2 API call cùng lúc bị 401, token refresh xử lý race condition thế nào?

**Trả lời:** Hệ thống dùng **queue pattern** trong Axios interceptor:

```
Request A → 401 → isRefreshing = false → gọi refreshToken() → isRefreshing = true
Request B → 401 → isRefreshing = true  → đẩy vào refreshSubscribers queue (chờ)
Request C → 401 → isRefreshing = true  → đẩy vào refreshSubscribers queue (chờ)

refreshToken() success → onRefreshed(newToken) → resolve tất cả request trong queue
                       → Request B retry với new token
                       → Request C retry với new token
```

**Cơ chế:**
- Biến `isRefreshing` (module-level) đảm bảo **chỉ gọi refresh 1 lần**
- Array `refreshSubscribers` chứa callback của các request đang chờ
- Khi refresh xong, `onRefreshed()` gọi tất cả callback với token mới

**Lỗ hổng tiềm ẩn:** Nếu `refreshToken()` **thất bại**, biến `isRefreshing` **không được reset** về `false` → tất cả request tiếp theo sẽ bị treo vĩnh viễn trong queue. Cần thêm `finally { isRefreshing = false; }` hoặc timeout mechanism.

---

### Câu 53: Permission cache ở frontend không được refresh — nếu admin thu hồi quyền user thì sao?

**Trả lời:**

**Thực trạng:** Khi app bootstrap, `sessionStore.setSession()` tạo object `permissionGranted` từ `listPermission`:
```typescript
session.user.listPermission.forEach(name => {
    session.permissionGranted[name] = true;  // Cache 1 lần
});
```

**Vấn đề:** Nếu admin thu hồi quyền của user đang online:
1. Frontend vẫn hiển thị menu/button (vì cache chưa thay đổi)
2. User vẫn thấy trang (ProtectedRoute dùng cache)
3. **NHƯNG** API call sẽ bị **backend reject** (403) vì backend kiểm tra permission real-time

**Tại sao chấp nhận được:**
- Frontend chỉ là **UX layer** — security thực sự nằm ở backend `@Permission` annotation
- Khi user refresh trang hoặc token hết hạn → permission được cập nhật lại
- Đây là pattern phổ biến trong SPA — **optimistic UI** với **server-side enforcement**

**Cải tiến nếu cần:**
- Dùng WebSocket/SSE để push permission changes real-time
- Hoặc polling `/api/auth-plugin/information/getBoostrap` định kỳ (ví dụ 5 phút/lần)

---

### Câu 54: Dexie.js (IndexedDB) có schema rỗng và sync mechanism chưa implement — giải thích?

**Trả lời:**

**Thực trạng:**
- `OrdDbClientDexie` tạo database với schema `Object.assign({})` — không có table nào được khai báo
- `HttpInterceptorService.handleSyncDataDexieDb()` là **empty function** — chưa có logic đồng bộ

**Giải thích:** Dexie.js được setup như một **infrastructure sẵn sàng** cho tính năng offline trong tương lai. Hiện tại chỉ sử dụng cho:
1. **Đếm số lần đăng nhập thất bại** → hiển thị CAPTCHA
2. **Lưu version setting** → quản lý database migration client-side

**Hướng phát triển:** Khi implement đầy đủ, Dexie.js sẽ:
- Cache danh sách ca bệnh đã xem offline
- Cho phép nhập liệu khi mất kết nối, sync lên server khi có mạng
- Cache master data (tỉnh/huyện/xã) để giảm API call

---

### Câu 55: Giải thích chi tiết cách MobX `observer` tối ưu render trong dự án?

**Trả lời:**

**Nguyên lý:** MobX theo dõi **chính xác property nào** được đọc trong render function:
```typescript
const DetailCase = observer(() => {
    const { detailCaseStore } = useStore();
    return <Table data={detailCaseStore.currentPageResult.items} />
    // MobX chỉ re-render khi currentPageResult.items thay đổi
    // KHÔNG re-render khi searchDataState hay createOrUpdateModal thay đổi
});
```

**So sánh với Redux:**
- Redux: Component subscribe toàn bộ store slice → re-render khi **bất kỳ field nào** trong slice thay đổi
- MobX: Component **chỉ re-render khi property được đọc** thay đổi → **fine-grained reactivity**

**Trong dự án:**
- 50+ store nhưng mỗi component chỉ phản ứng với data nó thực sự dùng
- `observer()` HOC wrap component → tạo **reactive boundary**
- `makeAutoObservable(this)` trong store → tự động đánh dấu properties là observable

**Nhược điểm:**
- Debug khó hơn Redux (không có Redux DevTools time-travel)
- Phải nhớ wrap `observer()` — nếu quên thì component không re-render
- Implicit dependencies → khó trace data flow trong dự án lớn

---

### Câu 56: Tại sao dùng cả Recharts lẫn Chart.js? Không dư thừa sao?

**Trả lời:**

- **Recharts** (React-native): Dùng cho dashboard chính — tích hợp tốt với React lifecycle, hỗ trợ responsive, animation, export PNG (`recharts-to-png`)
- **Chart.js** (via react-chartjs-2): Dùng cho các biểu đồ đặc thù mà Recharts không hỗ trợ tốt (ví dụ: stacked mixed chart, radar chart)

**Trade-off:**
- Tăng bundle size (~100KB thêm cho Chart.js)
- Nhưng mỗi thư viện có **thế mạnh riêng** cho các loại biểu đồ khác nhau
- **Cải tiến:** Nên thống nhất về 1 thư viện (ưu tiên Recharts vì React-native) và custom cho các chart đặc biệt

---

### Câu 57: `dangerouslySetInnerHTML` được dùng ở những đâu? Có an toàn không?

**Trả lời:** Các vị trí sử dụng:

| Vị trí | Có sanitize? | Rủi ro |
|--------|-------------|--------|
| System Notifications (messageBody) | **Có** — `Utils.sanitizeHtml()` | Thấp |
| Landing Page (post content) | **Có** — `Utils.sanitizeHtml()` | Thấp |
| Project Information page | **Có** — `Utils.sanitizeHtml()` | Thấp |
| Map Popup (DengueCaseMap) | **KHÔNG** — gán `innerHTML` trực tiếp | **Cao** |
| Forecast Map Popup | **KHÔNG** — gán `innerHTML` trực tiếp | **Cao** |

**Kết luận:** Các nơi dùng `dangerouslySetInnerHTML` đều qua DOMPurify → an toàn. Nhưng map popup dùng **DOM manipulation trực tiếp** (`popupRef.current.innerHTML = ...`) mà không sanitize → cần sửa.

---

## XIII. Câu hỏi hóc búa về Backend

> **Mức độ: KHÓ** - Thầy cô chuyên về Java/Spring Boot sẽ hỏi

### Câu 58: BaseCrudApi phân biệt Create vs Update bằng cách nào? Có lỗ hổng gì không?

**Trả lời:**

**Cơ chế:** Kiểm tra ID có null hay không:
```java
if (dto.getId() == null) {
    // CREATE mới
    entity = modelMapper.map(dto, entityClass);
    repository.save(entity);
} else {
    // UPDATE entity có sẵn
    entity = repository.findById(dto.getId());
    modelMapper.map(dto, entity);
    repository.save(entity);
}
```

**Lỗ hổng tiềm ẩn:**
- Nếu user **tự set ID** trong create request → hệ thống hiểu là UPDATE
- Nếu ID không tồn tại trong DB → `findById()` trả null → NullPointerException
- User có thể **update record của người khác** nếu không có kiểm tra ownership

**Biện pháp đã có:**
- Hook method `validationBeforeCreateOrUpdate(isCreateNew, dto)` để validate
- Hook `validationUpdate(dto, entityUpdate)` để kiểm tra quyền sửa
- `@Transactional(rollbackFor = ...)` đảm bảo rollback khi lỗi

**Cải tiến:** Nên tách thành 2 endpoint riêng `/create` và `/update` thay vì `/create-or-update` để rõ ràng hơn.

---

### Câu 59: Soft Delete có vấn đề gì? Làm sao đảm bảo không query được data đã xóa?

**Trả lời:**

**Cơ chế soft delete:**
```java
// Không xóa thật, chỉ đánh dấu
entity.setIsDeleted(true);
entity.setDeletionTime(LocalDateTime.now());
repository.save(entity);
```

**Đảm bảo filter:**
- `CriteriaBuilder` **tự động thêm** `Criteria.where("isDeleted").is(false)` vào **mọi query**
- Đây là single point of enforcement — tất cả search đều đi qua `CriteriaBuilder`

**Vấn đề tiềm ẩn:**
1. Nếu developer query trực tiếp qua `MongoTemplate` hoặc `repository.findById()` **không qua CriteriaBuilder** → có thể lấy được record đã xóa
2. `findById()` trong update flow **không kiểm tra isDeleted** → có thể update record đã xóa
3. Data đã xóa vẫn chiếm **storage space** → cần job dọn dẹp định kỳ

**Cải tiến:**
- Thêm `@Query("{'isDeleted': false}")` mặc định trên repository method
- Hoặc dùng MongoDB **TTL index** để tự động xóa cứng sau N ngày

---

### Câu 60: AppFactory dùng Service Locator Pattern — tại sao không dùng Dependency Injection thuần?

**Trả lời:**

**AppFactory hiện tại:**
```java
// ConcurrentHashMap cache + applicationContext.getBean()
public <T> T getService(Class<T> requiredType) {
    return serviceCache.computeIfAbsent(requiredType, applicationContext::getBean);
}
```

**Lý do dùng Service Locator:**
- Các base class generic (`BaseCrudApi`) không biết trước cần service nào → DI truyền thống không phù hợp
- `AppFactory` đóng vai trò **trung tâm** cung cấp service cho mọi module
- Cache trong `ConcurrentHashMap` → hiệu năng tốt, thread-safe

**Nhược điểm (so với DI thuần):**
- **Implicit dependency** — không nhìn thấy dependency trong constructor → khó test, khó trace
- **Coupling** với ApplicationContext → khó mock trong unit test
- Vi phạm **Dependency Inversion Principle** — high-level module phụ thuộc vào concrete factory

**Trade-off chấp nhận được:** Trong dự án có 43+ controller kế thừa BaseCrudApi, việc inject tất cả service qua constructor sẽ tạo **constructor bloat**. AppFactory giúp code gọn hơn với chi phí là implicit dependencies.

---

### Câu 61: Password mặc định admin hardcode trong config — có vấn đề gì?

**Trả lời:**

**Thực trạng:**
```yaml
# application.yml
application:
  default-password-admin: 'Orenda@123'
```

**Vấn đề:**
- Mật khẩu mặc định nằm **trong source code** → nếu repository public thì ai cũng biết
- Nếu admin không đổi mật khẩu sau cài đặt → hệ thống bị compromise

**Biện pháp giảm thiểu đã có:**
- Mật khẩu chỉ dùng cho **initial migration** (tạo admin lần đầu)
- BCrypt hash → mật khẩu lưu trong DB là hash, không phải plaintext
- Field `mustChangePassword` có thể bắt buộc đổi mật khẩu lần đầu đăng nhập

**Cải tiến:**
- Đọc default password từ **environment variable** thay vì config file
- Hoặc generate random password lúc migration, in ra console log
- Bắt buộc đổi mật khẩu ở lần đăng nhập đầu tiên

---

### Câu 62: BCrypt cost factor mặc định là 10 — có đủ an toàn không?

**Trả lời:**

```java
new BCryptPasswordEncoder();  // Default cost = 10 → 2^10 = 1,024 iterations
```

**Phân tích:**
- Cost 10 là **mức tối thiểu chấp nhận** theo OWASP 2023
- Mỗi tăng 1 cost → **gấp đôi thời gian** hash
- Cost 10 ≈ ~100ms/hash, Cost 12 ≈ ~400ms/hash trên server thông thường

**So sánh:**

| Cost | Thời gian/hash | Khuyến nghị |
|------|---------------|-------------|
| 10 | ~100ms | Tối thiểu chấp nhận |
| 11 | ~200ms | Tốt |
| 12 | ~400ms | **OWASP khuyến nghị** |
| 14 | ~1.6s | An toàn cao nhưng ảnh hưởng UX |

**Kết luận:** Cost 10 **chấp nhận được** cho đồ án, nhưng production nên dùng **cost 12**. Trade-off: tăng cost → đăng nhập chậm hơn → cần cân bằng giữa bảo mật và UX.

---

### Câu 63: Spring Boot Actuator expose những gì? Có rủi ro gì?

**Trả lời:**

**Endpoints được expose:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, info, configprops, env, loggers, prometheus
```

**Bảo mật:**
- `/management/**` yêu cầu **ROLE_ADMIN** trong SecurityFilterChain
- `/management/health` cho phép public (dùng cho health check)

**Rủi ro nếu cấu hình sai:**
- `/management/env` → lộ **environment variables**, database URL, secret key
- `/management/configprops` → lộ **toàn bộ cấu hình** application
- `/management/loggers` → attacker có thể **thay đổi log level** runtime (ví dụ: bật DEBUG để lộ thông tin nhạy cảm)

**Biện pháp:**
- Đã yêu cầu ROLE_ADMIN → chỉ admin mới truy cập
- Production nên chỉ expose `health` và `prometheus`, ẩn `env`/`configprops`
- Hoặc chạy actuator trên **port riêng** (management.server.port) không public

---

### Câu 64: Tại sao dùng Undertow thay Tomcat? Khác biệt thực tế là gì?

**Trả lời:**

**Undertow:**
- **Non-blocking I/O** (NIO) based — xử lý nhiều concurrent connection với ít thread hơn
- **Nhẹ hơn** Tomcat (~20MB vs ~50MB memory footprint)
- Khởi động **nhanh hơn**
- Phù hợp cho **REST API** thuần (không cần JSP, Servlet 3.0 advanced features)

**Tomcat:**
- Mature, battle-tested, cộng đồng lớn hơn
- Hỗ trợ đầy đủ Servlet spec
- Nhiều documentation hơn

**Trong dự án:** Backend chỉ serve REST API (không có JSP/server-side rendering) → Undertow là lựa chọn **phù hợp hơn**, nhẹ và nhanh.

---

## XIV. Câu hỏi về lỗ hổng thiết kế & Trade-off

> **Mức độ: RẤT KHÓ** - Thầy cô muốn kiểm tra tư duy kiến trúc

### Câu 65: Hệ thống chỉ có REST API polling — tại sao không dùng WebSocket cho real-time?

**Trả lời:**

**Lý do không dùng WebSocket:**
- **Độ phức tạp tăng đáng kể:** Cần quản lý connection state, reconnection, heartbeat
- **Infrastructure:** WebSocket cần reverse proxy (Nginx) cấu hình đặc biệt, load balancer phải hỗ trợ sticky session
- **Scaling:** Mỗi WebSocket connection giữ **persistent TCP connection** → tốn resource server
- **Use case chưa đủ mạnh:** Dữ liệu dịch tễ không thay đổi real-time theo giây — cập nhật theo giờ/ngày là đủ

**Khi nào nên thêm WebSocket:**
- Khi có chức năng **chat giữa các cán bộ y tế**
- Khi cần **alert real-time** khi phát hiện ổ dịch mới
- Khi có nhiều user cùng **edit data đồng thời** cần sync

**Giải pháp trung gian:** **Server-Sent Events (SSE)** — đơn giản hơn WebSocket, một chiều server→client, phù hợp cho push notification.

---

### Câu 66: MongoDB không có schema validation — làm sao đảm bảo data integrity?

**Trả lời:**

**Các lớp validation:**

1. **Frontend validation:** Ant Design Form + Yup schema validation → check trước khi gửi API
2. **Backend DTO validation:** Jakarta Validation (`@NotNull`, `@NotEmpty`, `@Size`) trên DTO class
3. **Service layer validation:** Hook methods `validationBeforeCreateOrUpdate()` cho business rules
4. **MongoDB schema validation (có thể thêm):**
   ```javascript
   db.createCollection("detailCase", {
     validator: { $jsonSchema: { ... } }
   })
   ```

**Trade-off:**
- MongoDB **cố ý** không enforce schema → linh hoạt cho data evolve
- Nhưng validation ở **application layer** đã đảm bảo data integrity
- Nếu cần strict → dùng MongoDB JSON Schema Validation ở database level

---

### Câu 67: Lưu cả ID lẫn Name trong Address model — có vi phạm normalization? Đồng bộ thế nào khi tên tỉnh thay đổi?

**Trả lời:**

**Đúng — vi phạm normalization (denormalization có chủ đích):**

```
Address {
  level1Id: "01",       level1Name: "Hà Nội"    // Lưu cả ID và Name
  level2Id: "001",      level2Name: "Ba Đình"
}
```

**Lý do denormalize:**
- MongoDB **không có JOIN** hiệu quả → nếu chỉ lưu ID, mỗi lần hiển thị phải lookup thêm
- Báo cáo cần hiển thị tên → join hàng triệu record sẽ rất chậm
- Tên tỉnh/huyện/xã ở Việt Nam **rất hiếm khi thay đổi** (chỉ khi tách/nhập đơn vị hành chính)

**Khi tên thay đổi:**
- Cập nhật master data (Province/District/Commune)
- Chạy **batch update** tất cả document có ID tương ứng:
  ```java
  mongoTemplate.updateMulti(
      Query.query(Criteria.where("address.level1Id").is("01")),
      Update.update("address.level1Name", "Tên mới"),
      DetailCaseEntity.class
  );
  ```
- Đây là **eventual consistency** — chấp nhận được vì sự kiện này cực kỳ hiếm

---

### Câu 68: Refresh token không được implement ở backend — em có biết không?

**Trả lời:** **Đúng — đây là điểm chưa hoàn thiện.**

**Thực trạng:**
- `JWTToken.java` có field `refreshToken` nhưng **không bao giờ được populate**
- `AuthorityServiceImpl.createJwtToken()` chỉ tạo **access token**
- Frontend có code gọi `AuthApiService.refreshToken()` nhưng backend **chưa có endpoint tương ứng hoạt động đầy đủ**

**Hệ quả:**
- Access token hạn 24h — user phải đăng nhập lại mỗi 24h
- Nếu token bị lộ → attacker có 24h để khai thác (thay vì 15-30 phút nếu có refresh token)

**Cách khắc phục:**
1. Giảm access token xuống **15 phút**
2. Implement refresh token endpoint — lưu refresh token hash trong DB/Redis
3. Refresh token hạn 7-30 ngày, **single-use** (rotation) — mỗi lần dùng tạo refresh token mới
4. Khi refresh → kiểm tra token blacklist + password change time

---

### Câu 69: Hệ thống có hỗ trợ multi-tenancy không? Giải thích header `x-shop-current`.

**Trả lời:**

**Có dấu hiệu thiết kế cho multi-tenancy:**
- Axios interceptor gắn header `x-shop-current` từ `CurrentShopUtil.getShop()`
- Entity có thể có `tenantCode` hoặc `shopId` để phân tách data
- Login API nhận `TenantCode` parameter

**Mục đích:** Cho phép **một hệ thống serve nhiều đơn vị y tế** (tỉnh/huyện khác nhau), mỗi đơn vị chỉ thấy data của mình.

**Trong E-Dengue:** Multi-tenancy giúp triển khai 1 instance phục vụ nhiều **Trung tâm Y tế** khác nhau, mỗi trung tâm chỉ quản lý ca bệnh trong khu vực mình.

---

## XV. Câu hỏi tình huống giả định (Scenario-based)

> **Mức độ: RẤT KHÓ** - Thầy cô muốn kiểm tra khả năng xử lý vấn đề thực tế

### Câu 70: Nếu có 100 user cùng nhập ca bệnh — hệ thống có bị conflict không?

**Trả lời:**

**Trường hợp 1 — Tạo mới (Create):** Không conflict — mỗi user tạo document mới với ID riêng (MongoDB ObjectId unique).

**Trường hợp 2 — Sửa cùng 1 ca bệnh (Update):**
- Hiện tại: **Last-write-wins** — user cuối cùng save sẽ ghi đè toàn bộ
- Không có **optimistic locking** (version field để detect conflict)

**Giải pháp nếu cần:**
- Thêm field `@Version` vào entity → MongoDB tự động check version khi update
- Nếu version mismatch → throw exception → user phải reload data mới rồi sửa lại
- Hoặc dùng **field-level update** (`$set` chỉ field thay đổi) thay vì ghi đè toàn bộ document

**Mức độ rủi ro thực tế:** THẤP — trong quy trình y tế, mỗi ca bệnh thường do **1 cán bộ phụ trách**, ít khi 2 người cùng sửa.

---

### Câu 71: Server bị crash giữa lúc đang tạo ca bệnh và tạo report — data có inconsistent không?

**Trả lời:**

**Phân tích:**
- `@Transactional` annotation đảm bảo rollback toàn bộ nếu exception
- Nhưng MongoDB **single-document transaction** mặc định — nếu create case thành công nhưng create report thất bại:
  - Case đã lưu ✓
  - Report chưa lưu ✗
  - → **Data inconsistent**

**Giải pháp hiện tại:**
- Spring Data MongoDB hỗ trợ **multi-document transaction** (từ MongoDB 4.0+)
- `@Transactional` đã được cấu hình với `ClientSession` → đảm bảo atomicity nếu MongoDB replica set được setup

**Điều kiện:** MongoDB phải chạy ở **Replica Set mode** (không phải standalone) để transaction hoạt động. Trong development (standalone) → transaction không hoạt động → cần lưu ý khi deploy production.

---

### Câu 72: Nếu Redis chết — hệ thống có hoạt động không?

**Trả lời:**

**Ảnh hưởng khi Redis không khả dụng:**

| Chức năng | Ảnh hưởng | Mức độ |
|-----------|-----------|--------|
| Token Blacklist | Không kiểm tra được → token đã logout vẫn valid | **Cao** |
| Caching | Mất cache → mọi query đến MongoDB trực tiếp → chậm hơn | Trung bình |
| File Token | Không download được file đã generate | Trung bình |

**Hệ thống vẫn hoạt động** nhưng với **security giảm** (token revocation không hoạt động) và **performance giảm** (không có cache).

**Giải pháp:**
- Config Redisson với **fallback** — catch exception khi Redis unavailable
- Cho token blacklist: fallback về **in-memory** set (mất khi restart nhưng có còn hơn không)
- Monitoring: Alert khi Redis connection lost
- Production: Dùng **Redis Sentinel** hoặc **Redis Cluster** cho high availability

---

### Câu 73: User upload file 100MB — flow xử lý như thế nào? Có bottleneck ở đâu?

**Trả lời:**

**Flow:**
```
Browser → [100MB multipart] → Spring Boot (buffer in memory/disk)
       → Backend validate → SeaweedFS Filer API → Volume Server (lưu file)
       → Save FileUploadEntity to MongoDB (metadata)
       → Return file URL to frontend
```

**Bottleneck:**

1. **Spring Boot multipart buffer:** Mặc định buffer file vào **memory** trước khi xử lý
   - Config: `spring.servlet.multipart.max-file-size=100MB`
   - Nếu nhiều user upload cùng lúc → **OutOfMemoryError**
   - Fix: Dùng `spring.servlet.multipart.file-size-threshold` để buffer ra disk

2. **Network:** Upload 100MB qua mạng chậm → HTTP request timeout
   - Fix: Tăng timeout, hoặc implement **chunked upload** (chia file thành nhiều phần nhỏ)

3. **SeaweedFS single volume:** Nếu chỉ 1 volume server → bottleneck I/O
   - Fix: Thêm volume server, SeaweedFS tự load balance

**Cải tiến:** Implement **direct upload** từ browser → SeaweedFS (bypass backend) → gửi file URL về backend để lưu metadata.

---

### Câu 74: Nếu cần thêm chức năng "Chat giữa các cán bộ y tế" — thiết kế thế nào?

**Trả lời:**

**Kiến trúc đề xuất:**

1. **Protocol:** WebSocket (full-duplex, low latency)
2. **Backend:** Thêm Spring WebSocket module (`spring-boot-starter-websocket`)
3. **Message Broker:** Redis Pub/Sub hoặc RabbitMQ (nếu multi-instance)
4. **Storage:** MongoDB collection `ChatMessage` (lưu lịch sử chat)
5. **Frontend:** Thêm WebSocket client, chat component UI

**Flow:**
```
User A gửi message → WebSocket → Backend → Redis Pub/Sub → Broadcast
                                         → MongoDB (lưu history)
                                         → WebSocket → User B nhận
```

**Xác thực:** Dùng JWT token trong WebSocket handshake header để authenticate.

**Tại sao không implement trong đồ án:** Chức năng chat không thuộc **core business** (giám sát dịch tễ), thêm complexity không cần thiết cho scope đồ án.

---

### Câu 75: Hệ thống hiện tại là monolith — nếu chuyển sang microservices thì tách như thế nào?

**Trả lời:**

**Tách theo business domain (bounded context):**

| Microservice | Chức năng | Database |
|-------------|-----------|----------|
| **Auth Service** | Login, JWT, Permission, User management | MongoDB (users) |
| **Epidemic Service** | Dengue Case, Outbreak, Vector, Reports | MongoDB (epidemic) |
| **Master Data Service** | Province, District, Commune, Dictionary | MongoDB (master) |
| **Content Service** | Posts, Categories, Projects | MongoDB (content) |
| **Dashboard Service** | Aggregation, Statistics, Charts | MongoDB (analytics) |
| **File Service** | Upload, Download, SeaweedFS integration | SeaweedFS |
| **Notification Service** | Email, Push, In-app notifications | MongoDB (notifications) |

**Thay đổi cần thiết:**
- **API Gateway:** Kong/Spring Cloud Gateway cho routing
- **JWT:** Chuyển sang RS256 — Auth Service giữ private key, các service khác dùng public key verify
- **Service Communication:** REST hoặc gRPC giữa services
- **Event Bus:** Kafka/RabbitMQ cho async communication (ví dụ: khi tạo ca bệnh → trigger dashboard update)

**Tại sao giữ monolith cho đồ án:** Quy mô nhỏ, team 1 người, monolith **đơn giản hơn** để develop, test, deploy. Microservices chỉ cần khi **team lớn** hoặc **scale independent** từng module.

---

### Câu 76: Em đánh giá đồ án của mình bao nhiêu điểm? Nếu làm lại, em sẽ thay đổi gì?

**Trả lời:**

> **Lưu ý:** Đây là câu hỏi bẫy — đừng tự cho điểm cao quá (tự phụ) hoặc thấp quá (thiếu tự tin). Hãy trả lời **khách quan**.

**Điểm mạnh đã đạt được:**
- Kiến trúc module rõ ràng, base class giảm code trùng lặp
- Bảo mật nhiều lớp (JWT, Permission, CAPTCHA, Token Blacklist)
- GIS integration cho dữ liệu dịch tễ trên bản đồ
- Dashboard trực quan, đa dạng biểu đồ
- Đa ngôn ngữ, hỗ trợ theme sáng/tối

**Nếu làm lại, em sẽ thay đổi:**
1. **Implement refresh token đầy đủ** — giảm thời hạn access token xuống 15 phút
2. **Thêm WebSocket/SSE** cho real-time notification khi có ổ dịch mới
3. **Viết unit test** đầy đủ hơn (coverage > 70%)
4. **Fix XSS** trong map popup và CKEditor config
5. **Lưu JWT trong HttpOnly Cookie** thay vì localStorage
6. **Implement offline sync** với Dexie.js đầy đủ
7. **Thêm CI/CD pipeline** với GitHub Actions
8. **Dùng Docker Compose** cho toàn bộ stack (bao gồm MongoDB, Redis)

---

## Lời khuyên khi bảo vệ

### Câu hỏi thường được hỏi nhiều nhất (Mức cơ bản)

> 1. **Kiến trúc tổng thể** (Câu 4) - Phải vẽ được sơ đồ kiến trúc
> 2. **Authentication/Authorization** (Câu 21, 22) - Giải thích rõ flow JWT
> 3. **Design Pattern** (Câu 35) - Nắm vững pattern nào dùng ở đâu
> 4. **Tại sao chọn công nghệ X** (Câu 5, 6, 7) - So sánh ưu/nhược điểm
> 5. **Hạn chế & hướng phát triển** (Câu 41) - Thể hiện sự trung thực và hiểu biết

### Câu hỏi thầy cô khó tính sẽ hỏi (Mức nâng cao)

> 1. **Lỗ hổng XSS cụ thể** (Câu 49, 51, 57) - Biết rõ vị trí lỗ hổng VÀ cách sửa
> 2. **JWT storage & refresh token** (Câu 47, 52, 68) - Biết rõ hạn chế và giải pháp
> 3. **Race condition** (Câu 52) - Giải thích được cơ chế queue trong interceptor
> 4. **NoSQL Injection / ReDoS** (Câu 48) - Biết regex dùng trực tiếp từ user input
> 5. **Soft Delete pitfall** (Câu 59) - Biết `findById` không kiểm tra isDeleted
> 6. **Khi nào hệ thống fail** (Câu 71, 72, 73) - Redis chết, server crash, upload lớn
> 7. **Câu cuối cùng** (Câu 76) - Luôn chuẩn bị sẵn "nếu làm lại sẽ thay đổi gì"

### Nguyên tắc vàng khi bảo vệ

> - **Thà nói "em chưa implement" còn hơn nói sai** — thầy cô đánh giá cao sự trung thực
> - **Biết lỗ hổng = biết cách sửa** — nếu tự chỉ ra được hạn chế VÀ giải pháp → điểm cao
> - **Đừng thuộc lòng** — hiểu nguyên lý để trả lời linh hoạt khi bị hỏi xoáy
> - **Vẽ sơ đồ** — khi giải thích kiến trúc/flow, vẽ lên bảng sẽ thuyết phục hơn nói miệng
