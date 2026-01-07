# Tài Liệu Kỹ Thuật GIS - Hệ Thống Giám Sát Sốt Xuất Huyết EDengue

## Mục Lục

1. [Tổng Quan Hệ Thống](#1-tổng-quan-hệ-thống)
2. [Cấu Trúc Dữ Liệu GIS](#2-cấu-trúc-dữ-liệu-gis)
3. [Luồng Dữ Liệu GIS](#3-luồng-dữ-liệu-gis)
4. [Các Hàm Nghiệp Vụ GIS](#4-các-hàm-nghiệp-vụ-gis)
5. [API Endpoints](#5-api-endpoints)
6. [Công Nghệ và Thư Viện](#6-công-nghệ-và-thư-viện)
7. [Database Schema](#7-database-schema)
8. [Best Practices](#8-best-practices)

---

## 1. Tổng Quan Hệ Thống

### 1.1. Giới Thiệu

Hệ thống EDengue là một ứng dụng giám sát và quản lý dịch bệnh sốt xuất huyết, sử dụng công nghệ GIS (Geographic Information System) để:
- Theo dõi vị trí địa lý của các ca bệnh
- Hiển thị ổ dịch trên bản đồ số
- Phân tích và dự báo nguy cơ dịch bệnh theo khu vực
- Quản lý ranh giới hành chính đa cấp (Tỉnh/TP, Quận/Huyện, Xã/Phường, Thôn/Ấp)

### 1.2. Công Nghệ Sử Dụng

| Công nghệ | Mục đích |
|-----------|----------|
| **Spring Data MongoDB** | Quản lý dữ liệu GIS với hỗ trợ geospatial |
| **GeoJSON** | Định dạng chuẩn cho dữ liệu địa lý |
| **MongoDB 2D Sphere Index** | Tối ưu hóa truy vấn không gian |
| **BigDecimal** | Lưu trữ tọa độ với độ chính xác cao |

### 1.3. Kiến Trúc Hệ Thống

```
┌─────────────────────────────────────────────────────────────┐
│                     Frontend (Map Client)                    │
│              (Leaflet, Google Maps, OpenLayers)             │
└──────────────────────┬──────────────────────────────────────┘
                       │ REST API (GeoJSON)
┌──────────────────────▼──────────────────────────────────────┐
│                    API Layer                                │
│  - InformationOutbreakApiResource                           │
│  - ForecastInformationApiResource                           │
│  - ProvinceApiResource                                      │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                  Service Layer                              │
│  - OutbreakService                                          │
│  - ReportCaseSummaryService                                 │
│  - GeoJsonUtlis                                             │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                Repository Layer                             │
│  - ProvinceEntityRepository                                 │
│  - DistrictEntityRepository                                 │
│  - CommuneEntityRepository                                  │
│  - HamletEntityRepository                                   │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│              MongoDB (Geospatial Database)                  │
│         Collections: provinces_new, districts_new,          │
│          communes_new, hamlets_new, outbreaks, etc.         │
└─────────────────────────────────────────────────────────────┘
```

---

## 2. Cấu Trúc Dữ Liệu GIS

### 2.1. Các Loại Geometry

Hệ thống sử dụng 3 loại geometry chính theo chuẩn GeoJSON:

#### 2.1.1. GeoJsonPoint (Điểm)
**Sử dụng cho**: Vị trí cụ thể (thôn/ấp, ca bệnh, ổ dịch)

```java
// Cấu trúc
public class GeoJsonPoint {
    private double x; // Kinh độ (Longitude)
    private double y; // Vĩ độ (Latitude)
}

// Ví dụ JSON
{
    "type": "Point",
    "coordinates": [106.6297, 10.8231]  // [longitude, latitude]
}
```

**Entity sử dụng**:
- `HamletEntity` - Vị trí thôn/ấp
- `DetailCaseEntity` - Vị trí ca bệnh
- `OutbreakEntity` - Vị trí ổ dịch

#### 2.1.2. GeoJsonPolygon (Đa giác)
**Sử dụng cho**: Ranh giới hành chính đơn giản

```java
// Cấu trúc
public class GeoJsonPolygon {
    private List<GeoJsonLineString> coordinates;
    // GeoJsonLineString chứa List<Point>
}

// Ví dụ JSON
{
    "type": "Polygon",
    "coordinates": [
        [
            [106.0, 10.0],
            [106.5, 10.0],
            [106.5, 10.5],
            [106.0, 10.5],
            [106.0, 10.0]  // Điểm đầu = Điểm cuối (vòng kín)
        ]
    ]
}
```

**Entity sử dụng**:
- `ProvinceEntity` - Ranh giới tỉnh/thành phố
- `DistrictEntity` - Ranh giới quận/huyện
- `CommuneEntity` - Ranh giới xã/phường

#### 2.1.3. GeoJsonMultiPolygon (Nhiều đa giác)
**Sử dụng cho**: Ranh giới hành chính phức tạp (đảo, vùng không liên tục)

```java
// Cấu trúc
public class GeoJsonMultiPolygon {
    private List<GeoJsonPolygon> coordinates;
}

// Ví dụ JSON
{
    "type": "MultiPolygon",
    "coordinates": [
        [  // Polygon 1
            [[106.0, 10.0], [106.5, 10.0], [106.5, 10.5], [106.0, 10.0]]
        ],
        [  // Polygon 2
            [[107.0, 11.0], [107.5, 11.0], [107.5, 11.5], [107.0, 11.0]]
        ]
    ]
}
```

**Entity sử dụng**:
- `DistrictEntity` - Quận/huyện có nhiều vùng
- `CommuneEntity` - Xã/phường có nhiều vùng

### 2.2. Entity Models

#### 2.2.1. ProvinceEntity (Tỉnh/Thành phố)

**File**: `src/main/java/com/ord/modules/masterData/domain/ProvinceEntity.java`

```java
@Document(collection = "provinces_new")
public class ProvinceEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPolygon geometry;          // Ranh giới tỉnh
    private BigDecimal latitude;              // Vĩ độ trung tâm
    private BigDecimal longitude;             // Kinh độ trung tâm

    // Administrative Data
    @NotBlank
    private String code;                      // Mã tỉnh (VD: "01", "79")
    @NotBlank
    private String name;                      // Tên tỉnh (VD: "Hà Nội")
    private String nameEn;                    // Tên tiếng Anh
    @NotBlank
    private String fullName;                  // Tên đầy đủ (VD: "Thành phố Hà Nội")
    private String fullNameEn;                // Tên đầy đủ tiếng Anh
    @NotBlank
    private String codeName;                  // Mã tên (VD: "ha_noi")

    // Population Data
    private Integer totalHousehold;           // Tổng số hộ dân
    private Integer totalPopulation;          // Tổng dân số
    private BigDecimal populationDensity;     // Mật độ dân số

    // Relationships
    private Integer administrativeUnitId;     // ID cấp hành chính
    private Integer administrativeRegionId;   // ID khu vực hành chính

    // Feature Collection
    private TypeFeature type = TypeFeature.Feature;  // GeoJSON type
}
```

**Index**: 2D Sphere index trên trường `geometry` để tối ưu hóa các truy vấn không gian như:
- Tìm tỉnh chứa một điểm
- Tìm các tỉnh trong phạm vi
- Tính khoảng cách địa lý

#### 2.2.2. DistrictEntity (Quận/Huyện)

**File**: `src/main/java/com/ord/modules/masterData/domain/DistrictEntity.java`

```java
@Document(collection = "districts_new")
public class DistrictEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data - Hỗ trợ cả Polygon và MultiPolygon
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPolygon geometry;           // Ranh giới đơn giản

    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonMultiPolygon geometryMulti; // Ranh giới phức tạp

    private BigDecimal latitude;
    private BigDecimal longitude;

    // Administrative Data
    private String code;                       // Mã quận/huyện
    private String name;                       // Tên quận/huyện
    private String fullName;                   // Tên đầy đủ
    private String codeName;                   // Mã tên

    // Parent Relationship
    private String provinceCode;               // Mã tỉnh cha
    private String provinceName;               // Tên tỉnh cha

    // Feature Collection
    private TypeFeature type = TypeFeature.Feature;
}
```

**Đặc biệt**: Có cả 2 trường `geometry` và `geometryMulti` để xử lý:
- Quận/huyện có ranh giới liên tục → dùng `geometry`
- Quận/huyện có nhiều vùng không liên tục → dùng `geometryMulti`

#### 2.2.3. CommuneEntity (Xã/Phường)

**File**: `src/main/java/com/ord/modules/masterData/domain/CommuneEntity.java`

```java
@Document(collection = "communes_new")
public class CommuneEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPolygon geometry;

    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonMultiPolygon geometryMulti;

    private BigDecimal latitude;
    private BigDecimal longitude;

    // Administrative Data
    private String code;
    private String name;
    private String fullName;
    private String codeName;

    // Parent Relationships
    private String districtCode;
    private String districtName;
    private String provinceCode;
    private String provinceName;

    private TypeFeature type = TypeFeature.Feature;
}
```

#### 2.2.4. HamletEntity (Thôn/Ấp)

**File**: `src/main/java/com/ord/modules/masterData/domain/HamletEntity.java`

```java
@Document(collection = "hamlets_new")
public class HamletEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data - Chỉ dùng Point vì thôn/ấp không có ranh giới rõ ràng
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPoint geometry;             // Vị trí trung tâm thôn/ấp

    private BigDecimal latitude;               // Vĩ độ
    private BigDecimal longitude;              // Kinh độ

    // Administrative Data
    private String code;
    private String name;
    private String fullName;

    // Parent Relationships
    private String communeId;
    private String communeName;
    private String districtId;
    private String districtName;
    private String provinceId;
    private String provinceName;

    private TypeFeature type = TypeFeature.Feature;
}
```

#### 2.2.5. OutbreakEntity (Ổ Dịch)

**File**: `src/main/java/com/ord/modules/application/domain/outbreak/OutbreakEntity.java`

```java
@Document(collection = "outbreaks")
public class OutbreakEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPoint geometry;             // Vị trí ổ dịch

    // Outbreak Information
    private String name;                       // Tên ổ dịch
    private LocalDateTime detectionDate;       // Ngày phát hiện
    private StatusOutbreakEnum status;         // Trạng thái (DETECTING, HANDLING, COMPLETED)

    // Location Information
    private String hamletId;                   // ID thôn/ấp
    private String hamletName;
    private String communeId;
    private String communeName;
    private String districtId;
    private String districtName;
    private String provinceId;
    private String provinceName;

    // Statistics
    private Integer totalCases;                // Tổng số ca bệnh
    private Integer totalDeaths;               // Tổng số ca tử vong
}
```

#### 2.2.6. DetailCaseEntity (Ca Bệnh Chi Tiết)

**File**: `src/main/java/com/ord/modules/application/domain/dengueCase/detailCase/DetailCaseEntity.java`

```java
@Document(collection = "detail_cases")
public class DetailCaseEntity extends FullAuditEntity<String> {
    @Id
    private String id;

    // GIS Data
    @GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
    private GeoJsonPoint geometry;             // Vị trí ca bệnh

    // Patient Information
    private String patientName;                // Tên bệnh nhân
    private LocalDate dateOfBirth;             // Ngày sinh
    private String gender;                     // Giới tính
    private String address;                    // Địa chỉ chi tiết

    // Medical Information
    private LocalDate onsetDate;               // Ngày khởi phát
    private LocalDate diagnosisDate;           // Ngày chẩn đoán
    private String classification;             // Phân loại (DF, DHF, DSS)

    // Location Information
    private String hamletId;
    private String communeId;
    private String districtId;
    private String provinceId;
}
```

### 2.3. DTO Models

#### 2.3.1. FeatureDto

**File**: `src/main/java/com/ord/modules/application/service/dto/share/FeatureDto.java`

```java
@Data
@AllArgsConstructor
public class FeatureDto {
    private TypeFeature type;                      // "Feature"
    private GeoJsonPoint geometryPoint;            // Geometry dạng Point
    private GeoJsonMultiPolygon geometryMultiPolygon; // Geometry dạng MultiPolygon
    private GeoJsonPolygon geometryPolygon;        // Geometry dạng Polygon
    private FeatureProperties properties;          // Thuộc tính của feature
}
```

**Mục đích**: DTO chuẩn GeoJSON Feature để trả về API, hỗ trợ đa dạng loại geometry.

#### 2.3.2. FeatureProperties

**File**: `src/main/java/com/ord/modules/application/service/dto/share/FeatureProperties.java`

```java
@Data
public class FeatureProperties {
    private String address;                    // Địa chỉ
    private String name;                       // Tên
    private String code;                       // Mã
    private Integer detection;                 // Số ca phát hiện
    private Object data;                       // Dữ liệu tùy chỉnh
}
```

#### 2.3.3. BoundaryGeometryDto

**File**: `src/main/java/com/ord/modules/masterData/service/dto/BoundaryGeometryDto.java`

```java
@JsonIgnoreProperties(ignoreUnknown = true)
public class BoundaryGeometryDto {
    @JsonProperty("type")
    private String type;                       // "Polygon" hoặc "MultiPolygon"

    private List<?> coordinates;               // Mảng tọa độ

    // Chuyển đổi từ JSON coordinates sang Spring Data GeoJSON
    public Object toGeoJsonObject() {
        if ("Polygon".equalsIgnoreCase(type)) {
            return convertToGeoJsonPolygon((List<List<List<Double>>>) coordinates);
        } else if ("MultiPolygon".equalsIgnoreCase(type)) {
            return convertToGeoJsonMultiPolygon((List<List<List<List<Double>>>>) coordinates);
        }
        throw new IllegalArgumentException("Unsupported geometry type: " + type);
    }

    private GeoJsonPolygon convertToGeoJsonPolygon(List<List<List<Double>>> polygonCoords) {
        List<Point> points = polygonCoords.get(0).stream()
            .map(coord -> new Point(coord.get(0), coord.get(1)))
            .collect(Collectors.toList());
        return new GeoJsonPolygon(points);
    }

    private GeoJsonMultiPolygon convertToGeoJsonMultiPolygon(
            List<List<List<List<Double>>>> multiPolygonCoords) {
        List<GeoJsonPolygon> polygons = multiPolygonCoords.stream()
            .map(this::convertToGeoJsonPolygon)
            .collect(Collectors.toList());
        return new GeoJsonMultiPolygon(polygons);
    }
}
```

**Mục đích**: Deserialize JSON boundary từ file GeoJSON upload và convert sang Spring Data MongoDB GeoJSON objects.

#### 2.3.4. InformationDigitalMapDto

**File**: `src/main/java/com/ord/modules/application/service/dto/share/InformationDigitalMapDto.java`

```java
@Data
public class InformationDigitalMapDto {
    private List<FeatureDto> listGeoJsonPoint;     // Danh sách feature dạng Point
    private List<FeatureDto> listGeoJsonPolygon;   // Danh sách feature dạng Polygon/MultiPolygon
}
```

**Mục đích**: Tách riêng Point features và Polygon features để frontend render khác nhau (markers vs shapes).

---

## 3. Luồng Dữ Liệu GIS

### 3.1. Import Boundary Data (Nhập Dữ Liệu Ranh Giới)

**Flow**: File GeoJSON → API → DTO → Entity → MongoDB

```
┌─────────────────┐
│  GeoJSON File   │
│  (Upload)       │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  POST /api/master-data/province/            │
│       import-boundary-lv2                   │
│  (ProvinceApiResource.java)                 │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  ObjectMapper.readValue()                   │
│  → List<BoundaryDto>                        │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  BoundaryGeometryDto.toGeoJsonObject()      │
│  Converts coordinates to:                   │
│  - GeoJsonPolygon                           │
│  - GeoJsonMultiPolygon                      │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Match by code and update Entity:           │
│  - entity.setGeometry(polygon)              │
│  - entity.setGeometryMulti(multiPolygon)    │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  repository.saveAll(entities)               │
│  → MongoDB (2D Sphere Index)                │
└─────────────────────────────────────────────┘
```

**Code Example** (`ProvinceApiResource.java:316-354`):

```java
@PostMapping(path = "/import-boundary-lv2")
public CommonResultDto<Boolean> importBoundaryLv2(@RequestParam("file") MultipartFile file) {
    try {
        // 1. Parse GeoJSON file
        ObjectMapper objectMapper = new ObjectMapper();
        List<BoundaryDto> input = objectMapper.readValue(
            file.getInputStream(), new TypeReference<List<BoundaryDto>>() {}
        );

        // 2. Get existing districts from database
        var districtRepo = appFactory.getService(DistrictEntityRepository.class);
        var communeRepo = appFactory.getService(CommuneEntityRepository.class);
        var lstCommune = communeRepo.findAll();

        var lstUpdate = new ArrayList<CommuneEntity>();

        // 3. Match and update geometry
        for (BoundaryDto item : input) {
            var l2Code = item.getProperties().getL2_code();
            var ent = lstCommune.stream()
                .filter(x -> l2Code.equals(x.getCode()))
                .findFirst().orElse(null);

            if (ent != null) {
                // 4. Convert and set geometry based on type
                if (item.getGeometry().getType().equals("Polygon")) {
                    var geoJson = item.getGeometry().toGeoJsonObject();
                    ent.setGeometry((GeoJsonPolygon) geoJson);
                }
                if (item.getGeometry().getType().equals("MultiPolygon")) {
                    var geoJson = item.getGeometry().toGeoJsonObject();
                    ent.setGeometryMulti((GeoJsonMultiPolygon) geoJson);
                }

                // 5. Update other properties
                ent.setProperties(item.getProperties());
                ent.setFullName(item.getProperties().getL2_name_loc());
                lstUpdate.add(ent);
            }
        }

        // 6. Save to database
        communeRepo.saveAll(lstUpdate);
        return CommonResultDto.success(lstUpdate.size(), "Successful");

    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

### 3.2. Get Digital Map Data (Lấy Dữ Liệu Bản Đồ)

**Flow**: Request → Service → Repository → MongoDB → DTO → GeoJSON Response

```
┌─────────────────┐
│  Frontend Map   │
│  Client         │
└────────┬────────┘
         │ POST /api/outbreak/information/get-digital-map
         │ { provinceId, districtId, communeId, ... }
         ▼
┌─────────────────────────────────────────────┐
│  InformationOutbreakApiResource             │
│  .getPaged(InformationInputPagedDto)        │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  OutbreakService                            │
│  .groupReportOutbreakDetail(input, field)   │
│  → Aggregate outbreak data by hamlet        │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Get hamlet geometry from HamletRepository  │
│  ReportOutbreakDetailDto with geometry      │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Map to FeatureDto:                         │
│  new FeatureDto(                            │
│    TypeFeature.Feature,                     │
│    item.getGeometry(),  // GeoJsonPoint     │
│    null, null,                              │
│    new FeatureProperties(...)               │
│  )                                          │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Get commune/district boundary if needed    │
│  Add polygon features to result             │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  InformationDigitalMapDto                   │
│  {                                          │
│    listGeoJsonPoint: [...],                 │
│    listGeoJsonPolygon: [...]                │
│  }                                          │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────┐
│  JSON Response  │
│  to Frontend    │
└─────────────────┘
```

**Code Example** (`InformationOutbreakApiResource.java:63-98`):

```java
@PostMapping(path = "/get-digital-map")
public InformationDigitalMapDto getPaged(@RequestBody InformationInputPagedDto input) {
    // 1. Update input with user permissions
    input.updateAddressByUser(appFactory.getCurrentUserInformation());

    // 2. Aggregate outbreak data by hamlet
    List<ReportOutbreakDetailDto> items = outbreakService.groupReportOutbreakDetail(input, "hamletId");

    // 3. Map to Point features (outbreak locations)
    var listGeoJsonPoint = items.stream().map(item ->
        new FeatureDto(
            TypeFeature.Feature,
            item.getGeometry(),  // GeoJsonPoint from hamlet
            null, null,
            new FeatureProperties(
                item.getProperties() != null ? item.getProperties().getAddress() : null,
                item.getHamletName(),
                null,
                item.getDetection(),
                item
            )
        )
    ).collect(Collectors.toList());

    // 4. Create result object
    var result = new InformationDigitalMapDto();
    result.setListGeoJsonPoint(listGeoJsonPoint);
    var listGeoJsonPolygon = new ArrayList<FeatureDto>();

    // 5. Add commune boundary if filtering by commune
    if (input.getCommuneId() != null) {
        communeEntityRepository.findById(input.getCommuneId()).ifPresent(communeInfo -> {
            // Calculate totals for this commune
            int totalOutbreakDetection = items.stream()
                .filter(item -> input.getCommuneId().equals(item.getCommuneId()))
                .mapToInt(item -> Objects.requireNonNullElse(item.getDetection(), 0))
                .sum();

            // Add commune polygon
            listGeoJsonPolygon.add(new FeatureDto(
                TypeFeature.Feature,
                null,
                communeInfo.getGeometryMulti(),  // GeoJsonMultiPolygon
                communeInfo.getGeometry(),       // GeoJsonPolygon
                new FeatureProperties(
                    communeInfo.getProperties() != null ? communeInfo.getProperties().getAddress() : null,
                    communeInfo.getFullName(),
                    communeInfo.getCode(),
                    totalOutbreakDetection,
                    new OutbreakProperties(...)
                )
            ));
        });
    }

    result.setListGeoJsonPolygon(listGeoJsonPolygon);
    return result;
}
```

### 3.3. Create Case/Outbreak with Geometry

**Flow**: Case Data → Get Hamlet Coordinates → Set Geometry → Save

```
┌─────────────────┐
│  Create Case    │
│  Request        │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  ReportCaseSummaryServiceImpl               │
│  Lines 1152-1157, 1467-1472                 │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Get hamlet by ID:                          │
│  hamletRepo.findById(hamletId)              │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Extract geometry:                          │
│  if (hamlet.getGeometry() != null) {        │
│    entity.setGeometry(                      │
│      hamlet.getGeometry()                   │
│    );                                       │
│  } else if (hamlet.getLatitude() != null) { │
│    entity.setGeometry(                      │
│      new GeoJsonPoint(                      │
│        hamlet.getLongitude(),               │
│        hamlet.getLatitude()                 │
│      )                                      │
│    );                                       │
│  }                                          │
└────────┬────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│  Save entity to MongoDB                     │
│  → Auto index with 2D Sphere                │
└─────────────────────────────────────────────┘
```

**Code Reference** (`ReportCaseSummaryServiceImpl.java:1467-1472`):

```java
// Set geometry from hamlet
if (hamlet.getGeometry() != null) {
    entity.setGeometry(hamlet.getGeometry());
} else if (hamlet.getLatitude() != null && hamlet.getLongitude() != null) {
    entity.setGeometry(new GeoJsonPoint(
        hamlet.getLongitude().doubleValue(),
        hamlet.getLatitude().doubleValue()
    ));
}
```

### 3.4. Polygon to Point Conversion

**Flow**: GeoJsonPolygon → Calculate Centroid → GeoJsonPoint

**File**: `src/main/java/com/ord/core/utils/GeoJsonUtlis.java`

```java
public static GeoJsonPoint convertPolygonToPoint(GeoJsonPolygon polygon) {
    // 1. Get polygon coordinates
    List<GeoJsonLineString> coordinates = polygon.getCoordinates();

    double sumLat = 0;
    double sumLon = 0;
    int count = 0;

    // 2. Iterate through all points in the first ring (outer boundary)
    var firstItem = coordinates.get(0);
    for (Point coord : firstItem.getCoordinates()) {
        sumLon += coord.getX(); // Longitude
        sumLat += coord.getY(); // Latitude
        count++;
    }

    // 3. Calculate centroid (average)
    double centerLon = sumLon / count;
    double centerLat = sumLat / count;

    // 4. Return center point
    return new GeoJsonPoint(centerLon, centerLat);
}
```

**Use Case**: Khi cần hiển thị marker tại trung tâm của một khu vực (polygon).

---

## 4. Các Hàm Nghiệp Vụ GIS

### 4.1. Spatial Queries (Truy Vấn Không Gian)

#### 4.1.1. Find Within Boundary (Tìm Trong Ranh Giới)

**MongoDB Query Pattern**:

```java
// Tìm tất cả ca bệnh trong một polygon
Criteria criteria = Criteria.where("geometry")
    .within(polygon);  // polygon là GeoJsonPolygon

List<DetailCaseEntity> casesInArea = mongoTemplate.find(
    Query.query(criteria),
    DetailCaseEntity.class
);
```

#### 4.1.2. Near Query (Tìm Gần)

```java
// Tìm các ổ dịch trong bán kính 5km từ một điểm
Point point = new Point(106.6297, 10.8231);
Distance distance = new Distance(5, Metrics.KILOMETERS);

Criteria criteria = Criteria.where("geometry")
    .nearSphere(point)
    .maxDistance(distance.getNormalizedValue());

List<OutbreakEntity> nearbyOutbreaks = mongoTemplate.find(
    Query.query(criteria),
    OutbreakEntity.class
);
```

#### 4.1.3. Intersects Query (Tìm Giao)

```java
// Tìm các quận giao với một vùng
Criteria criteria = Criteria.where("geometry")
    .intersects(targetPolygon);

List<DistrictEntity> intersectingDistricts = mongoTemplate.find(
    Query.query(criteria),
    DistrictEntity.class
);
```

### 4.2. Aggregation Functions (Hàm Tập Hợp)

#### 4.2.1. Group by Administrative Unit (Gom Nhóm Theo Đơn Vị Hành Chính)

**Use Case**: Đếm số ca bệnh theo xã/phường

**Code** (`OutbreakService.groupReportOutbreakDetail`):

```java
public List<ReportOutbreakDetailDto> groupReportOutbreakDetail(
        InformationInputPagedDto input,
        String groupByField) {

    // 1. Build filter criteria
    Criteria criteria = buildCriteria(input);

    // 2. Build aggregation pipeline
    Aggregation aggregation = Aggregation.newAggregation(
        // Match criteria
        Aggregation.match(criteria),

        // Group by field (hamletId, communeId, districtId, provinceId)
        Aggregation.group(groupByField)
            .first(groupByField).as(groupByField)
            .first("hamletName").as("hamletName")
            .first("communeId").as("communeId")
            .first("communeName").as("communeName")
            .first("districtId").as("districtId")
            .first("districtName").as("districtName")
            .first("provinceId").as("provinceId")
            .first("provinceName").as("provinceName")
            .sum("detection").as("detection")      // Tổng số ca phát hiện
            .sum("handle").as("handle"),           // Tổng số ca xử lý

        // Sort
        Aggregation.sort(Sort.Direction.DESC, "detection")
    );

    // 3. Execute aggregation
    AggregationResults<ReportOutbreakDetailDto> results = mongoTemplate.aggregate(
        aggregation,
        "outbreaks",
        ReportOutbreakDetailDto.class
    );

    // 4. Add geometry to results
    List<ReportOutbreakDetailDto> items = results.getMappedResults();
    enrichWithGeometry(items, groupByField);

    return items;
}

private void enrichWithGeometry(List<ReportOutbreakDetailDto> items, String groupByField) {
    if ("hamletId".equals(groupByField)) {
        // Get hamlet geometries
        List<String> hamletIds = items.stream()
            .map(ReportOutbreakDetailDto::getHamletId)
            .filter(Objects::nonNull)
            .toList();

        List<HamletEntity> hamlets = hamletEntityRepository.findByIdIn(hamletIds);
        Map<String, GeoJsonPoint> geometryMap = hamlets.stream()
            .collect(Collectors.toMap(
                HamletEntity::getId,
                HamletEntity::getGeometry
            ));

        // Set geometry to items
        items.forEach(item -> {
            if (item.getHamletId() != null) {
                item.setGeometry(geometryMap.get(item.getHamletId()));
            }
        });
    }
    // Similar logic for commune, district, province...
}
```

#### 4.2.2. Calculate Risk Level by Area (Tính Mức Độ Nguy Cơ Theo Khu Vực)

**File**: `InformationOutbreakApiResource.java`

```java
private OutbreakColorEnum detectColorByCommune(
        String communeId,
        List<ReportOutbreakDetailDto> communeCases,
        List<HamletEntity> hamlets) {

    // Logic: Màu dựa trên số thôn có ổ dịch
    int hamletsWithOutbreak = communeCases.size();
    int totalHamlets = hamlets.size();

    double ratio = (double) hamletsWithOutbreak / totalHamlets;

    if (ratio >= 0.5) {
        return OutbreakColorEnum.RED;     // Nguy cơ cao
    } else if (ratio >= 0.3) {
        return OutbreakColorEnum.ORANGE;  // Nguy cơ trung bình
    } else if (ratio >= 0.1) {
        return OutbreakColorEnum.YELLOW;  // Nguy cơ thấp
    } else {
        return OutbreakColorEnum.GREEN;   // An toàn
    }
}
```

### 4.3. Forecast Functions (Hàm Dự Báo)

#### 4.3.1. Get Risk Forecast Map (Lấy Bản Đồ Dự Báo Nguy Cơ)

**File**: `ForecastInformationApiResource.java`

**API**: `POST /api/forecast/get-risk-forecast`

```java
@PostMapping("/get-risk-forecast")
public RickMapOutputDto getRiskForecast(@RequestBody ForecastInputDto input) {
    // 1. Get forecast data from ML model or calculation
    List<ForecastData> forecasts = forecastService.calculateRisk(input);

    // 2. Get administrative boundaries
    List<String> communeIds = forecasts.stream()
        .map(ForecastData::getCommuneId)
        .toList();

    List<CommuneEntity> communes = communeRepository.findByIdIn(communeIds);

    // 3. Map to GeoJSON features with risk colors
    List<FeatureDto> features = forecasts.stream().map(forecast -> {
        CommuneEntity commune = communes.stream()
            .filter(c -> c.getId().equals(forecast.getCommuneId()))
            .findFirst()
            .orElse(null);

        if (commune == null) return null;

        // Determine risk color based on forecast value
        RiskLevel riskLevel = calculateRiskLevel(forecast.getRiskScore());

        return new FeatureDto(
            TypeFeature.Feature,
            null,
            commune.getGeometryMulti(),
            commune.getGeometry(),
            new ForecastProperties(
                commune.getFullName(),
                commune.getCode(),
                forecast.getRiskScore(),
                riskLevel.getColor(),
                forecast.getPredictedCases()
            )
        );
    })
    .filter(Objects::nonNull)
    .toList();

    return new RickMapOutputDto(features);
}
```

### 4.4. Data Validation Functions (Hàm Kiểm Tra Dữ Liệu)

#### 4.4.1. Validate Coordinates (Kiểm Tra Tọa Độ)

```java
public boolean isValidCoordinate(BigDecimal latitude, BigDecimal longitude) {
    if (latitude == null || longitude == null) {
        return false;
    }

    // Vietnam boundaries (approximate)
    // Latitude: 8.5°N to 23.5°N
    // Longitude: 102°E to 110°E
    return latitude.compareTo(new BigDecimal("8.5")) >= 0 &&
           latitude.compareTo(new BigDecimal("23.5")) <= 0 &&
           longitude.compareTo(new BigDecimal("102")) >= 0 &&
           longitude.compareTo(new BigDecimal("110")) <= 0;
}
```

#### 4.4.2. Validate Polygon (Kiểm Tra Polygon Hợp Lệ)

```java
public boolean isValidPolygon(GeoJsonPolygon polygon) {
    if (polygon == null || polygon.getCoordinates().isEmpty()) {
        return false;
    }

    List<Point> points = polygon.getCoordinates().get(0).getCoordinates();

    // Polygon must have at least 4 points (3 unique + 1 closing)
    if (points.size() < 4) {
        return false;
    }

    // First and last points must be the same (closed ring)
    Point first = points.get(0);
    Point last = points.get(points.size() - 1);

    return first.getX() == last.getX() && first.getY() == last.getY();
}
```

### 4.5. Distance Calculation (Tính Khoảng Cách)

```java
public double calculateDistance(GeoJsonPoint point1, GeoJsonPoint point2) {
    // Using Haversine formula
    double earthRadius = 6371; // km

    double lat1 = Math.toRadians(point1.getY());
    double lat2 = Math.toRadians(point2.getY());
    double deltaLat = Math.toRadians(point2.getY() - point1.getY());
    double deltaLon = Math.toRadians(point2.getX() - point1.getX());

    double a = Math.sin(deltaLat / 2) * Math.sin(deltaLat / 2) +
               Math.cos(lat1) * Math.cos(lat2) *
               Math.sin(deltaLon / 2) * Math.sin(deltaLon / 2);

    double c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

    return earthRadius * c; // Distance in km
}
```

---

## 5. API Endpoints

### 5.1. Master Data APIs

#### 5.1.1. Import Boundary Level 2 (Districts)

**Endpoint**: `POST /api/master-data/province/import-boundary-lv2`

**File**: `ProvinceApiResource.java:276-309`

**Request**:
```http
POST /api/master-data/province/import-boundary-lv2
Content-Type: multipart/form-data

file: boundary-lv2.geojson
```

**GeoJSON Format**:
```json
[
  {
    "type": "Feature",
    "geometry": {
      "type": "Polygon",
      "coordinates": [[[106.0, 10.0], [106.5, 10.0], [106.5, 10.5], [106.0, 10.0]]]
    },
    "properties": {
      "L2_code": "760",
      "L2_name_loc": "Quận 1",
      "L1_code": "79",
      "L1_name_loc": "Thành phố Hồ Chí Minh"
    }
  }
]
```

**Response**:
```json
{
  "success": true,
  "data": 24,
  "message": "Successful"
}
```

#### 5.1.2. Import Boundary Level 3 (Communes)

**Endpoint**: `POST /api/master-data/province/import-boundary-lv3`

**File**: `ProvinceApiResource.java:356-400`

**Request**: Similar to Level 2, nhưng match theo `fcode`

**GeoJSON Properties**:
```json
{
  "fcode": "phuong_ben_nghe.quan_1.thanh_pho_ho_chi_minh",
  "name": "Phường Bến Nghé",
  ...
}
```

### 5.2. Outbreak APIs

#### 5.2.1. Get Digital Map (Outbreak Map)

**Endpoint**: `POST /api/outbreak/information/get-digital-map`

**File**: `InformationOutbreakApiResource.java:63-180`

**Request**:
```json
{
  "provinceId": "64abc123...",
  "districtId": "64def456...",
  "communeId": "64ghi789...",
  "fromDate": "2024-01-01",
  "toDate": "2024-12-31",
  "status": "DETECTING"
}
```

**Response**:
```json
{
  "listGeoJsonPoint": [
    {
      "type": "Feature",
      "geometryPoint": {
        "type": "Point",
        "coordinates": [106.6297, 10.8231]
      },
      "geometryMultiPolygon": null,
      "geometryPolygon": null,
      "properties": {
        "address": "Thôn Bến Nghé, Phường 1, Quận 1, TP.HCM",
        "name": "Thôn Bến Nghé",
        "code": null,
        "detection": 5,
        "data": {
          "hamletId": "...",
          "hamletName": "Thôn Bến Nghé",
          "detection": 5,
          "handle": 3
        }
      }
    }
  ],
  "listGeoJsonPolygon": [
    {
      "type": "Feature",
      "geometryPoint": null,
      "geometryMultiPolygon": null,
      "geometryPolygon": {
        "type": "Polygon",
        "coordinates": [[[106.69, 10.76], [106.70, 10.76], ...]]
      },
      "properties": {
        "address": "Phường 1, Quận 1, TP.HCM",
        "name": "Phường 1",
        "code": "00001",
        "detection": 12,
        "data": {
          "name": "Phường 1",
          "color": "YELLOW",
          "totalDetection": 12,
          "totalHandle": 8
        }
      }
    }
  ]
}
```

#### 5.2.2. Get Digital Map Outbreak (Extended)

**Endpoint**: `POST /api/outbreak/information/get-digital-map-outbreak`

**File**: `InformationOutbreakApiResource.java:182+`

**Features**:
- Hỗ trợ filter theo cấp hành chính
- Tự động add boundaries của các cấp district/province khi cần
- Tính toán màu sắc nguy cơ (risk color) cho từng vùng

### 5.3. Forecast APIs

#### 5.3.1. Get Risk Forecast

**Endpoint**: `POST /api/forecast/get-risk-forecast`

**File**: `ForecastInformationApiResource.java`

**Request**:
```json
{
  "provinceId": "64abc123...",
  "forecastDate": "2024-06-01",
  "forecastPeriod": 7
}
```

**Response**:
```json
{
  "features": [
    {
      "type": "Feature",
      "geometryPolygon": {
        "type": "Polygon",
        "coordinates": [...]
      },
      "properties": {
        "name": "Phường Bến Nghé",
        "code": "79001001",
        "riskScore": 0.75,
        "riskColor": "#FF6B6B",
        "predictedCases": 15,
        "weatherFactors": {...},
        "populationDensity": 45000
      }
    }
  ]
}
```

### 5.4. Dashboard APIs

**File**: `DashboardApiResource.java`

**Features**:
- Aggregate cases by administrative units
- Filter by date range and geography
- Return statistics with geographic grouping

---

## 6. Công Nghệ và Thư Viện

### 6.1. Spring Data MongoDB GeoSpatial

#### 6.1.1. Annotations

```java
// Đánh dấu field cần index không gian
@GeoSpatialIndexed(type = GeoSpatialIndexType.GEO_2DSPHERE)
private GeoJsonPolygon geometry;
```

**Index Types**:
- `GEO_2D`: 2D flat index (deprecated)
- `GEO_2DSPHERE`: 2D sphere index (recommended) - hỗ trợ Earth-like sphere queries

#### 6.1.2. Repository Query Methods

```java
public interface HamletEntityRepository extends OrdEntityRepository<HamletEntity, String> {

    // Find by commune
    List<HamletEntity> findByCommuneIdAndIsDeletedFalse(String communeId);

    // Find by IDs
    List<HamletEntity> findByIdIn(List<String> ids);

    // Custom geospatial query example
    @Query("{ 'geometry': { $near: { $geometry: ?0, $maxDistance: ?1 } } }")
    List<HamletEntity> findNearPoint(GeoJsonPoint point, double maxDistanceMeters);
}
```

#### 6.1.3. MongoTemplate Geospatial Queries

```java
@Autowired
private MongoTemplate mongoTemplate;

// Within query
public List<OutbreakEntity> findOutbreaksInArea(GeoJsonPolygon area) {
    Query query = new Query(
        Criteria.where("geometry").within(area)
    );
    return mongoTemplate.find(query, OutbreakEntity.class);
}

// Near query
public List<DetailCaseEntity> findCasesNearPoint(GeoJsonPoint point, double radiusKm) {
    Query query = new Query(
        Criteria.where("geometry")
            .nearSphere(new Point(point.getX(), point.getY()))
            .maxDistance(radiusKm / 6371.0) // Convert km to radians
    );
    return mongoTemplate.find(query, DetailCaseEntity.class);
}

// Intersects query
public List<CommuneEntity> findCommunesIntersecting(GeoJsonPolygon polygon) {
    Query query = new Query(
        Criteria.where("geometry").intersects(polygon)
    );
    return mongoTemplate.find(query, CommuneEntity.class);
}
```

### 6.2. Jackson JSON Processing

#### 6.2.1. Custom Deserializer

**File**: `src/main/java/com/ord/core/annotation/GeoJsonPolygonDeserializer.java`

```java
public class GeoJsonPolygonDeserializer extends JsonDeserializer<GeoJsonPolygon> {
    @Override
    public GeoJsonPolygon deserialize(JsonParser p, DeserializationContext ctxt)
            throws IOException {
        JsonNode node = p.getCodec().readTree(p);

        if (node.has("coordinates")) {
            JsonNode coordsNode = node.get("coordinates");

            // Parse coordinates array
            List<List<List<Double>>> coords = parseCoordinates(coordsNode);

            // Convert to Spring Data GeoJsonPolygon
            List<Point> points = coords.get(0).stream()
                .map(coord -> new Point(coord.get(0), coord.get(1)))
                .collect(Collectors.toList());

            return new GeoJsonPolygon(points);
        }

        return null;
    }
}
```

**Usage**:
```java
@JsonDeserialize(using = GeoJsonPolygonDeserializer.class)
private GeoJsonPolygon geometry;
```

### 6.3. MongoDB 2D Sphere Index

#### 6.3.1. Index Creation

MongoDB automatically creates 2D sphere indexes for fields annotated with `@GeoSpatialIndexed`.

**Manual creation** (if needed):
```javascript
db.provinces_new.createIndex({ geometry: "2dsphere" })
db.districts_new.createIndex({ geometry: "2dsphere" })
db.communes_new.createIndex({ geometryMulti: "2dsphere" })
db.hamlets_new.createIndex({ geometry: "2dsphere" })
db.outbreaks.createIndex({ geometry: "2dsphere" })
db.detail_cases.createIndex({ geometry: "2dsphere" })
```

#### 6.3.2. Index Features

**Supported Queries**:
- `$near` / `$nearSphere`: Find documents near a point
- `$geoWithin`: Find documents within a shape
- `$geoIntersects`: Find documents that intersect with a shape
- `$centerSphere`: Find documents within a spherical radius

**Performance**:
- Index size depends on geometry complexity
- Queries on indexed fields are O(log n) average case
- Supports compound indexes with other fields

### 6.4. GeoJSON Standard

**Specification**: [RFC 7946](https://tools.ietf.org/html/rfc7946)

**Coordinate Order**: `[longitude, latitude]` (X, Y)

**Supported Geometry Types**:
- Point
- LineString
- Polygon
- MultiPoint
- MultiLineString
- MultiPolygon
- GeometryCollection

**Feature Structure**:
```json
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [106.6297, 10.8231]
  },
  "properties": {
    "name": "Hospital A",
    "type": "medical_facility"
  }
}
```

---

## 7. Database Schema

### 7.1. Collections Overview

| Collection | Purpose | Geometry Type | Index |
|------------|---------|---------------|-------|
| `provinces_new` | Tỉnh/TP | Polygon | 2dsphere on `geometry` |
| `districts_new` | Quận/Huyện | Polygon, MultiPolygon | 2dsphere on `geometry`, `geometryMulti` |
| `communes_new` | Xã/Phường | Polygon, MultiPolygon | 2dsphere on `geometry`, `geometryMulti` |
| `hamlets_new` | Thôn/Ấp | Point | 2dsphere on `geometry` |
| `outbreaks` | Ổ dịch | Point | 2dsphere on `geometry` |
| `detail_cases` | Ca bệnh | Point | 2dsphere on `geometry` |
| `report_outbreak_details` | Báo cáo ổ dịch | Point (derived) | N/A |
| `report_case_details` | Báo cáo ca bệnh | Point (derived) | N/A |

### 7.2. Sample Document Structure

#### 7.2.1. Province Document

```json
{
  "_id": ObjectId("64abc123..."),
  "code": "79",
  "name": "Hồ Chí Minh",
  "fullName": "Thành phố Hồ Chí Minh",
  "codeName": "thanh_pho_ho_chi_minh",
  "nameEn": "Ho Chi Minh City",
  "fullNameEn": "Ho Chi Minh City",
  "type": "Feature",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [106.3638, 10.3542],
        [106.8767, 10.3542],
        [106.8767, 11.1617],
        [106.3638, 11.1617],
        [106.3638, 10.3542]
      ]
    ]
  },
  "latitude": NumberDecimal("10.8231"),
  "longitude": NumberDecimal("106.6297"),
  "totalHousehold": 2500000,
  "totalPopulation": 9000000,
  "populationDensity": NumberDecimal("4363.2"),
  "administrativeUnitId": 1,
  "administrativeRegionId": 7,
  "createdTime": ISODate("2024-01-01T00:00:00Z"),
  "lastModificationTime": ISODate("2024-01-01T00:00:00Z"),
  "isDeleted": false,
  "isActive": true
}
```

#### 7.2.2. Hamlet Document

```json
{
  "_id": ObjectId("64def456..."),
  "code": "790010010001",
  "name": "Thôn 1",
  "fullName": "Thôn 1, Phường Bến Nghé",
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [106.7008, 10.7756]
  },
  "latitude": NumberDecimal("10.7756"),
  "longitude": NumberDecimal("106.7008"),
  "communeId": "64ghi789...",
  "communeName": "Phường Bến Nghé",
  "districtId": "64jkl012...",
  "districtName": "Quận 1",
  "provinceId": "64abc123...",
  "provinceName": "Thành phố Hồ Chí Minh",
  "createdTime": ISODate("2024-01-01T00:00:00Z"),
  "isDeleted": false,
  "isActive": true
}
```

#### 7.2.3. Outbreak Document

```json
{
  "_id": ObjectId("64mno345..."),
  "name": "Ổ dịch Thôn 1 - 2024/01",
  "detectionDate": ISODate("2024-01-15T08:00:00Z"),
  "status": "DETECTING",
  "geometry": {
    "type": "Point",
    "coordinates": [106.7008, 10.7756]
  },
  "hamletId": "64def456...",
  "hamletName": "Thôn 1",
  "communeId": "64ghi789...",
  "communeName": "Phường Bến Nghé",
  "districtId": "64jkl012...",
  "districtName": "Quận 1",
  "provinceId": "64abc123...",
  "provinceName": "Thành phố Hồ Chí Minh",
  "totalCases": 12,
  "totalDeaths": 0,
  "createdTime": ISODate("2024-01-15T08:00:00Z"),
  "lastModificationTime": ISODate("2024-01-20T10:30:00Z"),
  "isDeleted": false
}
```

### 7.3. Relationships

```
Region (Khu vực)
  └─ Province (Tỉnh/TP)
      └─ District (Quận/Huyện)
          └─ Commune (Xã/Phường)
              └─ Hamlet (Thôn/Ấp)
                  └─ Cases/Outbreaks (Ca bệnh/Ổ dịch)
```

**Foreign Keys** (stored as strings):
- `Hamlet.communeId` → `Commune._id`
- `Commune.districtCode` → `District.code`
- `District.provinceCode` → `Province.code`

**Denormalization**: Names are duplicated for query performance
- Each level stores parent names (provinceName, districtName, etc.)
- Reduces need for joins in MongoDB

---

## 8. Best Practices

### 8.1. Coordinate Precision

**Use BigDecimal for storage**:
```java
private BigDecimal latitude;   // Độ chính xác cao
private BigDecimal longitude;
```

**Convert to double for GeoJSON**:
```java
new GeoJsonPoint(
    longitude.doubleValue(),
    latitude.doubleValue()
)
```

**Precision Levels**:
- 4 decimal places: ~11 meters accuracy
- 5 decimal places: ~1.1 meters accuracy
- 6 decimal places: ~0.11 meters accuracy (recommended)

### 8.2. Index Optimization

**Create compound indexes** for common queries:
```javascript
// Compound index for filtering by location and date
db.detail_cases.createIndex({
  "provinceId": 1,
  "diagnosisDate": -1,
  "geometry": "2dsphere"
})
```

**Monitor index usage**:
```javascript
db.detail_cases.aggregate([
  { $indexStats: {} }
])
```

### 8.3. Query Performance

**Use projection** to reduce data transfer:
```java
Query query = new Query(criteria);
query.fields()
    .include("geometry")
    .include("name")
    .exclude("_id");
```

**Limit results** for map visualization:
```java
query.limit(1000);  // Limit markers on map
```

**Use aggregation** for statistics:
```java
// Better than loading all documents and counting in Java
Aggregation agg = Aggregation.newAggregation(
    Aggregation.match(criteria),
    Aggregation.group("communeId").count().as("total")
);
```

### 8.4. Data Validation

**Validate coordinates before save**:
```java
@PrePersist
@PreUpdate
public void validateGeometry() {
    if (geometry != null) {
        double lon = geometry.getX();
        double lat = geometry.getY();

        if (lon < -180 || lon > 180) {
            throw new IllegalArgumentException("Invalid longitude: " + lon);
        }
        if (lat < -90 || lat > 90) {
            throw new IllegalArgumentException("Invalid latitude: " + lat);
        }
    }
}
```

**Validate polygon closure**:
```java
public void validatePolygon(GeoJsonPolygon polygon) {
    List<Point> points = polygon.getCoordinates().get(0).getCoordinates();
    Point first = points.get(0);
    Point last = points.get(points.size() - 1);

    if (!first.equals(last)) {
        throw new IllegalArgumentException("Polygon must be closed (first point = last point)");
    }
}
```

### 8.5. Error Handling

**Handle geometry conversion errors**:
```java
try {
    Object geoJson = boundaryGeometryDto.toGeoJsonObject();
    if (geoJson instanceof GeoJsonPolygon) {
        entity.setGeometry((GeoJsonPolygon) geoJson);
    } else if (geoJson instanceof GeoJsonMultiPolygon) {
        entity.setGeometryMulti((GeoJsonMultiPolygon) geoJson);
    }
} catch (IllegalArgumentException e) {
    log.error("Invalid geometry type for entity {}: {}", entity.getId(), e.getMessage());
    throw new OrdBusinessException("INVALID_GEOMETRY", "Geometry type not supported");
}
```

**Handle missing geometry gracefully**:
```java
if (hamlet.getGeometry() != null) {
    entity.setGeometry(hamlet.getGeometry());
} else if (hamlet.getLatitude() != null && hamlet.getLongitude() != null) {
    // Fallback to lat/lng
    entity.setGeometry(new GeoJsonPoint(
        hamlet.getLongitude().doubleValue(),
        hamlet.getLatitude().doubleValue()
    ));
} else {
    log.warn("No geometry available for hamlet {}", hamlet.getId());
    // Don't set geometry, continue processing
}
```

### 8.6. Security Considerations

**Sanitize GeoJSON input**:
```java
// Limit polygon complexity to prevent DoS
private static final int MAX_POLYGON_POINTS = 10000;

public void validatePolygonComplexity(GeoJsonPolygon polygon) {
    int totalPoints = polygon.getCoordinates().stream()
        .mapToInt(ring -> ring.getCoordinates().size())
        .sum();

    if (totalPoints > MAX_POLYGON_POINTS) {
        throw new OrdBusinessException("POLYGON_TOO_COMPLEX",
            "Polygon exceeds maximum allowed points: " + MAX_POLYGON_POINTS);
    }
}
```

**Validate file uploads**:
```java
@PostMapping("/import-boundary")
public CommonResultDto<?> importBoundary(@RequestParam("file") MultipartFile file) {
    // Check file size
    if (file.getSize() > 10 * 1024 * 1024) { // 10MB limit
        throw new OrdBusinessException("FILE_TOO_LARGE", "File size exceeds 10MB");
    }

    // Check file type
    String contentType = file.getContentType();
    if (!"application/json".equals(contentType) &&
        !"application/geo+json".equals(contentType)) {
        throw new OrdBusinessException("INVALID_FILE_TYPE", "Only GeoJSON files allowed");
    }

    // Parse and validate...
}
```

### 8.7. Testing Strategies

**Unit test geometry utilities**:
```java
@Test
public void testConvertPolygonToPoint() {
    // Create test polygon
    List<Point> points = Arrays.asList(
        new Point(0, 0),
        new Point(10, 0),
        new Point(10, 10),
        new Point(0, 10),
        new Point(0, 0)
    );
    GeoJsonPolygon polygon = new GeoJsonPolygon(points);

    // Convert to point
    GeoJsonPoint centroid = GeoJsonUtlis.convertPolygonToPoint(polygon);

    // Assert
    assertEquals(5.0, centroid.getX(), 0.001); // Lon
    assertEquals(5.0, centroid.getY(), 0.001); // Lat
}
```

**Integration test repository queries**:
```java
@SpringBootTest
@Transactional
public class HamletRepositoryTest {

    @Autowired
    private HamletEntityRepository hamletRepository;

    @Test
    public void testFindByCommuneId() {
        // Given
        String communeId = "test-commune-id";
        HamletEntity hamlet = new HamletEntity();
        hamlet.setCommuneId(communeId);
        hamlet.setGeometry(new GeoJsonPoint(106.0, 10.0));
        hamletRepository.save(hamlet);

        // When
        List<HamletEntity> results = hamletRepository
            .findByCommuneIdAndIsDeletedFalse(communeId);

        // Then
        assertFalse(results.isEmpty());
        assertEquals(communeId, results.get(0).getCommuneId());
    }
}
```

---

## Phụ Lục

### A. Tài Liệu Tham Khảo

1. **GeoJSON Specification**: https://tools.ietf.org/html/rfc7946
2. **Spring Data MongoDB Geospatial**: https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo.geospatial
3. **MongoDB Geospatial Queries**: https://docs.mongodb.com/manual/geospatial-queries/
4. **JHipster**: https://www.jhipster.tech/

### B. Glossary

| Thuật ngữ | Tiếng Anh | Giải thích |
|-----------|-----------|------------|
| Tỉnh/Thành phố | Province | Cấp hành chính cấp 1 |
| Quận/Huyện | District | Cấp hành chính cấp 2 |
| Xã/Phường | Commune/Ward | Cấp hành chính cấp 3 |
| Thôn/Ấp | Hamlet/Village | Cấp hành chính cấp 4 (thấp nhất) |
| Ổ dịch | Outbreak | Khu vực có nhiều ca bệnh tập trung |
| Ranh giới | Boundary | Đường bao quanh khu vực hành chính |
| Tọa độ | Coordinates | Kinh độ, vĩ độ [longitude, latitude] |
| Đa giác | Polygon | Hình được tạo bởi nhiều điểm nối liền |

### C. Coordinate Systems

**WGS84 (EPSG:4326)**:
- Hệ tọa độ chuẩn quốc tế
- Sử dụng độ thập phân (Decimal Degrees)
- Format: [longitude, latitude]
- Range: Longitude [-180, 180], Latitude [-90, 90]

**Vietnam Bounds**:
- Latitude: 8.5°N to 23.5°N
- Longitude: 102°E to 110°E

---

**Phiên bản**: 1.0
**Ngày cập nhật**: 2024-01-07
**Tác giả**: EDengue Development Team
