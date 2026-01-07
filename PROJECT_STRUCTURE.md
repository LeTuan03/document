# E-Dengue System - Project Structure

## 1. Tổng quan dự án

### Mục đích
Hệ thống giám sát và quản lý dịch bệnh sốt xuất huyết (Dengue Fever) tại Việt Nam, bao gồm:
- Quản lý ca bệnh và bệnh nhân
- Theo dõi ổ dịch và dự báo dịch bệnh
- Kiểm soát vector truyền bệnh (khảo sát lăng quăng, phun thuốc)
- Báo cáo và phân tích thống kê
- Quản lý người dùng và phân quyền

### Công nghệ sử dụng

**Backend:**
- Spring Boot 3.4.2 (Java 17)
- MongoDB (Spring Data MongoDB)
- Spring Security + JWT Authentication
- MapStruct 1.6.3 (Entity-DTO mapping)
- SpringDoc OpenAPI (Swagger documentation)
- Mongock 5.5.0 (Database migration)
- SeaweedFS (Distributed file storage)
- Apache POI + iText PDF + Docx4j (Export documents)
- Redis + Redisson 3.37.0 (Caching)

**Frontend:**
- React 18.3.1 + TypeScript 5.7.3
- Redux (@reduxjs/toolkit)
- React Router 7.1.5
- Axios 1.7.9
- Bootstrap 5 + Reactstrap

**DevOps:**
- Maven (Build tool)
- Webpack 5 (Frontend bundler)
- JHipster 8.9.0 (Project generator)
- Docker support

### Kiến trúc tổng thể

```
┌─────────────────────────────────────────────────┐
│          React Frontend (Port 5173)            │
│    Redux Store + React Router + Axios          │
└──────────────────┬──────────────────────────────┘
                   │ REST API (JSON)
┌──────────────────▼──────────────────────────────┐
│     Spring Boot Backend (Port 8080)            │
│  ┌─────────────────────────────────────────┐   │
│  │  Security Layer (JWT + Spring Security) │   │
│  └─────────────────┬───────────────────────┘   │
│  ┌─────────────────▼───────────────────────┐   │
│  │    REST Controllers (ApiResource)       │   │
│  └─────────────────┬───────────────────────┘   │
│  ┌─────────────────▼───────────────────────┐   │
│  │      Service Layer (Business Logic)     │   │
│  └─────────────────┬───────────────────────┘   │
│  ┌─────────────────▼───────────────────────┐   │
│  │    Repository Layer (Spring Data)       │   │
│  └─────────────────┬───────────────────────┘   │
└────────────────────┼───────────────────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
   ┌─────────┐            ┌──────────────┐
   │ MongoDB │            │  SeaweedFS   │
   └─────────┘            └──────────────┘
```

**Kiến trúc:** Layered Architecture + Modular Monolith
- **Presentation Layer:** REST API Controllers
- **Service Layer:** Business logic, validation, orchestration
- **Data Access Layer:** MongoDB repositories
- **Core Framework:** Reusable base classes và utilities

---

## 2. Cấu trúc thư mục

### Directory Tree

```
back-end/
├── src/
│   ├── main/
│   │   ├── java/com/ord/
│   │   │   ├── core/                    # Framework core
│   │   │   │   ├── api/                 # Base API utilities
│   │   │   │   ├── auth/                # Authentication & Authorization
│   │   │   │   ├── crud/                # Generic CRUD base classes
│   │   │   │   ├── dao/                 # Data access utilities
│   │   │   │   ├── dto/                 # Common DTOs
│   │   │   │   ├── entity/              # Base entities (Audit, FullAudit)
│   │   │   │   ├── factory/             # Service locator (AppFactory)
│   │   │   │   └── utils/               # Utility classes
│   │   │   │
│   │   │   ├── myapp/                   # Application configuration
│   │   │   │   ├── config/              # Spring configuration classes
│   │   │   │   ├── security/            # Security config (JWT, CORS)
│   │   │   │   ├── web/                 # Web filters, interceptors
│   │   │   │   └── EDengueApp.java      # Main entry point
│   │   │   │
│   │   │   └── modules/                 # Feature modules
│   │   │       ├── admin/               # User, Role, Authority management
│   │   │       │   ├── domain/          # Entities (UserEntity, RoleEntity)
│   │   │       │   ├── repository/      # MongoDB repositories
│   │   │       │   ├── service/         # Business logic services
│   │   │       │   └── web/rest/        # REST API endpoints
│   │   │       │
│   │   │       ├── application/         # Main business features
│   │   │       │   ├── dengue/          # Dengue case management
│   │   │       │   ├── vector/          # Vector control (larvae, spraying)
│   │   │       │   ├── outbreak/        # Outbreak tracking
│   │   │       │   ├── forecast/        # Forecasting & risk assessment
│   │   │       │   ├── report/          # Reports & analytics
│   │   │       │   ├── media/           # Posts, feedback, documents
│   │   │       │   └── file/            # File upload/download
│   │   │       │
│   │   │       ├── masterData/          # Reference data
│   │   │       │   ├── province/        # Province data
│   │   │       │   ├── district/        # District data
│   │   │       │   ├── commune/         # Commune data
│   │   │       │   ├── hamlet/          # Hamlet data
│   │   │       │   └── dictionary/      # System dictionaries
│   │   │       │
│   │   │       └── mobile/              # Mobile-specific APIs
│   │   │
│   │   ├── resources/
│   │   │   ├── config/
│   │   │   │   ├── application.yml              # Base config
│   │   │   │   ├── application-dev.yml          # Development
│   │   │   │   └── application-prod.yml         # Production
│   │   │   ├── i18n/                            # Internationalization
│   │   │   └── templates/                       # Export templates (JXLS)
│   │   │
│   │   └── webapp/                              # React frontend
│   │       ├── app/
│   │       │   ├── config/                      # Redux store, axios
│   │       │   ├── entities/                    # Feature components
│   │       │   ├── modules/                     # Account, admin modules
│   │       │   ├── shared/                      # Reusable components
│   │       │   └── app.tsx                      # Root component
│   │       ├── index.html
│   │       └── index.tsx                        # Frontend entry
│   │
│   └── test/java/                               # Unit & integration tests
│
├── pom.xml                                      # Maven configuration
├── package.json                                 # NPM dependencies
├── webpack/                                     # Webpack configs
├── tsconfig.json                               # TypeScript config
└── .yo-rc.json                                 # JHipster metadata
```

### Giải thích vai trò từng thư mục

| Thư mục | Vai trò |
|---------|---------|
| **core/** | Framework tái sử dụng: base CRUD classes, authentication, DTOs, utilities |
| **myapp/** | Configuration layer: Spring configs, security, web filters |
| **modules/admin/** | Quản lý người dùng, vai trò, phân quyền, email, notifications |
| **modules/application/** | Business logic chính: ca bệnh, ổ dịch, vector, dự báo, báo cáo |
| **modules/masterData/** | Dữ liệu tham chiếu: địa giới hành chính, dictionaries |
| **modules/mobile/** | API endpoints dành cho mobile app |
| **resources/config/** | YAML configuration files cho các môi trường khác nhau |
| **webapp/app/** | React frontend: components, Redux store, routing |
| **test/** | Unit tests và integration tests |

---

## 3. Các thành phần quan trọng

### Entry Point

**Backend:**
```java
com.ord.myapp.EDengueApp
```
- Main class với `@SpringBootApplication`
- Khởi động Spring Boot trên port `8080`
- Enable Mongock migrations, scheduling

**Frontend:**
```typescript
src/main/webapp/index.tsx
```
- React root entry point
- Render `<App />` component vào DOM
- Setup Redux Provider

### Business Logic

**Pattern:** Entity → Repository → Service → Controller

**Ví dụ: Quản lý ca bệnh**
```
DetailCaseEntity (domain/dengue/)
    ↓
DetailCaseEntityRepository (repository/)
    ↓
DetailCaseService (service/)
    ↓
DetailCaseApiResource (web/rest/)
```

**Core Services:**
- **UserService:** Quản lý user, authentication
- **DetailCaseService:** Quản lý chi tiết ca bệnh
- **OutbreakService:** Theo dõi ổ dịch
- **LarvaeSurveyHouseholdService:** Khảo sát lăng quăng
- **ReportCaseSummaryService:** Báo cáo tổng hợp ca bệnh
- **ForecastInformationService:** Dự báo dịch bệnh
- **RiskAssessmentsService:** Đánh giá rủi ro

### Data Access

**Base Repository:**
```java
com.ord.core.repository.OrdEntityRepository<T, ID>
```
- Extend từ `MongoRepository`
- Custom query methods
- Generic cho tất cả entities

**Advanced Search:**
```java
com.ord.core.dao.advSearch.AdvSearchHelp
```
- Build complex MongoDB queries
- Support dynamic criteria (CriteriaBuilder)
- Pagination và sorting

**Main Repositories:**
- `UserRepository` - User entities
- `DetailCaseEntityRepository` - Dengue cases
- `OutbreakEntityRepository` - Outbreaks
- `ProvinceEntityRepository` - Provinces
- `DistrictEntityRepository` - Districts

### API / UI Layer

**REST Controllers (Backend):**
- Extend `BaseCrudApi<T, K, R, D, G, F, S>` (generic CRUD)
- Endpoint pattern: `/api/{module}/{resource}/{action}`
- Return `CommonResultDto` hoặc `BasePagedResultDto`

**Main API Groups:**
```
/api/authenticate              # Authentication
/api/user/**                   # User management
/api/detail-case/**            # Dengue cases
/api/outbreak/**               # Outbreaks
/api/larvae/**                 # Larvae surveys
/api/forecast/**               # Forecasting
/api/report-summary/**         # Reports
/api/master-data/**            # Reference data
/api/mobile/**                 # Mobile APIs
```

**Frontend Components (React):**
- Redux store: `src/main/webapp/app/config/store.ts`
- API client: Axios với interceptors
- Routing: React Router trong `app/routes.tsx`

### Config & Environment

**Configuration Files:**
```yaml
src/main/resources/config/
├── application.yml           # Base (common settings)
├── application-dev.yml       # Development
└── application-prod.yml      # Production
```

**Key Configurations:**
- **Database:** MongoDB connection (default: `localhost:27017`)
- **JWT:** Secret key, token validity (24h)
- **CORS:** Allowed origins cho development
- **SeaweedFS:** Master (9333), Volume (8081), Filer (8888)
- **Email:** Gmail SMTP settings
- **Redis:** Cache configuration

**Environment Variables:**
- `SPRING_PROFILES_ACTIVE`: Active profile (dev/prod)
- Database connection strings
- JWT secret key (production)

---

## 4. Luồng xử lý chính

### Request Flow (HTTP → Response)

```
1. HTTP Request từ client
       ↓
2. Spring Security Filter Chain
   - JWT Token validation (JwtService)
   - Authentication check
       ↓
3. UserActivityInterceptor
   - Log user activity vào UserLogEntity
       ↓
4. Controller Layer (ApiResource)
   - Validate request
   - Parse params/body
       ↓
5. Service Layer
   - Business logic processing
   - Data validation
   - Transaction handling
       ↓
6. Repository Layer
   - MongoDB queries (MongoTemplate)
   - CRUD operations
       ↓
7. Response Mapping
   - Entity → DTO (ModelMapper/MapStruct)
   - Wrap trong CommonResultDto
       ↓
8. JSON Response to client
```

### Authentication Flow

```
1. POST /api/authenticate
   - Username + password
       ↓
2. UserDetailsService
   - Load user từ MongoDB
   - Verify password (BCrypt)
       ↓
3. JwtService.createToken()
   - Generate JWT token (24h validity)
   - Encode user info + authorities
       ↓
4. Return token to client
       ↓
5. Client stores token (localStorage/sessionStorage)
       ↓
6. Subsequent requests
   - Header: Authorization: Bearer <token>
       ↓
7. JwtFilter validates token
   - Parse claims
   - Set SecurityContext
       ↓
8. PermissionInterceptor
   - Check method-level permissions (@Permission)
   - Allow/Deny request
```

### Data Flow: Tạo ca bệnh mới

```
1. User input form (React)
       ↓
2. Redux action dispatch
   - createEntity(detailCase)
       ↓
3. Axios POST /api/detail-case
   - JSON payload
       ↓
4. DetailCaseApiResource.create()
   - Validate DTO
       ↓
5. DetailCaseService.save()
   - Business validation
   - Set audit fields (createdBy, createdDate)
   - Link PatientEntity if exists
       ↓
6. Repository.save()
   - Insert MongoDB document
       ↓
7. Trigger background jobs
   - Update report summaries
   - Recalculate risk assessments
       ↓
8. Return success response
       ↓
9. Update Redux state
   - Refresh entity list
       ↓
10. Notification to user
```

### File Upload Flow

```
1. User selects file (React FileUpload)
       ↓
2. POST /api/file/upload
   - Multipart form data
       ↓
3. FileAppService.upload()
       ↓
4. UploadFileToCacheService
   - Save to SeaweedFS cluster
   - Master assigns volume server
   - Store file on volume server
       ↓
5. Return file URL + metadata
   - fileId, fileName, fileSize, url
       ↓
6. Store fileId in entity
   - DetailCaseEntity.attachedFiles
       ↓
7. Download: GET /api/file/download/{fileId}
   - Retrieve from SeaweedFS
   - Stream to client
```

### Report Generation Flow

```
1. User clicks "Export Excel"
       ↓
2. GET /api/report-summary/export?criteria=...
       ↓
3. Service layer
   - Query data với criteria
   - Aggregate results
       ↓
4. ExcelExportService
   - Load JXLS template (resources/templates/)
   - Populate data vào template
   - Apache POI generates Excel
       ↓
5. Return file stream
   - Content-Type: application/vnd.ms-excel
   - Content-Disposition: attachment
       ↓
6. Browser downloads file
```

---

## 5. Ghi chú kỹ thuật

### Coding Conventions

**Java:**
- **Package naming:** `com.ord.{layer}.{module}.{component}`
- **Class naming:**
  - Entities: `{Name}Entity`
  - DTOs: `{Name}Dto`
  - Services: `{Name}Service`
  - Controllers: `{Name}ApiResource`
- **Variable naming:** camelCase
- **Constants:** UPPER_SNAKE_CASE
- **Lombok:** Sử dụng `@Data`, `@Builder`, `@Slf4j`
- **Annotations order:** Class-level → Field-level → Method-level

**TypeScript/React:**
- **Component naming:** PascalCase (e.g., `DetailCaseList.tsx`)
- **File naming:** kebab-case hoặc PascalCase
- **Hooks:** camelCase với prefix `use` (e.g., `useAppDispatch`)
- **Props interface:** `I{ComponentName}Props`
- **Redux slices:** Feature-based trong `app/entities/`

### Điểm cần chú ý khi maintain

**1. Database Migrations (Mongock):**
- Tạo migration class trong `com.ord.myapp.dbmigrations`
- Naming: `V{version}__{description}.java`
- Luôn test migration trên dev trước khi deploy

**2. Security:**
- JWT secret PHẢI thay đổi trong production
- KHÔNG commit credentials vào git
- Sử dụng `@Permission` annotation cho method-level security
- Public endpoints phải được khai báo trong `SecurityConfiguration`

**3. Performance:**
- MongoDB queries: Tạo index cho các field thường query
- Pagination: Luôn sử dụng `Pageable` cho list queries
- Caching: Redis cache cho master data (Province, District)
- File upload: Limit file size (default 10MB)

**4. Error Handling:**
- Custom exceptions trong `com.ord.myapp.web.rest.errors`
- Global exception handler: `ExceptionTranslator`
- Return meaningful error messages (i18n supported)

**5. Testing:**
- Unit tests cho Service layer
- Integration tests cho API endpoints
- Mock MongoDB với `@DataMongoTest`
- Test security với `@WithMockUser`

**6. Logging:**
- Sử dụng SLF4J (`@Slf4j`)
- Log levels: ERROR (production), DEBUG (development)
- User activity logged tự động qua `UserActivityInterceptor`

**7. API Versioning:**
- Hiện tại: No versioning (v1 implicit)
- Nếu thêm breaking changes: Tạo `/api/v2/...`

**8. Frontend State Management:**
- Redux Toolkit cho global state
- Local state (useState) cho component-specific data
- Async actions với `createAsyncThunk`

**9. Build & Deploy:**
- Development: `./mvnw` + `npm start`
- Production: `./mvnw -Pprod clean verify`
- Docker: `npm run java:docker`
- Health check: `GET /management/health`

**10. Common Issues:**
- **MongoDB connection timeout:** Check MongoDB service running
- **JWT expired:** Token validity = 24h, refresh mechanism needed
- **CORS errors:** Add origin to `application-dev.yml`
- **File upload fails:** Check SeaweedFS cluster status
- **Redis connection:** Ensure Redis server running on localhost:6379

---

## Quick Start

### Development Setup

```bash
# 1. Start MongoDB
mongod --dbpath /path/to/data

# 2. Start Redis (optional, for caching)
redis-server

# 3. Start SeaweedFS (for file storage)
weed master
weed volume -port=8081
weed filer -port=8888

# 4. Backend (Terminal 1)
./mvnw

# 5. Frontend (Terminal 2)
npm start

# 6. Access
# Frontend: http://localhost:5173
# Backend API: http://localhost:8080
# Swagger UI: http://localhost:8080/swagger-ui.html
```

### Production Build

```bash
# Build optimized JAR
./mvnw -Pprod clean verify

# Run JAR
java -jar target/*.jar --spring.profiles.active=prod

# Build Docker image
npm run java:docker
docker run -p 8080:8080 edengue:latest
```

---

## Tài liệu tham khảo

- **JHipster Documentation:** https://www.jhipster.tech
- **Spring Boot:** https://spring.io/projects/spring-boot
- **MongoDB:** https://www.mongodb.com/docs
- **React + Redux:** https://react-redux.js.org
- **API Documentation:** http://localhost:8080/swagger-ui.html (khi chạy dev)

---

**Lưu ý:** Tài liệu này được tạo tự động bởi phân tích source code. Cần cập nhật khi có thay đổi lớn về architecture hoặc thêm modules mới.
