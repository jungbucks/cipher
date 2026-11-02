# 암호의 세계 (cipher) — 유지보수 노트

> 정보 교과 암호학 시각화 **단일 HTML**(`index.html` 하나가 전부, 빌드 없음).
> 탭 6개: 🔄시저(휠·매핑표·브루트포스 해독 도전) / 🚂레일 펜스 / 📊열 전치 / 🔑대칭 키 / 🔐공개 키 / #️⃣해시 함수(SHA-256 눈사태 라이브).
> 배포: **온라인 Pages 주 배포**(https://jungbucks.github.io/cipher/ — 저장소 jungbucks/cipher) + file:// 더블클릭도 동작(crypto.subtle 포함, 2026-07-15 확인).

## 규칙

1. **콘텐츠 수정 위치**: 퀴즈 = `QUIZZES` 객체(탭당 3문항, 총 18 — 보기 순서는 로드 때 섞이고 채점은 원래 번호 `a` 기준, 오답이면 [다시 풀기]), 브루트포스 문구 = `BF_PHRASES`. 페이지 설명·표는 각 `<section>` 마크업.
2. **디자인**: 웜 페이퍼 팔레트(`:root` — bg `#FBF9F6`, amber·plum·teal·rose·green). hex 추가 전에 기존 변수 확인. 탭별 색 정체성(시저=amber, 전치=plum/teal, 대칭=amber, 공개=plum, 해시=green) 유지.
   - **타입 스케일(2026-07-24 정리, `:root` 주석에도 박아둠)**: 새 폰트 크기는 이 안에서만 고른다 — **라벨 11 · 캡션 12 · 컨트롤 13~14 · 본문/데이터 15 · 강조데이터 16 · h2 26 · 디스플레이 clamp(30,6vw,54)**. 아이콘/이모지 크기(18·20·22)는 텍스트 아니므로 예외. 임의의 12.5·13.5·14 본문 새로 넣지 말 것("제각각" 재발 방지).
   - **폰트 역할**: 한글/디스플레이 = Paperlogy(=`Paperozi` @font-face, 페이지 정체성·유지). 코드/암호문/알파벳/키 = `--mono`(=`ui-monospace,'SF Mono','Consolas',…` — 구 `Courier New`에서 2026-07-24 교체, 의존성 0). mono는 정렬 필요한 데이터에만.
3. **폰트**: Paperlogy CDN(jsdelivr)이 유일한 외부 의존 — **온라인 배포 전제로 유지 결정**(2026-07-15). 오프라인·차단망에서는 Pretendard/맑은고딕 폴백으로 동작(font-display:swap). 서브셋 내장은 이 PC에 폰트 도구가 없어 보류.
4. 입력은 영문 A-Z만 처리(`clean()`), 영문 외 입력 시 `latinNote()`가 안내 표시. 해시 탭만 예외(모든 문자 허용 — 의도된 대비).
5. 용어: 법·제도 표현은 현행 기준 — **공동인증서**(구 공인인증서) 표기 유지, 알고리즘 구식 표기(DES·MD5 등)도 정확히.

## 검증법 (이 PC 특이사항)

- 단일 파일이라 브라우저로 열면 끝. 자동 검증은 **file:// 프로브**: scratchpad에 iframe 프로브 작성 → 헤드리스 Edge `--headless=new --allow-file-access-from-files --user-data-dir=<임시프로필> --dump-dom`. ⚠️ `--user-data-dir` 없으면 기존 Edge에 위임돼 출력이 빈다.
- 회귀 기준(2026-07-15, 16종 PASS): 브루트포스(25행·정답/오답·리셋), 해시(64자·diff·동일입력), 퀴즈 54버튼(18문항), 휠 52자·매핑 26칸, `rfEncrypt('HELLO',2)==='HLOEL'`, `colDecrypt('EODASREIERCEWDV','ZEBRA')==='WEAREDISCOVERED'`, latinNote.
- 로직 검산 완료(2026-07-15): 레일펜스·열 전치 암/복호 왕복, 불규칙 열 전치의 짧은 열 처리 정확.

## 열 전치 — 기초/심화 (2026-07-24)

- **서브탭 2개**(`.sub-tab`, `colLevel`): 🌱**기초**(교과서식) = 키워드 없이 **열 수(3~6 슬라이더)만** 지정 → 왼쪽 열부터 항등순서로 세로 읽기 / 🔑**심화** = 기존 ZEBRA 키워드(알파벳 정렬순 열 읽기).
- **엔진 공유**: `colVisualize`는 `cols·order·sortedCols·headers`를 레벨별로만 분기(기초=항등순열·숫자헤더 / 심화=getColOrder·글자헤더) — 검증된 애니메이션 그대로 재사용. 기초 암호화 헬퍼 `basicEncrypt(text,cols)`.
- **1×/2× 속도**: (Phase B부터) 세 탭 공통 — `setSpeed(tab,s)` → `makeAnim().setSpeed`. `setColSpeed`는 예전 이름 호환용.
- ⚠️ **기초판은 표준형**(열수만·자연순서)으로 구현 — 실제 교과서 표기와 다를 수 있어 화면에 "다르면 알려달라" 안내 박아둠. 교과서 원본 확인 시 조정 대상.
- **검증(2026-07-24, 프로브 전건 PASS)**: 기초 enc/dec 왕복 cols=5(`WDVEIEASRRCEEOD`)·cols=3(`WRIOREESVEADCED`), 심화 ZEBRA 왕복(`EODASREIERCEWDV`), 헤더 기초=숫자·심화=글자, 2×속도 무오류. 스크린샷 확인.
- ⚠️ **프로브 함정 2종**: ① (2026-11-02 Phase B에서 해소) `showPage(id)`는 이제 프로그램 호출 가능. 탭 이동+주소 변경은 `goTab(id)`. ② `let colAnimating` 등 최상위 let은 `window` 속성 아님 → `W.colAnimating` undefined. 애니메이션 완료 대기는 **`await W.colVisualize()`**(함수 선언이라 window에 있음)로.

## 2026-11-02 개선 Phase A (내용 오류·상태 버그)

- **휠 방향**: 바깥=평문, 안쪽=암호문. 안쪽 고리는 `rotate(-k/26*360)`(반시계) → 바깥 P 바로 안쪽에 P+k. 매핑표는 모드와 무관하게 윗줄 평문/아랫줄 암호문(=휠을 펼친 것), 복호화는 아래→위로 읽는다(`#mapDirLabel`). 시각화 강조는 바깥 평문·안쪽 암호문·표 같은 열.
- **즉시 다시 그리기**: 레일 펜스 `rfDraw(animate)`·열 전치 `colDraw(animate)` 하나로 통일. 입력·슬라이더·키워드·모드 변경 = `draw(false)` 즉시, ▶시각화 = `draw(true)`. `makeAnim()` 타이머 묶음이 새 그리기마다 이전 타이머를 전부 취소(`ANIM_CANCEL`). 시저도 입력 변경 시 진행 중 시각화 취소.
- 열 전치 예시 문장 자동 교체는 칸이 비었거나 예시(평문/기초·심화 예시 암호문)일 때만 — 학생 입력은 안 지움. `colEncrypt(input,key)` 헬퍼 추가.
- `FEATURES` 플래그 객체(스크립트 맨 위) — Phase C에서 사용(아래).
- 검증: Playwright(msedge) 프로브 67항목 PASS. ⚠️ `page.evaluate(()=>rfVisualize())`처럼 promise를 반환하면 애니메이션 끝까지 기다림 — 도중 조작 테스트는 `{ rfVisualize(); }`로.

## 2026-11-02 개선 Phase B (배치·조작부·탐색·접근성)

- **시저 작업대**(`.caesar-bench`): 휠·입력·이동 칸 수·조작부·결과를 카드 하나에. 900px 이상 = 휠 왼쪽/조작 오른쪽(grid-template-areas), 미만 = 휠(70vw) → 슬라이더 → 결과 → 입력 → 조작부.
- **공통 조작부**: `buildControls()`가 `.ctrl-bar[data-tab]`에 같은 순서로 생성 — [모드 암호화|복호화][▶ 암호화/복호화 시각화][⏸ 일시정지/▶ 재생][⏭ 한 단계][초기화][속도 1×|2×]. 설정은 `CTRL` 객체(탭별 anim·setMode·play·reset). 모드 표시는 `syncModeUI`.
- **makeAnim**: 일시정지(held)·한 단계(`wait(g,ms,true)` = 단계 끝)·속도. ⏭를 멈춘 상태에서 누르면 첫 단계에서 멈춘 채 시작. 일시정지 버튼은 `aria-disabled`(포커스 유지 위해 disabled 안 씀).
- **탭**: `.nav-wrap` sticky + 좌우 페이드(`navFade`), 주소 `#caesar…#hash`(`location.hash` → hashchange → `showPage`), 뒤로/앞으로 동작, 각 탭 끝 "다음: ○○ →"(해시=처음으로). role=tablist/tab/tabpanel, ←→ Home End.
- **접근성**: 결과·피드백 aria-live=polite(진행 중엔 aria-busy), 휠 role=img+shift aria-label, :focus-visible, prefers-reduced-motion이면 ▶/⏭가 즉시 완료 상태(`reducedMotion()`) + CSS 전환 0s.
- 레일 격자 칸 `minmax(18px,32px)`+aspect-ratio → 390px에서 15글자 가로 스크롤 없음.
- **첫 화면 기준(사용자 결정 B)**: 1280×800·390×844에서 스크롤 0으로 휠·슬라이더·결과가 다 보이게 머리말·여백 압축(Phase B-2 CSS), 데스크톱 휠 270px. 머리말·여백을 다시 늘리면 이 기준이 깨짐 — probeB "첫 화면" 2항목으로 확인.
- 검증: probeB 41항목 + Phase A 67항목 회귀 PASS.

## 2026-11-02 개선 Phase C (인터랙티브 확장 — FEATURES 플래그)

- **원칙**: Phase C 화면은 전부 `initPhaseC()`에서 플래그가 true일 때만 JS로 만들어 붙인다. false면 DOM에 아예 없음(전부 false = Phase C 이전 화면과 id·요소 수 동일, probeC로 확인). 코드 구획 = CSS `/* C1 … */`~`/* C6 … */`, JS `// ---------------- C1 …`~`C6`.
- **FEATURES**: `progress`(C1 진행판+미션 퍼즐 3종+첫 방문 안내) · `xorLab`(C2 XOR 실험실+키 개수 폭발) · `rsaLab`(C3 미니 RSA+자물쇠 상자 게임) · `hashLab`(C4 로그인 시뮬+블록 3개 체인) · `handsOn`(C5 휠 드래그·빈도 분석·레일 손으로 놓기·키워드 카드 정렬) · `shareNote`(C6 쪽지 공유 링크+QR).
- **localStorage 키**(모두 `store` 래퍼 — 막히거나 깨져도 기본값): `cipher_progress_v1` = `{v:1,missions:{탭:true},quiz:{탭:[문항번호]}}`(읽을 때 유효값만 남김), `cipher_intro_seen` = '1'. 로그인 시뮬의 비밀번호·해시는 메모리에만(저장 금지).
- **미션 연결**: 시저=`bfPick` 정답 / 레일=`#rfPuzzle` / 열 전치=`#colPuzzle` / 대칭=`ciTry('sym','K')` / 공개=`ciTry('pub','bob-priv')` / 해시=`#hashPuzzle` → `missionDone(tab)`. 퀴즈 정답 → `quizSolved(tab,i)`.
- **끌어다 놓기**: `enableDrag(src,onDrop)`(pointer events, 마우스·터치 공통, `[data-drop]` 대상) + 키보드/탭은 "고르고 → 대상 누르기".
- **QR**: 외부 라이브러리 없이 `QR.encode/svg`(바이트 모드·오류정정 M·버전 1~10, 마스크 8종 중 벌점 최소). 쪽지는 100자 제한. probeC가 jsQR(테스트 전용, scratchpad에만 설치)로 해독해 링크와 일치 확인.
- **쪽지 링크**: `?note=암호문&type=caesar|railfence|columnar#탭` — 키는 절대 넣지 않음. 들어오면 type 탭 + "친구가 보낸 쪽지" 카드 + 복호화 칸에 자동 입력. note는 A-Z·공백만 남김(삽입 방지). type이 #해시보다 우선.
- **첫 화면 기준**: 진행판이 켜지면 머리말 장식선을 빼고(`.has-progress`) 모바일 여백을 더 줄여 1280×800·390×844 첫 화면 기준 유지.
- **검증**: probeC 48항목(플래그 하나씩 끄기·전부 끄기·Phase C 이전과 구조 비교·터치 드래그 CDP·QR 해독 포함) + probeA 67 + probeB 43 PASS. ⚠️ 프로브는 첫 방문 안내 모달이 클릭을 가리므로 `cipher_intro_seen`을 미리 넣고 시작.

## 이력·백로그

- 2026-07-06 초판(5탭). 2026-07-15 교사 리뷰 9건 반영: 휠 문구·공동인증서·영문 안내(1~3), 브루트포스 도전(4), 해시 탭 신설(5), 시나리오 퀴즈 10→17문항(6), 폰트 폴백 보강·배포 준비(7~9).
- 백로그(선택): 한글 시저(자모 밀기) — 미착수, 수요 확인 후. (휠 드래그 회전은 2026-11-02 C5로 완료)
