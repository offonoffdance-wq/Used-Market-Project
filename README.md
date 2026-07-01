# Nailed — 중고거래 플랫폼

Spring Boot + React 기반의 풀스택 중고거래 플랫폼입니다. 상품 등록부터 주문, 결제, 정산, CS까지 이커머스의 전체 트랜잭션 흐름을 팀 프로젝트로 직접 설계하고 구현했습니다.

- **배포 주소**: (배포 URL 입력)
- **프로젝트 기간**: 2026.04 ~ 2026.06
- **팀 구성**: 3인 (도메인별 풀스택 분담)

---

## 📁 프로젝트 구조

```
nailed-springboot-react/
├── backend/    # Spring Boot 기반 REST API 서버
└── frontend/   # React 기반 클라이언트
```

---

## 🛠 기술 스택

**Backend**
- Java 21, Spring Boot 3.5, Spring Security
- Spring Data JPA (Hibernate)
- MySQL

**Frontend**
- React 19, Vite
- React Router, Axios

**Infra / Tools**
- AWS EC2, Nginx
- Git / GitHub

---

## 👤 담당 역할 

**상품(Product)** 도메인을 백엔드부터 프론트엔드까지 단독으로 설계·구현했습니다. 아울러 팀원이 각자 자신의 파트를 구현한 공용 화면인 **관리자 페이지**에서는 담당 파트(상품 관리·신고 관리)의 API와 처리 로직을 맡아 구현했습니다.

### 담당 도메인 한눈에 보기

| 영역 | Backend | Frontend |
|---|---|---|
| 상품 등록/조회 | `product` 패키지 (Controller/Service/Entity/Repository) | `SellPage`, `ProductDetailPage`, `ProductListPage` |
| 상품 이미지 | `ProductImage` 1:N, 이미지 업로드 API | 등록/상세 이미지 UI, `ProductCard` |
| 홈 상품 영역 | 신상품·인기·랜덤·연관상품 조회 API | `HomePage` 상품 섹션 |
| 관리자 상품관리 *(담당 파트)* | `AdminProductController`, `AdminProductService` (삭제·복구) | *(화면은 팀원)* API·로직 담당 |
| 관리자 신고관리 *(담당 파트)* | `AdminReportController`, `AdminReportService` (제재·반려) | *(화면은 팀원)* API·로직 담당 |

### 핵심 기술 성과: 상품 조회 성능 & 데이터 정합성

동시 조회·증가가 몰리는 상황에서도 **조회 성능과 데이터 정합성**을 함께 고려해 설계했습니다.

- **다중 조건 검색**: `ProductSearchCondition`으로 키워드·카테고리·가격대·사이즈·컨디션·판매완료 제외 등 복합 조건 검색 구현
- **인기순 가중치 정렬**: 단순 조회수가 아닌 `ORDER BY (view_count + wishlist_count * 3)`으로 "찜"에 더 높은 가중치를 부여해 인기 상품 산정
- **N+1 방지**: 상세 조회 시 `fetch join`(`findByIdWithFetch`)으로 판매자·카테고리·이미지를 한 번에 로딩
- **동시성 안전 카운터**: 조회수·찜 수를 `UPDATE ... SET view_count = view_count + 1` 원자적 쿼리로 증가시켜 동시 요청에서도 값 누락 방지

### 다중 이미지 생명주기 관리

- `Product` ↔ `ProductImage`를 1:N 연관관계로 설계하고 `cascade` + `orphanRemoval` 적용
- 상품 수정 시 이미지 교체·삭제가 누락 없이 처리되도록 생명주기를 엔티티에 위임

### 상품 상태 흐름 & Soft Delete

- 상품 상태를 enum(`ProductStatus`)으로 관리: `ON_SALE`(판매중) → `SOLD`(판매완료) → `DELETED`(삭제)
- **Soft Delete** — 물리 삭제 대신 `DELETED` 상태와 `deletedReason`을 보존해 데이터 추적성 확보

### 신고 처리 워크플로우 (관리자)

사용자 신고를 운영자가 처리하는 **상태 기반 워크플로우**를 설계했습니다.

- 상태 전이: `APPROVED`(접수) → **`REJECTED`(반려)** 또는 **`DONE`(제재 완료)**
- **제재 처리**(`penalizeReport`) 시 신고 대상 회원에게 penalty를 연동 생성(`createPenaltyFromReport`, 제재 유형·기간 지정)
- 이미 처리된 신고의 재처리를 막기 위해 `APPROVED` 상태에서만 처리 가능하도록 방어 로직 추가

### 관리자 상품 관리 (담당 파트)

- 관리자 상품 목록 조회(상태 필터) 및 부적절 상품 **삭제(블라인드)·복구** API 구현
- 이미 삭제된 상품의 재삭제 방지 등 상태 검증 포함

### 그 외 구현 내용

- **인증 연동**: 팀원이 구현한 Spring Security + JWT 인증 기반 위에서 상품·관리자 기능을 개발
- **공통 규격 활용**: 팀 공통으로 정립한 표준 응답 포맷(`ApiResponse<T>`)과 전역 예외 처리(`GlobalExceptionHandler`/`ErrorCode`)를 상품·신고 도메인에 적용
- **도메인 모델링**: 상태·사유 값을 enum으로 관리, 공통 `BaseEntity`(생성·수정 시각 감사) 활용

---

## 실행 방법

```bash
# Backend
cd backend
./mvnw spring-boot:run

# Frontend
cd frontend
npm install
npm run dev
```
