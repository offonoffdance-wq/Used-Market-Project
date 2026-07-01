# 중고거래 웹 플랫폼

> Spring Boot + React 기반 중고 물품 거래 웹 애플리케이션
> 3인 팀 프로젝트 · 저는 **상품(Product) 도메인을 백엔드부터 프론트까지 전담**했습니다.

![Java](https://img.shields.io/badge/Java-21-007396?logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5-6DB33F?logo=springboot&logoColor=white)
![Spring Security](https://img.shields.io/badge/Spring%20Security-JWT-6DB33F?logo=springsecurity&logoColor=white)
![JPA](https://img.shields.io/badge/Spring%20Data%20JPA-59666C?logo=hibernate&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?logo=mysql&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-646CFF?logo=vite&logoColor=white)

- 🔗 **배포**: http://13.125.205.120/ <!-- 접속 확인 후 유지/수정 -->
- 🗓 **개발 기간**: 2026.05 ~ 2026.06 <!-- 실제 기간으로 수정 -->
- 👥 **팀 구성**: 3인 (백엔드 / 프론트 협업, 도메인별 분담)

<br>

## 👤 담당 파트

전체는 팀 프로젝트이며, 저는 아래를 **주도적으로 설계·개발**했습니다.

### ⭐ 주담당 — 상품(Product) 도메인 (풀스택)
DB 스키마 → JPA 엔티티 → REST API → React 화면까지 **한 도메인을 끝까지** 책임졌습니다.

**Backend**
- 상품 **등록/수정/삭제/상세/목록** API 설계 및 구현 (`ProductController`, `ProductService`)
- **다중 이미지 업로드** — `Product` ↔ `ProductImage` 1:N 연관관계, `cascade` + `orphanRemoval`로 생명주기 일괄 관리
- **enum 기반 상태 관리** — 판매상태(`ProductStatus`), 상품컨디션(`ProductCondition`)을 문자열 상수 대신 enum으로 모델링
- **페이징 목록 + 검색/필터**, 신상품·인기·랜덤·**연관 상품 추천**(`/related`) 조회 API
- **조회수·찜 수 비정규화 카운터** 컬럼으로 조회 성능 최적화
- 연관관계 전부 **LAZY 로딩**(판매자/카테고리/브랜드)으로 불필요한 조회 방지
- **Soft Delete** 적용 (`deletedReason` 보존, 물리 삭제 대신 논리 삭제)

**Frontend**
- **상품 상세 페이지**(`ProductDetailPage`) — 이미지 갤러리, 판매자 정보, 관련 상품
- **상품 등록 페이지**(`SellPage`) — 카테고리/브랜드/컨디션 선택, 이미지 업로드 UI
- **상품 목록/검색**(`ProductListPage`), **홈 상품 영역**, `ProductCard` 컴포넌트

### 🛡 관리자 — 상품·신고 관리 (백엔드)
제가 맡은 상품·신고 도메인과 이어지는 **운영자 API·비즈니스 로직**을 담당했습니다.
- **신고 처리 워크플로우** — 신고 검토 후 **제재(penalize)** 또는 **반려(reject)** 처리 (`AdminReportController`, `AdminReportService`)
- **상품 관리** — 부적절 상품 **숨김(블라인드) 처리** (`AdminProductController`, `AdminProductService`)

> 관리자 **화면(UI)** 은 팀원이 담당했고, 저는 **서버 API와 처리 로직**을 담당했습니다.

### 🤝 함께 개발
- 리뷰 · 신고(사용자 등록) · 주문 · 위시리스트 도메인 (API 및 화면 일부)

### 🧩 공통 인프라 기여
- **Spring Security + JWT** 인증 설정 (Access/Refresh 토큰)
- **전역 예외 처리** 및 **표준 API 응답 포맷**(`ApiResponse<T>`) 설계
- 도메인 **enum 모델링**, 공통 `BaseEntity`(생성·수정 시각 감사) 활용

<br>

## ✨ 주요 기능
- **회원/인증**: JWT 기반 로그인, 프로필 관리
- **상품 거래**: 상품 등록·검색·상세, 다중 이미지, 카테고리/브랜드/컨디션 분류
- **거래 부가**: 위시리스트(찜), 리뷰, 신고, 1:1 문의
- **주문**: 주문 생성·조회
- **관리자**: 상품 숨김·신고 처리(제재/반려) 등 운영 기능 *(대시보드 UI는 팀 공동)*

<br>

## 🛠 기술 스택
| 구분 | 기술 |
|---|---|
| Backend | Java 21, Spring Boot 3.5, Spring Security, Spring Data JPA, Lombok |
| Database | MySQL |
| Frontend | React 19, Vite, React Router, Axios |
| 인증 | JWT (jjwt) — Access / Refresh Token |
| 배포 | AWS EC2, Nginx |

<br>

## 🏗 프로젝트 구조
```
marketplace-webapp/
├── backend/                 # Spring Boot REST API
│   └── src/main/java/com/nailed/
│       ├── web/             # 도메인별(product, member, order, review ...) controller·service·repository·dto·entity
│       ├── common/          # 공통 entity·enum·예외·응답 포맷
│       └── config/          # Security·JWT·Web 설정
└── frontend/                # React (Vite)
    └── src/
        ├── api/             # 도메인별 API 모듈
        ├── pages/           # 페이지
        ├── components/      # 재사용 컴포넌트
        └── hooks/ utils/ styles/
```

<br>

## 🚀 실행 방법

### Backend
```bash
cd backend
# application.properties 준비 (템플릿 복사 후 값 채우기)
cp src/main/resources/application-example.properties src/main/resources/application.properties
./mvnw spring-boot:run
```

### Frontend
```bash
cd frontend
cp .env.example .env      # VITE_API_BASE_URL 설정
npm install
npm run dev
```

<br>

## 💡 기술적으로 고민한 점
- **다중 이미지의 생명주기**: 상품-이미지를 1:N으로 두고 `orphanRemoval`로 삭제 누락을 방지 — 상품 수정 시 이미지 교체가 깔끔하게 처리되도록 설계
- **상태를 enum으로**: 판매상태/컨디션을 문자열이 아닌 enum으로 관리해 타입 안정성과 유지보수성 확보
- **조회 성능**: 조회수·찜 수를 매번 집계하지 않고 카운터 컬럼으로 비정규화
- **일관된 API 계약**: `ApiResponse<T>`로 성공/실패 응답 형식을 통일해 프론트 연동을 단순화
- **신고 처리 흐름**: 신고를 상태값으로 관리하고 **제재/반려**로 분기 — 사용자의 신고가 실제 운영 조치로 이어지는 과정을 서버에서 일관되게 처리
