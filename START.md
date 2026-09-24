# START — SharePie 개발 시작 가이드

이 문서는 팀원이 **개발을 처음 시작할 때** 순서대로 따라 하는 체크리스트입니다. `CLAUDE.md`는 "무엇을 만들지"의 규칙이고, 이 문서는 "어떻게 첫 삽을 뜨는지"입니다.

## 0. 시작 전 확인할 것

- [ ] GitHub 저장소 Pull 완료 (`Share Pie.dc.html`, `support.js`, `.thumbnail` 로컬에 있는지 확인)
- [ ] `CLAUDE.md`, `TASK.md`를 저장소 루트에 커밋해둠
- [ ] Node.js, VS Code, GitHub Desktop 설치 완료

## 1. Kiln API 키 발급

1. `https://docs.bricksum.com` (또는 콘솔 링크)에서 회원가입
2. "Get an API key" 버튼으로 키 발급 → `sk-bk-...` 형태
3. 프로젝트 루트에 `.env` 파일 생성:
   ```
   KILN_API_KEY=sk-bk-여기에_발급받은_키
   KILN_BASE_URL=https://api.bricksum.com/v1
   KILN_MODEL=gpt-oss-120b
   ```
4. `.env`는 반드시 `.gitignore`에 추가 — 절대 커밋하지 않는다

## 2. Kiln API 최소 동작 테스트 (제일 먼저 할 것)

아래 curl이 정상 응답(`Hello!`에 대한 답변)을 반환하는지 확인:
```bash
curl https://api.bricksum.com/v1/chat/completions \
  -H "Authorization: Bearer $KILN_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-oss-120b",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```
✅ 응답 안에 `usage` 객체(토큰 수)가 포함되는지도 함께 확인 — `CLAUDE.md` 3번 항목 로깅 요구조건에 필요.

## 3. 블록체인 환경 세팅

- [ ] 체인 확정 (Move/Aptos·Sui 또는 Solidity/EVM 중 팀 결정)
- [ ] CLI 설치 (Aptos CLI 또는 Hardhat/Foundry)
- [ ] 테스트넷 지갑 생성 + 테스트넷 토큰(faucet) 받기
- [ ] `CLAUDE.md` 4번의 함수 7개(`charge_token` 등)를 골격만 먼저 컴파일되게 작성 → devnet 배포 → 컨트랙트 주소를 `CLAUDE.md`의 환경변수 섹션에 채워넣기

## 4. UI/UX — 별도 담당자 진행 중, 여기서는 참고만

화면 제작은 별도 담당자가 진행하므로 이 가이드에서 다루지 않는다. 백엔드/블록체인 담당자는 `Share Pie.dc.html`을 열어 **API가 어떤 화면과 이어질지 감만 잡고**, 실제 작업은 5번(API 스텁)부터 시작한다.

## 5. 백엔드 서버 뼈대

1. Node.js(Express) 서버 생성
2. `CLAUDE.md` 5번의 엔드포인트 9개를 빈 함수(스텁)로 먼저 다 만들어두기 — 나중에 하나씩 채움
3. Kiln API 클라이언트 모듈 하나 만들어서(`kilnClient.js`) 모든 엔드포인트가 이걸 공유해서 쓰게 함 (Stage 태깅·토큰 로깅을 여기서 공통 처리)

## 6. 첫 번째로 완성해야 할 최소 흐름 (Day 1 목표)

```
[정산방 생성] → [자연어 조건 입력] → Stage1(AI) → Stage2(코드계산) → Stage3(AI설명) 
→ [승인] → [온체인 기록] → Transaction Hash 화면에 표시
```
이 흐름 하나가 끝까지 돌아가면 Run 1(정상 정산) 데모가 완성된 것입니다. 그다음 이의제기(4층)를 붙입니다.

## 7. 막히면

- Kiln API 응답 형식이 예상과 다르면 → `/llms.txt` 문서를 Claude Code에게 붙여넣고 다시 물어보기
- 컨트랙트 컴파일 에러 → 함수 시그니처가 `CLAUDE.md` 4번과 정확히 일치하는지부터 확인
- 무엇을 먼저 해야 할지 헷갈리면 → `TASK.md` 확인
