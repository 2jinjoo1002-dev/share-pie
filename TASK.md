# TASK — SharePie 실행 체크리스트

우선순위 순서대로 나열. 위에서부터 순서대로 처리하고, 순서를 건너뛰지 않는다. (`CLAUDE.md`의 개발 순서 원칙과 동일)

> UI/UX 화면 제작은 별도 담당자가 이미 진행 중이므로 이 체크리스트에는 포함하지 않는다. 여기서는 **AI 로직·백엔드·블록체인**만 다루고, 완성되는 대로 기존 화면에 "연결"만 한다.

## Phase 0 — 착수 (0~4h)

- [ ] (전체) `START.md` 1~5번 세팅 완료
- [ ] (백엔드) Kiln API `curl` 테스트 성공, `usage` 토큰 값 확인
- [ ] (블록체인 담당) 체인 확정 + devnet 컨트랙트 골격 배포(빈 함수라도 컴파일·배포 성공)
- [ ] (UI/UX 담당과 조율) 화면에서 호출할 API 요청/응답 스키마를 서로 확인하고 고정

## Phase 1 — 2층(Settlement) + 3층(PieCoin) 핵심 (4~20h)

### 백엔드
- [ ] `POST /settlement/analyze` — Kiln API Stage1 연동, 자연어→JSON 구조화
- [ ] `POST /settlement/calculate` — 순수 코드 계산 로직 (Kiln 호출 없음)
- [ ] `POST /settlement/explain` — Kiln API Stage3 연동
- [ ] 각 호출마다 Stage 태그(`settlement.analyze` 등) + 토큰 수 로깅
- [ ] `charge_token`, `lock_for_settlement`, `release_to_recipient` 컨트랙트 함수 실제 구현 + 백엔드에서 호출 연동

### 프론트 연결
- [ ] 정산방 생성 화면 → `POST /settlement/analyze` 연동
- [ ] 정산 결과 화면 → `POST /settlement/calculate`, `/explain` 응답 반영
- [ ] 승인 화면 → `POST /settlement/approve` 연동, 지갑 서명 흐름 확인

### 완료 기준
- [ ] **Run 1 시나리오(정상 정산)가 처음부터 끝까지 한 번에 성공** — 조건입력→계산→승인→온체인기록→TxHash 확인

## Phase 2 — 4층(Dispute Agent) 연결 (20~30h)

### 백엔드
- [ ] `POST /dispute/raise` — `raise_dispute` 컨트랙트 함수 연동
- [ ] `POST /dispute/investigate` — 전체 로그 취합 후 Kiln API Stage4 호출, 3분류 판정 프롬프트
- [ ] `POST /dispute/resolve` — 판정 결과에 따라 `resolve_dispute` + `refund_participant` 자동 호출

### 프론트 연결
- [ ] 이의제기 접수 화면 → `POST /dispute/raise` 연동
- [ ] 이의제기 결과 화면 → `POST /dispute/investigate`, `/resolve` 응답 반영

### 완료 기준
- [ ] **Run 2 시나리오(이의제기)가 성공** — Run 1 결과에 이의제기 → 판정 → 자동 처리까지 확인

## Phase 3 — 기술 로그 & 증빙 (30~34h)

- [ ] (백엔드) Kiln 호출별 input/output 토큰, 계산 로그, 온체인 기록을 조회 API로 제공
- [ ] (프론트 연결) 기술 로그 화면에 위 데이터 바인딩
- [ ] Run 1 / Run 2 각각의 로그를 스크린샷/캡처 가능하게 정리

## Phase 4 — 결제방식 분기 (34~38h, 시간 되면)

- [ ] (백엔드) `POST /settlement/offline-payment` — `mark_offline_payment` 연동
- [ ] (프론트 연결) 결제방식 선택 화면 → 위 API 연동

## Phase 5 — 1층(Shopping Agent) 추가 (38~44h, 시간 남으면만)

- [ ] 공동구매 샘플 데이터 20~30개 JSON 준비
- [ ] `POST /shopping/search` — Kiln API로 조건 추출 + 후보 필터링·비교 설명
- [ ] (프론트 연결) 공동구매 홈·상품비교 화면 → 위 API 연동, 선택 시 정산방 자동 생성 연결

## Phase 6 — README & 발표 준비 (44~48h)

- [ ] README.md 작성 — `CLAUDE.md` 1번의 선언 문장 그대로 사용
- [ ] Declared Function 위계(정산=메인, 쇼핑/분쟁=보조) 명확히 서술
- [ ] Kiln API 단계별 토큰 사용량 표 + 에너지 절감 설명 작성
- [ ] Run 1 / Run 2 로그·TxHash 캡처 첨부
- [ ] 발표 리허설

## 절대 순서를 어기지 말 것

> Phase 1(정산+블록체인 기본)이 안 끝났는데 Phase 5(쇼핑 에이전트)로 넘어가지 않는다. 시간이 부족하면 Phase 4~5는 통째로 스킵해도 되지만, Phase 1~2(정산+이의제기)는 반드시 완성해야 심사 기준을 충족한다.
