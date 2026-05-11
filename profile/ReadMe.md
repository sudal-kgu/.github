# 🦦 수달 — 수거의 달인

> 분리배출 사진을 찍으면 AI가 인증하고, 보상으로 받은 재화로 나만의 섬을 키우는 환경 타이쿤 게임

**데모 URL**: https://sonyk9919.store:20024

---

## 목차

- [서비스 소개](#서비스-소개)
- [핵심 게임 루프](#핵심-게임-루프)
- [시스템 아키텍처](#시스템-아키텍처)
- [기술 스택](#기술-스택)
- [레포지토리 구성](#레포지토리-구성)
- [핵심 기능](#핵심-기능)
- [팀원](#팀원)
- [브랜치 전략](#브랜치-전략)

---

## 서비스 소개

**수달**은 **수거의 달인**의 줄임말입니다.

사용자가 실제 쓰레기 분리배출 사진을 촬영·업로드하면 YOLO 기반 AI가 분석하여 인증 여부를 판단하고, 보상으로 받은 재화로 오염된 섬을 정화하며 성장시키는 **힐링 타이쿤 + 방치형** 환경 게임입니다.

- 기존 환경 앱의 딱딱한 정보 제공 방식 → **행동 유도 중심** 게임화
- 분리수거라는 귀찮은 행동 → **반복 가능한 게임 루프**로 전환
- 수달 마스코트 NPC가 온보딩·튜토리얼·스토리텔링 전반 담당

---

## 핵심 게임 루프

```
[현실]  쓰레기 촬영 & AI 분리배출 인증
            │
            ▼
[학습]  분리수거 퀴즈 수행
            │
            ▼
[보상]  조개 🐚 · 연료 ☢️ 획득
            │
            ▼
[성장]  슬롯 해금 → 건물 건설 · 레벨업 → 섬 정화
            │
            ▼
[힐링]  변화된 생태계 관찰 → 처음으로 반복
```

---

## 시스템 아키텍처

![시스템 아키텍처](https://github.com/user-attachments/assets/d6938296-924f-4e12-9e1c-fee56222e6f5)

---

## 기술 스택

### Client

| 분류 | 기술 |
|------|------|
| Framework | React 19 + Vite |
| Language | TypeScript |
| 3D 렌더링 | Three.js, @react-three/fiber, @react-three/drei |
| 상태 관리 | Zustand, TanStack Query |
| 스타일링 | styled-components |
| 유효성 검증 | Zod |

### API Server

| 분류 | 기술 |
|------|------|
| Framework | Spring Boot 4.0 |
| Language | Java 21 |
| ORM / DB | Spring Data JPA + MySQL |
| 동적 쿼리 | QueryDSL |
| 캐시 / 분산락 | Redis + Redisson |
| 메시지 큐 | RabbitMQ |
| 인증 | Spring Security + JWT |

### Analysis Server

| 분류 | 기술 |
|------|------|
| Framework | FastAPI |
| AI 모델 | Ultralytics YOLO |
| 딥러닝 | PyTorch |
| 메시지 큐 | aio-pika (RabbitMQ 비동기) |

### Infra

| 분류 | 기술 |
|------|------|
| CI/CD | Jenkins |
| 컨테이너 | Docker + Docker Compose |
| 웹서버 | NginX |

---

## 레포지토리 구성

| 레포 | 역할 |
|------|------|
| [server](https://github.com/sudal-kgu/server) | Spring Boot REST API 서버 |
| [client](https://github.com/sudal-kgu/client) | React 웹 클라이언트 |
| [fast-api-server](https://github.com/sudal-kgu/fast-api-server) | AI 이미지 분석 서버 |

---

## 핵심 기능

### 분리배출 인증

사용자가 쓰레기 사진을 업로드하면 RabbitMQ를 통해 FastAPI로 비동기 전달하고, YOLO 모델이 분류 결과를 반환합니다. 인증 완료 시 조개 🐚 · 연료 ☢️ 지급.

### 분리수거 퀴즈

인증 후 촬영한 쓰레기 카테고리에 연관된 퀴즈를 제공합니다. 퀴즈 완료 시 추가 보상이 지급되며, 배치된 건물 효과에 따라 보상량이 증가합니다.

### 재화 시스템

| 재화 | 획득 | 용도 |
|------|------|------|
| 조개 🐚 | 분리배출 인증 | 건물 건설, 슬롯 활성화, 아이템 구매 |
| 보석 💎 | 생산 건물 자동 생성 | 건물 건설(고급), 기프티콘 교환 |
| 연료 ☢️ | 분리배출 인증 | 생산 건물 가동 |

### 섬 성장

총 7레벨 구조. Lv.1~2는 분리배출만으로 달성 가능하며, Lv.3부터는 아이템 구매와 병행해야 레벨업이 가능합니다. 레벨 상승 시 섬 배경(하늘·바다·육지·식생 레이어)이 단계별로 정화됩니다.

### 건물 시스템

**정화 시설** — 퀴즈 완료 시 추가 보상 제공 (재활용 분류기, 대기 정화 타워)

**생산 시설** — 연료 소모로 보석 자동 생산, 건물별 부가 효과 적용

| 건물 | 해금 | 부가 효과 |
|------|------|-----------|
| 풍력 발전소 | Lv.3 | — |
| 폐기물 분류기 | Lv.3 | — |
| 옷 재활용 공장 | Lv.4 | 퀴즈 보상 +25% |
| 플라스틱 재활용 공장 | Lv.5 | 섬 전체 생산량 +10% |

### 슬롯 시스템

최대 7개 슬롯에 건물 배치·철거·이동(swap) 가능. 슬롯 해금 비용은 활성화된 슬롯 수에 비례해 지수적으로 증가합니다 (`1,000🐚 × 1.5^n`).

---

## 팀원

| GitHub | 역할 |
|--------|------|
| [@sonyk9919](https://github.com/sonyk9919) | 팀장 · 프론트엔드 (전체 게임 화면) · 백엔드 (퀴즈, 인증/인가) |
| [@abookhui](https://github.com/abookhui) | 백엔드 (건물, 슬롯) |
| [@hyunj24](https://github.com/hyunj24) | 백엔드 (재화, 경험치) · AI 분석 서버 (YOLO 학습) |
| [@youmjieun](https://github.com/youmjieun) | 프론트엔드 (분리배출 MVP, 퀴즈) |
| [@suminyeom](https://github.com/suminyeom) | 프론트엔드 (분리배출 MVP, 퀴즈) |

---

## 브랜치 전략

```
main        ─── 최종 배포
develop     ─── 통합 개발 (CI/CD 연동, 모든 PR 대상)
feat/*      ─── 기능 단위 작업
fix/*       ─── 버그 수정
refactor/*  ─── 리팩토링
```

- PR 방향: `feat/*` → `develop`
- 최소 1인 리뷰 + Approve 후 머지
- 커밋 컨벤션: `feat:` / `fix:` / `refactor:` / `chore:` / `test:`