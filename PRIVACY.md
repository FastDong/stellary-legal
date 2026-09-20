# 개인정보 처리방침 / Privacy Policy

**Stellary** — 오늘 별이 이쁘네요 / The Stars Are Beautiful Tonight / 今夜は星が綺麗ですね / 今夜星光真美

> ### ✏️ 게시 전 채울 항목 / Fill in before publishing
>
> 아래 표의 값은 이 문서 본문 어디에도 다시 적혀 있지 않습니다. **표만 채우면 본문의
> "개발자", "연락처", "관할" 참조가 모두 이 표를 가리킵니다.** 값을 지어내지 마십시오 —
> 표가 비어 있는 채로 이 문서를 스토어에 연결하면 안 됩니다.
> The values in this table appear nowhere else in this document. **Fill the table once**; every
> reference to "the Developer", "the contact address" or "the governing law" in the body points
> here. Do not publish this document with the table unfilled.
>
> | 항목 / Field | 값 / Value |
> | --- | --- |
> | 개발자·사업자명 / Developer or business name (“**Developer**”) | `Buseoragi (Donghyeok Kang)` |
> | 연락처 이메일 / Contact email (“**Contact**”) | `stellary2486@gmail.com` |
> | 준거법·관할 / Governing law and venue | `대한민국 법, 서울중앙지방법원 / Laws of the Republic of Korea; Seoul Central District Court` |
> | 이 방침의 공개 URL / Hosted policy URL (Steam 스토어의 Privacy Policy URL 항목에 동일 주소 등록) | `https://github.com/FastDong/stellary-legal/blob/main/PRIVACY.md` |
> | 시행일 / Effective date | `2026-09-21` |
>
> 이 문서는 법률 자문이 아닙니다. 상업적 출시 전 필요하다면 전문가 검토를 받으십시오.
> This document is not legal advice; have it reviewed before commercial release if needed.

---

## 한국어

### 요약

Stellary는 **계정 로그인을 요구하지 않고, 개발자 서버가 없으며, 개인정보를 수집·저장·전송하지
않습니다.** 회원가입, 로그인, 애널리틱스, 광고 SDK, 크래시 업로드가 일절 없습니다. 앱이
읽는 데이터는 **이 PC 안에 머무릅니다.** 이 PC 밖으로 나가는 요청은 (1) 사용자가 동의해서 켠
**본인 공개 Steam 프로필 조회** 한 가지와 (2) 식별 정보가 실리지 않는 **Valve 공개 페이지
조회**뿐이며, 모두 Valve 서버로만 향합니다. 이 문서는 코드가 실제로 하는 일을 그대로 적은
것이며, 코드가 바뀌면 이 문서도 같은 커밋에서 바뀝니다.

### 1. 이 PC에서 읽는 정보

별하늘을 만들기 위해 앱은 사용자의 컴퓨터에 **이미 존재하는** Steam 클라이언트 파일을 읽기
전용으로 엽니다. Steam 계정에 로그인하지 않고, Steam Web API 키를 쓰지 않습니다.

| 읽는 것 | 위치 | 용도 |
| --- | --- | --- |
| Steam 설치 경로 | 레지스트리 `HKCU`/`HKLM` `…\Valve\Steam` | 아래 파일들을 찾기 위해 |
| 활성 Steam 계정 식별자(SteamID64) | `<steam>\config\loginusers.vdf` | 어느 계정의 기록을 읽을지 정하고, 3항의 선택적 프로필 조회 주소에 사용 |
| 설치된 게임 목록 | `<steam>\steamapps\appmanifest_*.acf` (모든 라이브러리 폴더, `libraryfolders.vdf` 기준) | "보유" 별 — 설치가 확인된 게임 |
| 플레이 기록·플레이 시간 | `<steam>\userdata\<accountid>\config\localconfig.vdf` | "플레이한" 별, 별 크기·조건부 이미지 |
| 게임 이름·장르·태그 | `<steam>\appcache\appinfo.vdf` | 검색, 정보 패널, `#태그` 필터 |

앱은 **Steam 비밀번호, 결제 정보, 친구 목록, 채팅, 이메일 주소, 지갑, 인벤토리 내용에 접근하지
않습니다.** 위 파일 외의 Steam 데이터는 열지 않습니다.

### 2. 네트워크 통신 — 나가는 요청 전부

모든 요청은 **HTTPS GET(조회)** 이며, **Valve가 운영하는 `steamcommunity.com`·
`store.steampowered.com`으로만** 갑니다. 앱은 자체 HTTP 클라이언트(WinHTTP)를 **쿠키 비활성**
상태로 쓰므로, 브라우저나 Steam 클라이언트의 로그인 세션·쿠키가 함께 전송되는 일이 없습니다.
**어떤 데이터도 업로드하지 않으며, 개발자나 제3자 서버는 존재하지 않습니다.**

#### 2-1. 식별 정보가 실리는 유일한 요청 — 본인 공개 프로필 조회 (기본 꺼짐, 동의해야 켜짐)

| 요청 | 함께 전송되는 정보 | 상태 |
| --- | --- | --- |
| `https://steamcommunity.com/profiles/<SteamID64>/?xml=1` 및 `…/games?tab=all&xml=1` | **회원님의 SteamID64** (URL에 포함) | **기본값은 꺼짐이며, 동의 전에는 보내지 않습니다.** 첫 실행 때 나타나는 선택 창에서 **사용**을 고르거나 설정 > 일반 > **온라인 프로필 사용**을 켜면, **켜 두는 동안 하늘을 새로 읽을 때마다**(켠 직후 한 번, 이후 시작 시·약 6시간마다·수동 새로고침 때) 보냅니다. "나중에"를 고르거나 창을 닫으면 꺼진 채로 남습니다. |

이 요청은 회원님 **본인의** 공개 프로필에 적힌 "가장 많이 플레이한 게임" 목록을 읽어 하늘에
더하기 위한 것입니다. SteamID64는 Steam 프로필 주소에 쓰이는 공개 식별자이지만, 이 앱에서
**식별 정보가 이 PC 밖으로 나가는 지점은 이 하나뿐**이므로 기본으로 꺼 두고, 사용자가
직접 켜야만 보내며, 언제든 다시 끌 수 있게 했습니다(끄면 다음 새로고침부터 보내지 않습니다). 꺼 두어도
1항의 로컬 파일만으로 보유·플레이 별은 그대로 표시됩니다. 요청은 Valve 서버로만 가며,
개발자에게 전달되지 않습니다. (Valve는 자사 서버에 대한 요청을 자사 정책에 따라 처리합니다
— 4항.)

#### 2-2. 식별 정보가 실리지 않는 공개 조회

| 요청 대상 | 목적 | 함께 전송되는 정보 |
| --- | --- | --- |
| `store.steampowered.com/search/results/?specials=1…` | 할인 중인 게임을 별로 표시(세일 레이더) | UI 표시 언어 |
| `store.steampowered.com/tagdata/populartags/english` | 태그 번호 → 태그 이름 | 없음 |
| `store.steampowered.com/api/appdetails?appids=<id>&filters=genres` | `appinfo.vdf`에 없는 게임의 장르 | 조회 대상 게임의 앱 ID |
| `steamcommunity.com/app/<id>` | 이름을 아직 모르는 게임의 제목 | 조회 대상 게임의 앱 ID |

이 요청들에는 계정 식별자가 들어가지 않습니다. 다만 모든 인터넷 요청이 그렇듯 Valve 서버는
회원님의 **IP 주소**와, 앱 ID가 실린 요청의 경우 **어떤 게임이 조회되었는지**를 알 수 있습니다.
조회 결과는 3항의 캐시에 저장되어 재요청을 줄입니다(할인 목록 6시간, 태그 이름 7일, 게임
이름·장르는 영구).

#### 2-3. 별을 더블클릭할 때

별을 더블클릭하면 앱은 `steam://` 주소로 **Steam 클라이언트를 여는 것**이 전부입니다(보유 게임은
라이브러리 페이지, 그 외는 스토어 페이지). 그 뒤의 통신은 Valve의 Steam 클라이언트가 자체
정책에 따라 수행하며, 이 앱은 관여하지 않습니다.

### 3. 이 PC에 저장되는 것

모든 저장은 사용자 PC 안에서만 일어나며 외부로 전송되지 않습니다.

| 위치 | 내용 |
| --- | --- |
| `%LOCALAPPDATA%\LibraryGalaxy\config\` | 설정(`transition-settings.txt` — 동의 여부·프로필 조회 on/off 포함), 활성 콘텐츠 선택 |
| `%LOCALAPPDATA%\LibraryGalaxy\cache\` | 2-2항 조회 결과 캐시: 할인 목록, 게임 이름, 장르, 태그 이름 |
| `%LOCALAPPDATA%\LibraryGalaxy\assets\` | 사용자가 직접 넣은 이미지(6항) |
| `%LOCALAPPDATA%\LibraryGalaxy\crash\` | 크래시 미니덤프(5항) |
| `%TEMP%\LibraryGalaxyWidget\` | 선택적 제외 목록 `steam_excluded_apps.txt`, 로그를 켰을 때의 워커 로그 (네트워크 응답은 메모리로만 받으며 임시 파일에 쓰지 않습니다) |
| `%TEMP%\LibraryGalaxyWidget-debug.log` | 진단 로그(5항) — **로그를 켰을 때만** 생성 |
| 레지스트리 `HKCU\…\CurrentVersion\Run` | 설정 > 일반 > "윈도우 시작 시 실행"을 **켰을 때만** 실행 파일 경로 한 줄을 기록. 끄면 삭제 |

폴더 이름의 `LibraryGalaxy`/`LibraryGalaxyWidget`은 Stellary의 개발 코드명입니다. 앱을 삭제한
뒤 위 폴더(와 켜 두었다면 위 레지스트리 값)를 지우면 모든 데이터가 제거됩니다. 앱은 삭제 후
남는 데이터를 다른 곳에 두지 않습니다.

### 4. 제3자 — Valve Corporation

이 앱이 통신하는 유일한 상대는 Valve(Steam)의 서버입니다. Valve가 그 요청으로 받는 정보(IP
주소, 조회된 앱 ID, 2-1항을 켠 경우 SteamID64)는 **Valve의 개인정보 보호정책과 Steam 구독자
계약**에 따라 처리되며, 개발자는 그 정보에 접근할 수 없습니다. 개발자는 Valve Corporation과
제휴·후원·승인 관계가 없습니다.

### 5. 진단 로그와 크래시 덤프 — 모두 로컬, 모두 사용자가 삭제 가능

- **진단 로그**는 기본으로 **꺼져** 있습니다. 사용자가 `--log` 플래그로 실행했을 때만
  `%TEMP%\LibraryGalaxyWidget-debug.log`에 기록되며, 기록 전에 계정 식별자와 경로에 든
  사용자명을 **마스킹**합니다. 로그는 어디에도 전송되지 않으며, 버그를 재현할 때 사용자가
  직접 개발자에게 보내기로 선택하지 않는 한 개발자는 볼 수 없습니다.
- **크래시 미니덤프**는 앱이 비정상 종료될 때 `%LOCALAPPDATA%\LibraryGalaxy\crash\`에
  Windows의 최소 형식(`MiniDumpNormal`: 스레드 스택과 모듈 목록만, 힙 메모리 제외)으로
  저장됩니다. **자동으로 업로드되지 않으며 크래시 리포터가 없습니다.** 사용자가 파일을 언제든
  지울 수 있고, 지원 요청 시 첨부할지도 사용자가 정합니다.

### 6. 사용자가 추가한 이미지 (커스텀 에셋)

사용자가 `%LOCALAPPDATA%\LibraryGalaxy\assets\` 아래 슬롯 폴더나 실행 파일 옆 `workshop/`
폴더에 넣은 이미지·테마 파일은 **이 PC에서만 읽히고 업로드되지 않습니다.** 이 기능은 로컬
폴더 기반의 데이터 전용 레이어이며 Steam 창작마당(Workshop)과 연동되지 않습니다. 추가한
콘텐츠의 저작권·적법성에 대한 책임은 사용자에게 있습니다(EULA 2항).

### 7. 바탕화면 미리보기

갤럭시 화면의 "바탕화면으로 돌아가기" 버튼에 미리보기를 띄우기 위해, 갤럭시를 열 때 **주
모니터 화면을 한 번 캡처**합니다. 이 이미지는 **메모리에만 존재**하며 디스크에 저장되거나
전송되지 않고 앱 종료 시 사라집니다. `--no-desktop-thumb` 플래그로 캡처 자체를 끌 수 있습니다.

### 8. Steam 인벤토리 아이템 (선택 사항)

**이 버전(1.0)에는 Steam 인벤토리 아이템이 없으며, 앱은 Steam 인벤토리 서비스와 통신하지 않습니다.** 나중에 추가하면 이 절을 갱신하고 시행일을 바꿉니다.

### 9. 아동의 개인정보

앱은 개인정보를 수집하지 않으므로 아동으로부터 수집하는 정보도 없습니다.

### 10. 사용자의 권리

개발자는 회원님에 관한 어떤 데이터도 보유하지 않으므로, 열람·정정·삭제 요청의 대상이 되는
데이터가 개발자 측에 없습니다. 이 PC에 있는 데이터는 3항의 경로를 직접 지워 삭제할 수 있고,
2-1항의 조회는 설정에서 언제든 끌 수 있습니다. Valve가 보유한 데이터에 대한 권리는 Valve에
행사합니다.

### 11. 변경 및 문의

방침이 바뀌면 상단 표의 **공개 URL**과 스토어 페이지에 새 버전을 게시하고 표의 **시행일**을
갱신합니다. 문의는 상단 표의 **연락처 이메일**로 보내 주십시오. 이 방침의 준거법과 관할은
상단 표의 **준거법·관할** 항목을 따릅니다.

---

## English

### Summary

Stellary requires **no account, runs no developer server, and collects, stores, or transmits no
personal data.** There is no sign-up, no login, no analytics, no advertising SDK, and no crash
upload. Everything the app reads **stays on this PC.** Only two kinds of request ever leave it:
(1) a lookup of **your own public Steam profile**, which is off until you consent, and (2)
**public Valve page lookups** that carry no identifier — and both go only to Valve's servers.
This document describes what the code actually does; when the code changes, this document
changes in the same commit.

### 1. What the app reads on this PC

To build your sky, the app opens, read-only, Steam client files that **already exist** on your
computer. It does not log in to your Steam account and uses no Steam Web API key.

| Data | Location | Purpose |
| --- | --- | --- |
| Steam install path | Registry `HKCU`/`HKLM` `…\Valve\Steam` | To find the files below |
| Active Steam account identifier (SteamID64) | `<steam>\config\loginusers.vdf` | To pick whose history to read, and for the optional profile lookup in §3 |
| Installed games | `<steam>\steamapps\appmanifest_*.acf` (every library folder, via `libraryfolders.vdf`) | "Owned" stars — games whose installation is verified |
| Play history and playtime | `<steam>\userdata\<accountid>\config\localconfig.vdf` | "Played" stars, star size, conditional images |
| Game names, genres, tags | `<steam>\appcache\appinfo.vdf` | Search, info panel, `#tag` filters |

The app does **not** access your Steam password, payment details, friends list, chat, email
address, wallet, or inventory contents. It opens no Steam data other than the files above.

### 2. Network use — every outbound request

Every request is an **HTTPS GET (read)** and goes **only to Valve-operated `steamcommunity.com`
and `store.steampowered.com`.** The app uses its own HTTP client (WinHTTP) with **cookies
disabled**, so no browser or Steam-client login session or cookie is ever sent along.
**Nothing is uploaded, and there is no developer or third-party server.**

#### 2-1. The one request that carries an identifier — your own public profile (off by default, on only with consent)

| Request | Data included | State |
| --- | --- | --- |
| `https://steamcommunity.com/profiles/<SteamID64>/?xml=1` and `…/games?tab=all&xml=1` | **Your SteamID64** (in the URL) | **Off by default; never sent before you consent.** If you choose **Use it** in the prompt shown at first launch, or turn on Settings > General > **Use online Steam profile**, it is sent **each time the sky is refreshed while the switch is on** (once right after you turn it on, then at startup, about every 6 hours, and on a manual refresh). Choosing "Not now" or closing the prompt leaves it off. |

This lookup reads the "most played" list on **your own** public profile and adds those games to
the sky. Your SteamID64 is the public identifier used in Steam profile URLs, but because this is
**the only point in this app where an identifier leaves your PC**, it is off by default, sent
only after you turn it on yourself, and can be turned off again at any time (once off, it is not sent
at the next refresh or after). With it off, owned and played stars still appear from the
local files in §1. The request goes only to Valve's servers and is never passed to the developer.
(Valve handles requests to its own servers under its own policy — see §4.)

#### 2-2. Public lookups that carry no identifier

| Endpoint | Purpose | Data included |
| --- | --- | --- |
| `store.steampowered.com/search/results/?specials=1…` | Show games currently on sale as stars (the sale radar) | Your UI display language |
| `store.steampowered.com/tagdata/populartags/english` | Tag number → tag name | None |
| `store.steampowered.com/api/appdetails?appids=<id>&filters=genres` | Genres of games missing from `appinfo.vdf` | The app ID being looked up |
| `steamcommunity.com/app/<id>` | Title of a game whose name is not yet known | The app ID being looked up |

No account identifier is included in these requests. As with any internet request, Valve's
servers can see your **IP address** and, for requests that carry an app ID, **which games were
queried.** Results are cached locally (§3) to reduce repeat requests (sale list 6 hours, tag
names 7 days, game names and genres permanently).

#### 2-3. When you double-click a star

Double-clicking a star does one thing: it opens the **Steam client** via a `steam://` URL (the
library page for your games, the store page otherwise). Any communication after that is
performed by Valve's Steam client under Valve's own policy; this app is not involved.

### 3. What is stored on this PC

All storage happens on your PC only and is never transmitted.

| Location | Contents |
| --- | --- |
| `%LOCALAPPDATA%\LibraryGalaxy\config\` | Settings (`transition-settings.txt`, including your consent state and the profile-lookup switch), active content selection |
| `%LOCALAPPDATA%\LibraryGalaxy\cache\` | Cached results of the §2-2 lookups: sale list, game names, genres, tag names |
| `%LOCALAPPDATA%\LibraryGalaxy\assets\` | Images you added yourself (§6) |
| `%LOCALAPPDATA%\LibraryGalaxy\crash\` | Crash minidumps (§5) |
| `%TEMP%\LibraryGalaxyWidget\` | The optional exclusion list `steam_excluded_apps.txt` and the worker log when logging is on (network responses are read into memory only and never written to temporary files) |
| `%TEMP%\LibraryGalaxyWidget-debug.log` | Diagnostic log (§5) — **created only when logging is turned on** |
| Registry `HKCU\…\CurrentVersion\Run` | **Only if** you turn on Settings > General > "Start with Windows": one value holding the executable path. Removed when you turn it off |

The folder names `LibraryGalaxy` / `LibraryGalaxyWidget` are Stellary's development codename.
Deleting these folders (and the registry value, if you enabled it) after uninstalling removes
all data. The app leaves nothing anywhere else.

### 4. Third party — Valve Corporation

The only party this app communicates with is Valve's (Steam's) servers. What Valve receives
through those requests (your IP address, the app IDs queried, and your SteamID64 if §2-1 is
enabled) is handled under **Valve's Privacy Policy and the Steam Subscriber Agreement**; the
developer has no access to it. The developer is not affiliated with, sponsored by, or endorsed
by Valve Corporation.

### 5. Diagnostic log and crash dumps — local only, deletable by you

- The **diagnostic log** is **off by default.** It is written to
  `%TEMP%\LibraryGalaxyWidget-debug.log` only when you launch with the `--log` flag, and account
  identifiers and the user name in paths are **redacted** before being written. The log is never
  transmitted; the developer only sees it if you choose to send it yourself when reproducing a bug.
- **Crash minidumps** are written to `%LOCALAPPDATA%\LibraryGalaxy\crash\` when the app
  terminates abnormally, in Windows' minimal format (`MiniDumpNormal`: thread stacks and the
  module list only, no heap memory). **They are never uploaded automatically and there is no crash
  reporter.** You can delete the files at any time, and whether to attach one to a support
  request is your decision.

### 6. Images you add (custom assets)

Images and theme files you place in the slot folders under `%LOCALAPPDATA%\LibraryGalaxy\assets\`
or in the `workshop/` folder next to the executable are **read on this PC only and never
uploaded.** This is a local, data-only folder layer; it has no connection to the Steam Workshop.
You are responsible for the copyright and legality of any content you add (EULA §2).

### 7. Desktop preview

To show a preview on the "return to desktop" button, the app **captures the primary monitor's
screen once** when the galaxy opens. That image is **kept in memory only** — never written to
disk or transmitted — and is discarded when the app closes. The `--no-desktop-thumb` flag
disables the capture entirely.

### 8. Steam Inventory items (optional)

**This version (1.0) has no Steam Inventory items and the app does not talk to the Steam Inventory Service.** If that changes, this section and the effective date will be updated.

### 9. Children's privacy

The app collects no personal data, and therefore collects none from children.

### 10. Your rights

The developer holds no data about you, so there is no data on the developer's side to access,
correct, or delete. Data on this PC can be deleted by removing the paths in §3, and the lookup
in §2-1 can be turned off in Settings at any time. Rights over data held by Valve are exercised
with Valve.

### 11. Changes and contact

When this policy changes, the new version is posted at the **hosted policy URL** in the table
at the top and on the store page, and the table's **effective date** is updated. Questions go
to the **contact email** in that table. This policy is governed by the **governing law and
venue** named in that table.
