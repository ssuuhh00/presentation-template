# CLAUDE.md — 발표 덱 템플릿 작업 가이드 (Claude 전용)

이 폴더는 **16:9 단일 HTML 슬라이드 덱** 템플릿이다. 이 문서는 내가(=Claude) 이 템플릿으로
새 발표를 만들거나 수정할 때 따르는 운영 지침이다. 사람용 설명서는 `README.md`, 이건 **나를 위한
작업 메모**다.

## 이 저장소의 존재 이유 (잊지 말 것)

발표자료를 PPT 대신 **HTML로 만드는 이유는 나(AI) 때문**이다. AI는 `.pptx` 바이너리를
직접 못 다뤄 파이썬 스크립트로 우회 제어하는데 비효율적이다. 반대로 HTML·CSS·JS는 내가
방대하게 학습한 영역이라 **읽고·쓰고·디자인하기가 압도적으로 쉽다.** 그래서 이 템플릿은
"사용자가 말로 시키면 내가 잘 만든다"를 전제로 설계됐다.

**그러니 가능하면 사용자에게 코드(class 이름, HTML 구조)를 묻거나 외우게 하지 않는다.**
사용자는 자연어로 **레이아웃과 내용**만 말하면 되고, 그걸 컴포넌트로 번역해 조립하는 게
기본적으로 내 일이다(물론 사용자가 코드로 직접 지시하면 그대로 따른다).

**핵심 작업 흐름: 사용자가 "이런 배치에 이런 내용" 이라고 말한다 → 나는
"레이아웃을 고르고 → 글 박스 디자인을 고르고 → 내용을 채운다".**
준비된 컴포넌트를 **조합·배치**하는 것이 전부다. 새 컴포넌트를 발명하기보다 아래 블록을 조립한다.
요청이 모호하면(예: "강조해줘") 적당한 기본값으로 일단 만들고, 결과를 보여준 뒤 조정한다.

### 자연어 요청 → 컴포넌트 번역 (자주 나오는 매핑)

| 사용자가 이렇게 말하면 | 나는 이렇게 조립한다 (§ 참고) |
|---|---|
| "그냥 내용만", "글머리표로 정리" | 기본 `.card` + `ul.outline` (§2) |
| "박스로 묶어줘", "회색 박스" | `.card gray` (§2) |
| "강조해줘", "핵심 박스" | `.card teal`(파랑) 또는 `.card accent`(주황) (§2) |
| "그림 설명/해설 달아줘" | `.card dashed`(점선) (§2) |
| "글씨 작게/크게" | `.card sm` / `.card lg` (§2) |
| "좌우로 나눠", "2단/3단" | `.row > .col` (§3) |
| "왼쪽 그림, 오른쪽 글" | `.row > (figure) + (.col.card)` (§3) |
| "위에 큰 그림, 아래 설명" | 풀폭 `figure` + `.card` (§3) |
| "표로 비교", "✓/✗로" | `<table>` + `.mark.y/.n` (§4) |
| "수식 넣어줘" | 인라인/이미지/MathJax 중 복잡도에 맞게 (§4) |
| "움직이는 데모/시뮬" | `<iframe class="sim">` (§5) |
| "색/분위기 바꿔줘" | `:root` 의 `--teal`·`--accent` 토큰 (§6) |
| "잘리는지 확인", "넘치면 줄여" | §7 검증 워크플로 실행 |

---

## 0. 사용자 컨텍스트 (기본값으로 채워라)

- 발표자: **이수행**
- 소속: **지능형데이터처리연구실 (IDPLAB)** — 한글(영문) 병기로 표기
- 로고: `figures/lab-logo.png` (IDP 클라우드 로고). 본문 슬라이드 헤더 우측에 들어간다.
- 표지·푸터의 소속/이름은 위 값을 기본으로 쓴다(사용자가 다르게 지시하면 따른다).

---

## 1. 절대 규칙 — 상단 정렬 + 내용만큼

- 본문 `.grow` 는 콘텐츠를 **헤더 구분선 바로 아래(상단)** 에 붙인다. 박스·표·그림은 **자기 내용
  높이만큼만** 차지하고, 남는 세로 공간은 **아래쪽 여백**으로 둔다(의도된 여백, 채우려 하지 말 것).
- `.row` 안 칸들은 `align-items:stretch`(기본)라 **서로 같은 높이**가 된다(내용 기준).
- 가운데 정렬·고른 분배가 필요할 때만 **opt-in 헬퍼**: `.center`(표지·Q&A), `.grow.spread`(목차류).
- 어떤 슬라이드가 "내용이 상단에 안 붙는다"면 십중팔구 `.grow center` 가 붙어 있는 것 → 상단정렬
  원하면 `center` 제거.
- **저장할 때마다 §7 검증으로 overflow 0 을 확인한다. 잘리면 디자인이 아니다.**

---

## 2. ⭐ 글 박스 (`.card`) — 핵심 조립 컴포넌트

모든 "글 박스"는 단 하나의 컴포넌트 `.card` 다. **class 를 더해 모양을 바꾸고, 조합한다.**

```html
<div class="card dashed sm"> … </div>   <!-- 점선 + 작은 글씨 -->
```

| 축 | 변형 class | 결과 |
|---|---|---|
| **채움** | (없음=기본) | 테두리·배경 없음 — **내용 전용** |
| | `gray` | 회색 채움 + 실선 테두리 |
| | `teal` | 파란(`--teal-l`) 강조 배경 + 테두리 |
| | `accent` | 주황 강조 배경 + 테두리 |
| **테두리** | `dashed` | 점선 테두리 + 배경 없음 — **그림/표 해설에 잘 어울림** |
| **글씨** | `sm` / (기본 18px) / `lg` | 박스 안 글씨 크기 (안쪽 `.outline`·문단이 같이 커지고 작아짐) |

- 기본(`card`)은 **테두리 없음**이 의도다. 박스처럼 보이게 하려면 `gray`/`teal`/`accent`/`dashed` 를 붙인다.
- 내부 유틸: `.card .ctx`(맥락 한 줄, 남색 볼드) · `.card .key`(➡️ 결론 한 줄, 주황 볼드).

### 글 박스 안 내용 = `ul.outline` (들여쓰기)

PowerPoint처럼 들여쓰기로 적는다. 깊이별 글머리표 자동: **1단계 없음 → 2단계 점 → 3단계 대시.**
글씨 크기는 px 고정이 아니라 박스 글씨(`.card` 의 `font-size`) 기준 `em` 이라 `sm`/`lg` 에 따라 같이 조절된다.

**표준 예시 내용 구조** — 새 글 박스를 만들 때 이 골격을 쓴다(라벨/설명 한 줄은 슬라이드마다 교체):

```html
<ul class="outline">
  <li>설명 / 라벨 한 줄</li>           <!-- 1단계(글머리표 없음) -->
  <li>가나다라마 ABCabc 0123           <!-- 1단계: 예시문구 -->
    <ul><li>가나다라마 ABCabc 0123      <!-- 2단계: 점 -->
      <ul><li>가나다라마 ABCabc 0123</li></ul>  <!-- 3단계: 대시 -->
    </li></ul></li>
</ul>
```

- 강조: `<b>`(남색) · `<em>`(주황). 인라인 변수: `.var`(이탤릭) + `<sub>/<sup>`.

---

## 3. 레이아웃 — 그림·글 박스를 배치하기

세로로 쌓고, 가로로 나눌 땐 `.row > .col`. 자주 쓰는 배치(템플릿 3~8페이지가 살아있는 예시):

| 배치 | 골격 |
|---|---|
| 내용만(글 박스 1개) | `.grow > .card` |
| 2단 / 3단 | `.grow > .row.grow-row > .col.card ×2~3` |
| 왼쪽 그림 · 오른쪽 글 | `.row > (.col > figure) + (.col.card)` |
| 오른쪽 그림 · 왼쪽 글 | 위와 `.col` 순서만 반대로 |
| 위에 큰 그림 · 아래 글 | `figure(width:100%)` + `.card` |
| 표 + 해설 | `<table>` + `.card` |

- `.row` 가 슬라이드 높이만큼 늘지 않게 하려면 `.row.grow-row` (행은 내용만큼만).
- 그림은 `<figure><img class="figure" style="max-height:…"><figcaption>…</figcaption></figure>`.
  가로로 긴 그림은 `figure style="width:100%"` + `img style="width:100%"`.

---

## 4. 그 밖의 컴포넌트 빠른 참조

- **표** `<table>`: 헤더 남색·짝수행 음영 자동. 가운데 셀 `th.c`/`td.c`, 체크마크 `.mark.y`(✓)/`.mark.n`(✗).
- **수식** (3가지):
  1. **인라인 HTML** — `.var`+`<sub>/<sup>`+유니코드(Σ θ ∇). 인터넷 불필요. 간단한 식.
  2. **이미지** — `<img class="eqimg">`(원본 비율·가운데). 복잡해도 OK, 인터넷 불필요.
  3. **MathJax/KaTeX** — 진짜 LaTeX. CDN(인터넷) 또는 라이브러리 동봉. 복잡한 식.
- **시뮬** `<iframe class="sim" src="simulations/…">` 를 `<div style="flex:1;min-height:0">` 로 감싼다. (§5 참고)
- **표지** `.title-wrap`: `.venue`(발표 종류) + `h1` + `.sub` + `.presenter`.
- **목차** `.toc > .ti`(번호 `.pn` + 제목 `.tt`). 섹션명만, 소제목 안 씀.
- **섹션 트래커** `.secnav`(본문 헤더 kicker 자리): 전체 섹션을 가로로 나열, 현재 섹션 `<span>` 에만
  `.cur`. 새 본문 슬라이드 복사 시 **`.cur` 위치만** 옮긴다. 표지·목차·Q&A 는 `.secnav` 대신
  `.kicker`(Overview / Thank you) 유지. **소제목은 트래커가 아니라 그 슬라이드 `h2` 에 적는다.**
- **헤더 로고**: 헤더 우측 `<img class="lab-logo" src="figures/lab-logo.png">`.
- **PDF 출력**: 우상단 `🖨` 버튼 → `window.print()`. `@media print` 규칙이 모든 `.slide` 를 펼쳐
  각 1장을 16:9 한 페이지(`@page{size:1280px 720px}`)로 출력. 라이브러리·인터넷 불필요(브라우저 인쇄).
  화면 전용 UI(`#fsbtn`·`#pdfbtn`·`#bar`)는 인쇄 시 숨김. 새 슬라이드는 기존 `.slide` 구조만 따르면
  추가 작업 없이 자동으로 PDF에 포함된다. iframe 시뮬은 인쇄 시 정지화면으로 나옴(상호작용 불가).

### 표지·푸터 채우기 규칙
- `.venue` = **발표 종류**(세미나 / 논문 / 학위 디펜스 / 과제 발표 등).
- `.presenter` = `소속 (IDPLAB) | 발표자 이수행 | YYYY.MM.DD`. **날짜 자리표시 → 실제 날짜로 교체.**
- 푸터: **좌하단 비움**, **우하단 = 발표 제목 + 페이지번호**. 총 장수 `#tot` 는 JS 자동 계산
  (폴백 숫자만 대충 맞춰두면 됨).

---

## 5. 인터랙티브 시뮬 (iframe) 만들 때

`simulations/example-3d-sim.html` 이 표준 예시다. 새 시뮬도 이 패턴을 따른다:

1. **임베드 감지**: `<script>if(window.self!==window.top)document.documentElement.classList.add('embed');</script>`
   — iframe 안이면 `.embed` 가 붙어 자체 헤더를 숨기고 슬라이드용으로 압축된다.
2. **캔버스는 백버퍼를 표시 크기에 맞춘다**(레터박스 방지). 고정 width/height + `object-fit` 쓰지 말고,
   매 프레임 `fitCanvas()` 로 `cv.width/height = clientWidth/clientHeight` 를 맞추고 `W,H,cx,cy,SCALE` 갱신.
3. 임베드 CSS: `.embed body{height:100%;overflow:hidden}`, 캔버스 `flex:1 1 0;min-height:0;width:100%;height:auto`.

---

## 6. 색·글꼴 토큰 (`:root` 만 고치면 전체 반영)

| 변수 | 값 | 용도 |
|---|---|---|
| `--navy` | `#1b2a4a` | 제목·강조, 표 헤더, `<b>` |
| `--teal` | `#0b78bd` (파랑) | 포인트색 — kicker·마커·강조 박스·진행바 |
| `--teal-l` | `#e7f1fa` | teal 박스 배경 |
| `--accent` | `#d97706` (주황) | 대비 강조 — `<em>`·accent 박스·결론(`.key`) |
| `--green`/`--amber` | `#1e7a4d`/`#b8860b` | 보조(✓ / 주의) |
| `--ink`/`--muted` | `#1d2330`/`#6b7385` | 본문 / 흐린 텍스트 |
| `--card`/`--line` | `#f7f9fc`/`#e3e7ee` | gray 박스 배경 / 테두리·구분선 |

- 글꼴: 본문 `--sans`, 제목·수식 `--serif`. 주제가 바뀌면 `--teal`·`--accent` 두 개만 바꿔도 분위기 전환.

---

## 7. 검증 워크플로 (Windows / PowerShell) — 함정 주의

내용을 바꾼 뒤 **반드시** 잘림(overflow)·레이아웃을 확인한다.

### 환경 함정 (꼭 지킬 것)
- **UTF-8 읽기**: `Get-Content -Raw` 는 한글을 깨뜨린다. 반드시
  `[System.IO.File]::ReadAllText($path,[System.Text.Encoding]::UTF8)`.
- **UTF-8(BOM 없이) 쓰기**: `[System.IO.File]::WriteAllText($p,$txt,(New-Object System.Text.UTF8Encoding($false)))`.
- **Chrome 싱글톤 잠금**: 매 실행마다 **고유 `--user-data-dir`** 를 준다. ⚠️ `Get-Process chrome | Stop-Process`
  **절대 금지**(사용자 일반 크롬까지 죽음). 잔여 headless 만 골라 종료:
  ```powershell
  Get-CimInstance Win32_Process -Filter "Name='chrome.exe'" |
    Where-Object { $_.CommandLine -like '*--headless*' } |
    ForEach-Object { Stop-Process -Id $_.ProcessId -Force -ErrorAction SilentlyContinue }
  ```
- **`--dump-dom` 은 빈 출력**이 난다 → 결과를 화면에 크게 띄워 스크린샷 → 이미지로 Read.
- 한글 경로 `file:///` 는 PowerShell 호출 연산자(`& $CHROME`)로 잘 열린다.
- 특정 슬라이드만 보려면 소스의 `show(0);` 를 `show(N-1);` 로 치환해 임시 파일 저장 후 캡처.
- Chrome: `C:\Program Files\Google\Chrome\Application\chrome.exe` (없으면 Edge), 플래그
  `--headless=new --disable-gpu --window-size=1280,720`.

### 잘림 자동 점검 — 통째로 붙여 실행
```powershell
Get-CimInstance Win32_Process -Filter "Name='chrome.exe'" | Where-Object { $_.CommandLine -like '*--headless*' } | ForEach-Object { Stop-Process -Id $_.ProcessId -Force -ErrorAction SilentlyContinue }
$CHROME = "C:\Program Files\Google\Chrome\Application\chrome.exe"
$dir = "C:\Users\lsh\Desktop\presentation-template"
$enc = New-Object System.Text.UTF8Encoding($false)
$deck = [System.IO.File]::ReadAllText("$dir\template.html",[System.Text.Encoding]::UTF8)
$js = @'
<script>document.addEventListener('DOMContentLoaded',()=>{const o=[];document.querySelectorAll('.slide').forEach((s,k)=>{const w=s.classList.contains('active');s.classList.add('active');const v=s.scrollHeight-s.clientHeight;if(v>0)o.push((k+1)+':'+v);if(!w)s.classList.remove('active')});const d=document.createElement('div');d.style.cssText='position:fixed;inset:0;z-index:9999;background:#fff;color:#111;font:bold 36px monospace;display:flex;align-items:center;justify-content:center';d.textContent='TOT '+document.querySelectorAll('.slide').length+' | '+(o.length?('OVERFLOW '+o.join('  ')):'ALL OK');document.body.appendChild(d);});</script>
'@
[System.IO.File]::WriteAllText("$dir\_chk.html",$deck.Replace('</body>',$js+'</body>'),$enc)
& $CHROME --headless=new --disable-gpu --no-first-run --user-data-dir="C:\Temp\chk$(Get-Random)" --window-size=1280,720 --screenshot="$dir\_chk.png" (("file:///"+($dir -replace '\\','/')+"/_chk.html")) --virtual-time-budget=4500 | Out-Null
```
→ `_chk.png` 를 Read 로 열어 **`TOT n | ALL OK`** 확인. `OVERFLOW 3:42` 면 3번 슬라이드가 42px
넘침 → 내용 축소 / `max-height` / 폰트(`card sm`) 조정.

### 특정 슬라이드 스크린샷
```powershell
$mod = $deck -replace 'show\(0\);', 'show(4);'   # 5번 슬라이드
[System.IO.File]::WriteAllText("$dir\_s.html",$mod,$enc)
& $CHROME --headless=new --disable-gpu --no-first-run --user-data-dir="C:\Temp\s$(Get-Random)" --window-size=1280,720 --screenshot="$dir\_s.png" (("file:///"+($dir -replace '\\','/')+"/_s.html")) --virtual-time-budget=4500 | Out-Null
```

### 끝나면 임시 파일 정리
```powershell
Get-ChildItem $dir -Filter "_*" -File | Remove-Item -Force
```

---

## 8. 폴더 구조 & 현재 슬라이드 구성

```
presentation-template/
├─ template.html            ← 본체(복사해서 시작)
├─ README.md                ← 사람용 설명·사용법
├─ CLAUDE.md                ← 이 문서
├─ figures/                 ← 예시 그림·로고 (lab-logo.png, example-*.png)
└─ simulations/             ← 예시 3D 시뮬 (example-3d-sim.html, §5 패턴)
```

현재 `template.html` 은 **10장**이며, 각 본문 슬라이드는 컴포넌트 조합의 **본보기**다(복사해서 변형):

1. **표지** — `.title-wrap`
2. **목차** — `.toc`
3. **내용 전용** — 기본 글 박스(테두리 없음) + `ul.outline` 다단계 + 인라인 수식
4. **글 박스 변형 카탈로그** — 기본/점선 · 회색/파랑/주황 · 작은/큰 (모든 변형 한눈에)
5. **글 박스 배치** — 풀폭 / 2단 / 3단 (`.row`·`.col`)
6. **그림 박스** — 좌 그림 / 우 점선 해설박스
7. **가로로 긴 그림 박스** — 풀폭 그림 + 아래 점선 해설박스
8. **표 + 글 박스** — 표 + 가로로 긴 점선 해설박스
9. **인터랙티브 시뮬** — `<iframe class="sim">` (3D 회전 + 슬라이더)
10. **Q&A** — `.grow.center`

`example-*` 자료는 그대로 두면 렌더되는 자리표시용. 실제 자료로 교체해 쓴다.

---

## 9. 디자인 원칙 한 줄 요약

- 한 슬라이드 = 한 메시지(트래커 `.secnav`=위치, `h2`=주제, 본문=근거).
- 컴포넌트를 **고르고 조합**한다 — 레이아웃 → 글 박스 디자인 → 내용.
- 상단부터 내용만큼. 빈 아래 여백은 의도된 호흡.
- 색은 둘만: 파랑(`--teal`)=흐름·긍정, 주황(`--accent`)=대비·주의. 남발 금지.
- 그림·표 해설엔 점선 글 박스(`card dashed`)가 잘 어울린다.
- 비교는 글보다 표 ✓/✗.
- 잘리면 디자인이 아니다 — 저장할 때마다 overflow 0 확인(§7).
