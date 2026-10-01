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
> | 시행일 / Effective date | `2026-09-30` |
>
> 이 문서는 법률 자문이 아닙니다. 상업적 출시 전 필요하다면 전문가 검토를 받으십시오.
> This document is not legal advice; have it reviewed before commercial release if needed.

---

## 한국어

### 요약

Stellary는 **계정 로그인을 요구하지 않고, 개발자 서버가 없으며, 개발자는 어떤 개인정보도
수집하거나 받지 않습니다.** 회원가입, 로그인, 애널리틱스, 광고 SDK, 크래시 업로드가 일절
없습니다. 앱이 읽고 저장하는 데이터는 **이 PC 안에 머무릅니다.** 앱은 **Steam 클라이언트의 폴더
안에 있는 파일을 열지 않습니다.** 이 PC 밖으로 나가는 요청은 (1) 사용자가 직접 자신의 키를 붙여 넣어 켜는 **Steam
라이브러리 연결**(본인 Steam Web API 키로 하는 보유 게임 조회) 한 가지와 (2) 계정 식별 정보가
실리지 않는 **Valve 공개 스토어 조회**뿐이며, 모두 Valve 서버로만 향합니다. 이 문서는 코드가
실제로 하는 일을 그대로 적은 것이며, 코드가 바뀌면 이 문서도 같은 커밋에서 바뀝니다.

### 1. 이 PC에서 읽는 정보

앱은 **Steam 클라이언트의 파일(Steam 설치 폴더와 라이브러리 폴더 안의 파일)을 열지 않고**, Steam
계정에 로그인하지도 않습니다. 별하늘을 만들기 위해 Windows 레지스트리에서 다음 값만 읽기 전용으로
읽습니다.

| 읽는 것 | 위치 | 용도 |
| --- | --- | --- |
| Steam 설치 여부와 실행 파일 경로 | 레지스트리 `HKCU\Software\Valve\Steam`(`SteamExe`, `SteamPath`), `HKLM\Software\Valve\Steam`·`HKLM\Software\WOW6432Node\Valve\Steam`(`InstallPath`) | Steam이 설치돼 있는지 확인하고, 별을 더블클릭할 때 Steam 클라이언트를 열기 위해 |
| 이 PC에 설치된 Steam 게임 | Windows의 설치된 앱 목록 `HKLM`/`HKCU` `…\Microsoft\Windows\CurrentVersion\Uninstall\Steam App <앱 ID>` 항목의 표시 이름(`DisplayName`) | "내 게임" 별. Steam이 설치한 게임마다 만들어 두는 목록으로, Windows 설정 > 앱에서 게임을 지울 때 쓰이는 바로 그 목록입니다 |
| 지금 Steam에 로그인한 계정의 번호 | `HKCU\Software\Valve\Steam\ActiveProcess`의 `ActiveUser` | 2-1항의 연결을 켠 동안: 조회할 계정을 정하기 위해(하늘을 새로 읽을 때, 그리고 은하가 떠 있는 동안 약 30초마다 계정이 바뀌었는지 확인). 설정 > Steam 라이브러리 탭을 보고 있는 동안: 프로필 주소 입력 버튼이 필요한지 보기 위해(2초마다). **연결하지 않았으면 그 탭을 볼 때 말고는 읽지 않습니다** |

앱은 **Steam 비밀번호, 결제 정보, 친구 목록, 채팅, 이메일 주소, 지갑, 인벤토리 내용에 접근하지
않습니다.** 플레이 시간과 이 PC에 설치하지 않은 보유 게임은 2-1항의 연결을 켰을 때 Valve가
돌려주는 값만 씁니다. 2-4항의 설치 상태 확인도 같은 설치된 앱 목록만 읽습니다.

### 2. 네트워크 통신 — 나가는 요청 전부

모든 요청은 **HTTPS GET(조회)** 이며, **Valve가 운영하는 `api.steampowered.com`·
`store.steampowered.com`으로만** 갑니다. 앱은 자체 HTTP 클라이언트(WinHTTP)를 **쿠키 비활성**
상태로 쓰므로, 브라우저나 Steam 클라이언트의 로그인 세션·쿠키가 함께 전송되는 일이 없습니다.
**어떤 데이터도 업로드하지 않으며, 개발자나 제3자 서버는 존재하지 않습니다.**

#### 2-1. 식별 정보가 실리는 유일한 요청 — Steam 라이브러리 연결 (기본 꺼짐, 키를 붙여 넣어야 켜짐)

| 요청 | 함께 전송되는 정보 | 언제 |
| --- | --- | --- |
| `https://api.steampowered.com/IPlayerService/GetOwnedGames/v1/` | **회원님의 Steam Web API 키와 SteamID64** (URL에 포함) | **기본값은 꺼짐이며, 키를 붙여 넣기 전에는 보내지 않습니다.** 설정 > Steam 라이브러리에서 키를 붙여 넣은 뒤, 하늘을 새로 읽을 때마다 보냅니다: 키를 저장한 직후, 시작 시, 약 6시간마다(조회에 실패했거나 계정을 몰랐으면 30분 뒤), 수동 새로고침 때, Steam에 로그인한 계정이 바뀌었을 때 |
| `https://api.steampowered.com/ISteamUser/ResolveVanityURL/v1/` | 키와, 회원님이 붙여 넣은 프로필 주소의 사용자 지정 이름(`steamcommunity.com/id/<이름>`의 `<이름>`) | 로그인한 계정을 찾지 못해 사용자가 그런 형식의 주소를 붙여 넣었을 때만, 그 이름을 SteamID64로 바꾸기 위해 보냅니다. 한 번 바뀌면 숫자로 저장해 다시 보내지 않습니다 |

이 연결은 이 PC에 설치하지 않은 게임까지 포함한 **보유 게임 전체와 플레이 시간**을 하늘에 더하기
위한 것입니다. 받는 값은 게임마다 앱 ID, 이름, 플레이 시간뿐입니다. SteamID64는 1항의 로그인
계정 번호로 만들고, 찾지 못하면 회원님이 붙여 넣은 프로필 주소나 지난번에 조회에 성공한 계정을
씁니다.

- **키는 회원님이 직접 발급받습니다.** 설정의 "API 키 발급받기" 버튼은 기본 브라우저로 Valve의
  `https://steamcommunity.com/dev/apikey` 페이지를 열 뿐이며, 앱은 그 페이지와 통신하지 않습니다.
  클립보드는 "키 붙여넣기"(또는 "프로필 주소 붙여넣기") 버튼을 누른 순간에만 한 번 읽으며, 앱이
  스스로 클립보드를 읽는 일은 없습니다.
- **키는 이 PC에 암호화해 저장하며, Steam 공식 API(api.steampowered.com)로만 보냅니다.** 암호화는
  Windows DPAPI(현재 Windows 사용자 전용)이고, 파일은 3항의 `steam-webapi.dat`입니다. 키는 진단
  로그, 알림, 크래시 덤프 경로 어디에도 적지 않습니다.
- **같은 Steam 페이지에서 언제든 키를 폐기할 수 있습니다. 키는 누구와도 공유하지 마십시오.**
- 설정 > Steam 라이브러리의 **연결 해제**를 누르면 키 파일과 보유 목록 캐시(3항)를 이 PC에서
  지우고, 그 뒤로는 이 요청들을 보내지 않습니다.
- 첫 실행 안내가 끝나면 "Steam 라이브러리를 연결할까요?" 창이 한 번 뜹니다. **연결하기**는
  설정의 Steam 라이브러리 탭을 열 뿐 아무것도 보내지 않으며, **나중에**를 고르거나 창을 닫으면
  꺼진 채로 남습니다.

연결하지 않아도 1항의 설치된 게임과 세일 별은 그대로 표시됩니다. 요청은 Valve 서버로만 가며,
개발자에게 전달되지 않습니다. (Valve는 자사 서버에 대한 요청을 자사 정책에 따라 처리합니다
— 4항.)

#### 2-2. 계정 식별 정보가 실리지 않는 공개 조회

| 요청 대상 | 목적 | 함께 전송되는 정보 |
| --- | --- | --- |
| `store.steampowered.com/search/results/?specials=1…` | 할인 중인 게임을 별로 표시(세일 레이더) | UI 표시 언어 |
| `api.steampowered.com/IStoreBrowseService/GetItems/v1/` (키 없음) | 게임 이름, 종류(게임인지 DLC·사운드트랙·도구인지 — 게임이 아닌 항목을 별에서 빼기 위해), 태그 | 조회 대상 게임들의 앱 ID (언어·국가는 영어·미국으로 고정) |
| `store.steampowered.com/tagdata/populartags/english` | 태그 번호 → 태그 이름 | 없음 |
| `store.steampowered.com/api/appdetails?appids=<id>&filters=genres` | 게임의 장르 | 조회 대상 게임의 앱 ID |

이 요청들에는 계정 식별자가 들어가지 않습니다. 다만 모든 인터넷 요청이 그렇듯 Valve 서버는
회원님의 **IP 주소**와, 앱 ID가 실린 요청의 경우 **어떤 게임이 조회되었는지**를 알 수 있습니다.
조회되는 앱 ID에는 이 PC에 설치된 게임(2-1항을 켠 경우 보유 게임)이 들어갑니다. 조회 결과는
3항의 캐시에 저장되어 재요청을 줄입니다(할인 목록 6시간, 태그 이름 7일, 스토어 항목 30일, 게임
이름·장르는 영구).

#### 2-3. 별을 더블클릭할 때

별을 더블클릭하면 앱은 `steam://` 주소로 **Steam 클라이언트를 여는 것**이 전부입니다(보유 게임은
라이브러리 페이지, 그 외는 스토어 페이지). Steam이 설치되지 않은 PC에서는 대신 기본 브라우저로
`store.steampowered.com`의 그 게임 페이지를 엽니다. 그 뒤의 통신은 Valve의 Steam 클라이언트나
브라우저가 수행하며, 이 앱은 관여하지 않습니다.

#### 2-4. Steam 설치 상태 확인

실행 파일이 Steam 라이브러리 폴더(`<라이브러리>\steamapps\common\<폴더>\`) 안에 있을 때만, 앱은
1항의 Windows 설치된 앱 목록에서 **자기 자신의 항목**(`Steam App 5302160`)을 읽어 설치 상태를
확인합니다. 그 항목이 자기 폴더를 가리키지 않으면(체험판처럼 앱 ID가 다른 경우) 목록의 Steam
항목들을 훑어 설치 폴더(`InstallLocation`)가 같은 항목을 찾습니다. 읽는 때는 시작할 때와 실행 중
약 10분마다이고, 항목이 비어 보이면 한 번 더(시작 때는 3초 뒤, 실행 중에는 15초 뒤) 읽습니다.
Steam 폴더의 파일은 열지 않으며, 읽은 내용은 이 판단에만 쓰이고 PC 밖으로 나가지 않습니다.
네트워크 요청도 없습니다.

- **제거·환불**: Steam은 게임을 지울 때 이 항목의 값을 비웁니다. 항목이 비어 있으면(두 번 확인)
  앱은 3항의 자동 시작 레지스트리 값을 지우려고 시도한 뒤 종료합니다. 항목 자체가 없으면 판단할
  수 없으므로 아무것도 하지 않습니다. Steam에서 제거할 때 Steam이 실행하는 제거 스크립트
  (`Stellary.exe --uninstall`)도, 같은 Windows 사용자로 실행될 때는 같은 값을 지우고 실행 중인 앱을
  닫습니다. 다만 앱이 꺼진 상태에서 제거되고 Steam이 제거 스크립트를 다른 계정(시스템 계정 등)으로
  실행하면 값이 남을 수 있습니다. 남은 값은 이미 지워진 파일을 가리켜 아무것도 실행하지 않지만,
  없애려면 작업 관리자 > 시작 앱에서 `Stellary`를 사용 안 함으로 바꾸거나 3항의 레지스트리 값을
  직접 지우십시오. `%LOCALAPPDATA%\LibraryGalaxy`의 설정·이미지는 지우지 않습니다.
- **업데이트**: 앱은 업데이트를 이유로 스스로 종료하거나 다시 실행하지 않습니다. 새 버전은 Steam이
  자체 방식대로 설치합니다.
- **Steam에서 실행했을 때**: Steam의 "플레이"로 실행한 앱은 다른 게임처럼 Steam에 실행 중으로
  표시됩니다. 이 표시(친구 목록의 "플레이 중" 포함)와 플레이 시간 기록은 Steam 클라이언트가 자체
  정책에 따라 처리하며, 이 앱은 그 과정에서 데이터를 보내거나 저장하지 않습니다.

### 3. 이 PC에 저장되는 것

모든 저장은 사용자 PC 안에서만 일어나며 외부로 전송되지 않습니다.

| 위치 | 내용 |
| --- | --- |
| `%LOCALAPPDATA%\LibraryGalaxy\config\` | 설정(`transition-settings.txt` — 첫 실행 질문을 이미 보았는지 포함), 활성 콘텐츠 선택. 그리고 2-1항을 켰을 때만 `steam-webapi.dat`: Web API 키, 마지막으로 조회에 성공한 SteamID64, 붙여 넣은 프로필 주소. 파일 전체를 Windows DPAPI로 암호화해 이 PC의 같은 Windows 사용자만 풀 수 있으며, **연결 해제** 때 삭제됩니다 |
| `%LOCALAPPDATA%\LibraryGalaxy\cache\` | 2-2항 조회 결과 캐시: 할인 목록(`steam-specials.txt`), 게임 이름(`steam-names.txt`), 스토어 항목(`steam-storeitems.txt` — 앱 ID·종류·이름·태그 번호), 장르(`steam-genres.txt`), 태그 이름(`steam-tagnames.txt`). 그리고 2-1항을 켰을 때만 보유 목록(`steam-owned.txt` — 보유 게임의 앱 ID·플레이 시간(분)·이름, 저장 시각, 어느 계정의 목록인지 가리는 해시. SteamID64 자체는 적지 않습니다). 보유 목록은 오프라인에서도 하늘을 보여 주려고 두며, **연결 해제** 때 삭제됩니다. 설정 > 일반 > "저장된 게임 데이터" 삭제는 이 폴더의 캐시를 모두 지웁니다(키는 그대로) |
| `%LOCALAPPDATA%\LibraryGalaxy\assets\` | 사용자가 직접 넣은 이미지(6항) |
| `%LOCALAPPDATA%\LibraryGalaxy\crash\` | 크래시 미니덤프(5항) |
| `%TEMP%\LibraryGalaxyWidget\` | 선택적 제외 목록 `steam_excluded_apps.txt`, 로그를 켰을 때의 워커 로그 (네트워크 응답은 메모리로만 받으며 임시 파일에 쓰지 않습니다) |
| `%TEMP%\LibraryGalaxyWidget-debug.log` | 진단 로그(5항) — **로그를 켰을 때만** 생성 |
| 레지스트리 `HKCU\…\CurrentVersion\Run` | 설정 > 일반 > "윈도우 시작 시 실행"을 **켰을 때만** 실행 파일 경로 한 줄(값 이름 `Stellary`)을 기록. 끄면 삭제. Steam에서 제거·환불하면 앱이 삭제를 시도합니다(2-4항, 이전 버전의 값 이름 `TheStarsAreBeautifulTonight`도 함께). 앱이 꺼진 채 제거되면 남을 수 있으니, 그때는 작업 관리자 > 시작 앱에서 사용 안 함으로 바꾸거나 이 값을 직접 지우십시오 |

폴더 이름의 `LibraryGalaxy`/`LibraryGalaxyWidget`은 Stellary의 개발 코드명입니다. 앱을 삭제한
뒤 위 폴더(와 켜 두었다면 위 레지스트리 값)를 지우면 모든 데이터가 제거됩니다. 앱은 삭제 후
남는 데이터를 다른 곳에 두지 않습니다. 2-1항의 키를 Steam 쪽에서도 없애려면 발급받은 Steam
페이지에서 폐기하십시오.

### 4. 제3자 — Valve Corporation

이 앱이 통신하는 유일한 상대는 Valve(Steam)의 서버입니다. Valve가 그 요청으로 받는 정보(IP
주소, 조회된 앱 ID, 2-1항을 켠 경우 회원님의 Web API 키와 SteamID64)는 **Valve의 개인정보
보호정책과 Steam 구독자 계약**에 따라 처리되며, 개발자는 그 정보에 접근할 수 없습니다. 개발자는
Valve Corporation과 제휴·후원·승인 관계가 없습니다.

### 5. 진단 로그와 크래시 덤프 — 모두 로컬, 모두 사용자가 삭제 가능

- **진단 로그**는 기본으로 **꺼져** 있습니다. 사용자가 `--log` 플래그로 실행했을 때만
  `%TEMP%\LibraryGalaxyWidget-debug.log`에 기록되며, 기록 전에 계정 식별자와 경로에 든
  사용자명을 **마스킹**합니다. 2-1항의 Web API 키는 로그에 전혀 적지 않습니다. 로그는 어디에도
  전송되지 않으며, 버그를 재현할 때 사용자가 직접 개발자에게 보내기로 선택하지 않는 한 개발자는
  볼 수 없습니다.
- **크래시 미니덤프**는 앱이 비정상 종료될 때 `%LOCALAPPDATA%\LibraryGalaxy\crash\`에
  Windows의 최소 형식(`MiniDumpNormal`: 스레드 스택과 모듈 목록만, 힙 메모리 제외)으로
  저장됩니다(파일 이름은 `연월일-시분초.dmp`). 일반적인 크래시뿐 아니라 C++ 런타임의 치명
  오류(`abort`·`std::terminate`, 잘못된 인자 호출, 순수 가상 함수 호출)도 같은 형식으로 같은 폴더에
  남기며, 이 경우 앱은 오류 창을 띄우지 않고 바로 종료되고 Windows 오류 보고도 호출하지 않습니다.
  그 밖의 크래시는 덤프를 남긴 뒤 여느 프로그램처럼 Windows가 처리합니다. **자동으로 업로드되지
  않으며 크래시 리포터가 없습니다.** 사용자가 파일을 언제든 지울 수 있고, 지원 요청 시 첨부할지도
  사용자가 정합니다.
- **보관 기간**: 앱은 시작할 때마다 이 폴더에서 **가장 최근 덤프 5개만 남기고 오래된 덤프를 스스로
  삭제**합니다. 그 외의 파일은 건드리지 않습니다.
- **알림과 폴더 열기**: 새 덤프가 생기면 다음 실행 때 은하 화면에 **한 번만** 알림이 뜹니다. 은하가
  숨겨져 있거나 첫 실행 안내·확인 창이 떠 있으면 같은 실행 안에서 볼 수 있게 될 때 다시 시도하고,
  알림이 실제로 화면에 보였을 때만 알린 것으로 기록합니다. 폴더는 트레이 메뉴의
  **"크래시 보고서 폴더 열기"**로 언제든 열 수 있습니다. 알림과 폴더 열기 모두 아무것도 전송하지
  않습니다.

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
2-1항의 연결은 설정 > Steam 라이브러리의 **연결 해제**로 언제든 끊을 수 있습니다(키와 보유
목록이 이 PC에서 지워집니다). 키 자체는 발급받은 Steam 페이지에서 폐기합니다. Valve가 보유한
데이터에 대한 권리는 Valve에 행사합니다.

### 11. 변경 및 문의

방침이 바뀌면 상단 표의 **공개 URL**과 스토어 페이지에 새 버전을 게시하고 표의 **시행일**을
갱신합니다. 문의는 상단 표의 **연락처 이메일**로 보내 주십시오. 이 방침의 준거법과 관할은
상단 표의 **준거법·관할** 항목을 따릅니다.

---

## English

### Summary

Stellary requires **no account and runs no developer server, and the developer collects or
receives no personal data.** There is no sign-up, no login, no analytics, no advertising SDK, and no
crash upload. Everything the app reads and stores **stays on this PC.** The app **does not open any file inside the
Steam client's folders.** Only two kinds of request ever leave this PC: (1) the **Steam library
connection** you turn on yourself by pasting your own key (a lookup of your owned games with your own
Steam Web API key), and (2) **public Valve store lookups** that carry no account identifier — and
both go only to Valve's servers. This document describes what the code actually does; when the code
changes, this document changes in the same commit.

### 1. What the app reads on this PC

The app **does not open Steam client files (anything inside the Steam install folder or its library
folders)** and does not log in to your Steam account. To build your sky it reads only the following
Windows registry values, read-only.

| Data | Location | Purpose |
| --- | --- | --- |
| Whether Steam is installed, and its executable path | Registry `HKCU\Software\Valve\Steam` (`SteamExe`, `SteamPath`), `HKLM\Software\Valve\Steam` and `HKLM\Software\WOW6432Node\Valve\Steam` (`InstallPath`) | To check that Steam is installed, and to open the Steam client when you double-click a star |
| Steam games installed on this PC | The display name (`DisplayName`) of the `Steam App <app ID>` entries in the Windows installed-apps list, `HKLM`/`HKCU` `…\Microsoft\Windows\CurrentVersion\Uninstall` | "My games" stars. Steam creates one entry per installed game; it is the same list Windows Settings > Apps uses to uninstall games |
| The number of the account signed in to Steam right now | `ActiveUser` under `HKCU\Software\Valve\Steam\ActiveProcess` | While the §2-1 connection is on: to decide which account to look up (when the sky is refreshed, and about every 30 seconds while the galaxy is open, to notice an account change). While you are looking at the Settings > Steam library tab: to decide whether the profile-link button is needed (every 2 seconds). **Without the connection it is read only while that tab is on screen** |

The app does **not** access your Steam password, payment details, friends list, chat, email
address, wallet, or inventory contents. Playtime, and owned games that are not installed on this
PC, come only from what Valve returns when the §2-1 connection is on. The install check in §2-4
reads the same installed-apps list and nothing else.

### 2. Network use — every outbound request

Every request is an **HTTPS GET (read)** and goes **only to Valve-operated `api.steampowered.com`
and `store.steampowered.com`.** The app uses its own HTTP client (WinHTTP) with **cookies
disabled**, so no browser or Steam-client login session or cookie is ever sent along.
**Nothing is uploaded, and there is no developer or third-party server.**

#### 2-1. The one request that carries an identifier — the Steam library connection (off by default, on only after you paste a key)

| Request | Data included | When |
| --- | --- | --- |
| `https://api.steampowered.com/IPlayerService/GetOwnedGames/v1/` | **Your Steam Web API key and your SteamID64** (in the URL) | **Off by default; never sent before you paste a key.** After you paste a key in Settings > Steam library, it is sent each time the sky is refreshed: right after the key is saved, at startup, about every 6 hours (30 minutes after a failed lookup or when no account was known), on a manual refresh, and when the account signed in to Steam changes |
| `https://api.steampowered.com/ISteamUser/ResolveVanityURL/v1/` | Your key and the custom name in a profile link you pasted (the `<name>` in `steamcommunity.com/id/<name>`) | Only when no signed-in account could be found and you pasted a link of that form, to turn the name into a SteamID64. Once resolved, the number is stored and the name is not sent again |

This connection adds **every game you own, including games not installed on this PC, and your
playtime** to the sky. What comes back is only each game's app ID, name, and playtime. The
SteamID64 is built from the signed-in account number in §1; if none is found, the app uses the
profile link you pasted or the account of the last successful lookup.

- **You get the key yourself.** The "Get my API key" button in Settings only opens Valve's
  `https://steamcommunity.com/dev/apikey` page in your default browser; the app does not talk to
  that page. The clipboard is read once, only at the moment you press "Paste key" (or "Paste profile
  URL"); the app never reads the clipboard on its own.
- **Your key is stored encrypted on this PC and only sent to Steam's official API
  (api.steampowered.com).** It is encrypted with Windows DPAPI (your current Windows user only) in
  `steam-webapi.dat` (§3). The key is never written to the diagnostic log, notifications, or crash
  dump paths.
- **You can revoke it anytime on the same Steam page. Never share it with anyone.**
- Pressing **Disconnect** in Settings > Steam library deletes the key file and the saved library
  list (§3) from this PC, and these requests stop.
- After the first-run guide, a "Connect your Steam library?" prompt appears once. **Connect** only
  opens the Steam library tab in Settings and sends nothing; choosing **Later** or closing the prompt
  leaves the connection off.

Without the connection, the installed games in §1 and the sale stars still appear. The requests go
only to Valve's servers and are never passed to the developer. (Valve handles requests to its own
servers under its own policy — see §4.)

#### 2-2. Public lookups that carry no account identifier

| Endpoint | Purpose | Data included |
| --- | --- | --- |
| `store.steampowered.com/search/results/?specials=1…` | Show games currently on sale as stars (the sale radar) | Your UI display language |
| `api.steampowered.com/IStoreBrowseService/GetItems/v1/` (no key) | Game names, type (game or DLC, soundtrack, tool — to keep non-games out of the sky), tags | The app IDs being looked up (language and country fixed to English / US) |
| `store.steampowered.com/tagdata/populartags/english` | Tag number → tag name | None |
| `store.steampowered.com/api/appdetails?appids=<id>&filters=genres` | A game's genres | The app ID being looked up |

No account identifier is included in these requests. As with any internet request, Valve's
servers can see your **IP address** and, for requests that carry an app ID, **which games were
queried.** The app IDs looked up include the games installed on this PC (and, with §2-1 on, the
games you own). Results are cached locally (§3) to reduce repeat requests (sale list 6 hours, tag
names 7 days, store items 30 days, game names and genres permanently).

#### 2-3. When you double-click a star

Double-clicking a star does one thing: it opens the **Steam client** via a `steam://` URL (the
library page for your games, the store page otherwise). On a PC without Steam it opens that game's
`store.steampowered.com` page in your default browser instead. Any communication after that is
performed by Valve's Steam client or your browser; this app is not involved.

#### 2-4. Steam install check

Only when the executable sits in a Steam library folder (`<library>\steamapps\common\<folder>\`),
the app reads **its own entry** (`Steam App 5302160`) in the Windows installed-apps list from §1 to
check its install state. If that entry does not point to its own folder (a playtest or demo has a
different app ID), it looks through the list's Steam entries for the one whose install folder
(`InstallLocation`) is the same. It reads at startup and about every 10 minutes while running, and
once more when the entry looks empty (after 3 seconds at startup, after 15 seconds while running).
It opens no file in the Steam folders; what it reads is used only for this check and never leaves
the PC, and no network request is made.

- **Uninstall or refund**: Steam empties this entry's values when it uninstalls a game. If the
  entry is empty (checked twice), the app tries to remove the autostart registry value in §3 and
  exits. If the entry does not exist at all, it cannot tell, so it does nothing. The uninstall
  script Steam runs when you uninstall (`Stellary.exe --uninstall`) removes the same value and
  closes the running app when it runs as the same Windows user. If the app is not running when you
  uninstall and Steam runs the script under another account (such as the system account), the value
  can remain. It then points to a deleted file and starts nothing; to remove it, disable `Stellary`
  in Task Manager > Startup apps or delete the registry value in §3 yourself. Your settings and
  images in `%LOCALAPPDATA%\LibraryGalaxy` are not deleted.
- **Updates**: the app never exits or restarts itself because of an update. Steam installs new
  versions in its own way.
- **When started from Steam**: a copy started with Steam's Play button shows in Steam as running,
  like any game. That status (including "playing" in your friends list) and the playtime record are
  handled by the Steam client under Valve's policy; the app sends or stores nothing in the process.

### 3. What is stored on this PC

All storage happens on your PC only and is never transmitted.

| Location | Contents |
| --- | --- |
| `%LOCALAPPDATA%\LibraryGalaxy\config\` | Settings (`transition-settings.txt`, including whether the first-run question was already shown), active content selection. Also, only when §2-1 is on, `steam-webapi.dat`: your Web API key, the SteamID64 of the last successful lookup, and a profile link you pasted. The whole file is encrypted with Windows DPAPI so only the same Windows user on this PC can read it, and it is deleted by **Disconnect** |
| `%LOCALAPPDATA%\LibraryGalaxy\cache\` | Cached results of the §2-2 lookups: sale list (`steam-specials.txt`), game names (`steam-names.txt`), store items (`steam-storeitems.txt` — app ID, type, name, tag numbers), genres (`steam-genres.txt`), tag names (`steam-tagnames.txt`). Also, only when §2-1 is on, the owned-games list (`steam-owned.txt` — app ID, playtime in minutes and name of each owned game, when it was saved, and a hash that tells which account the list belongs to; the SteamID64 itself is not written). The list keeps the sky working offline and is deleted by **Disconnect**. Settings > General > "Cached game data" Delete removes every cache in this folder (the key stays) |
| `%LOCALAPPDATA%\LibraryGalaxy\assets\` | Images you added yourself (§6) |
| `%LOCALAPPDATA%\LibraryGalaxy\crash\` | Crash minidumps (§5) |
| `%TEMP%\LibraryGalaxyWidget\` | The optional exclusion list `steam_excluded_apps.txt` and the worker log when logging is on (network responses are read into memory only and never written to temporary files) |
| `%TEMP%\LibraryGalaxyWidget-debug.log` | Diagnostic log (§5) — **created only when logging is turned on** |
| Registry `HKCU\…\CurrentVersion\Run` | **Only if** you turn on Settings > General > "Start with Windows": one value (named `Stellary`) holding the executable path. Removed when you turn it off. When you uninstall or refund it through Steam the app tries to remove it (§2-4; together with the earlier versions' value name `TheStarsAreBeautifulTonight`); if the app was not running at the time it can remain, so disable it in Task Manager > Startup apps or delete the value yourself |

The folder names `LibraryGalaxy` / `LibraryGalaxyWidget` are Stellary's development codename.
Deleting these folders (and the registry value, if you enabled it) after uninstalling removes
all data. The app leaves nothing anywhere else. To retire the §2-1 key on Steam's side as well,
revoke it on the Steam page where you got it.

### 4. Third party — Valve Corporation

The only party this app communicates with is Valve's (Steam's) servers. What Valve receives
through those requests (your IP address, the app IDs queried, and your Web API key and SteamID64
if §2-1 is on) is handled under **Valve's Privacy Policy and the Steam Subscriber Agreement**; the
developer has no access to it. The developer is not affiliated with, sponsored by, or endorsed
by Valve Corporation.

### 5. Diagnostic log and crash dumps — local only, deletable by you

- The **diagnostic log** is **off by default.** It is written to
  `%TEMP%\LibraryGalaxyWidget-debug.log` only when you launch with the `--log` flag, and account
  identifiers and the user name in paths are **redacted** before being written. The §2-1 Web API key
  is never written to the log at all. The log is never transmitted; the developer only sees it if
  you choose to send it yourself when reproducing a bug.
- **Crash minidumps** are written to `%LOCALAPPDATA%\LibraryGalaxy\crash\` when the app
  terminates abnormally, in Windows' minimal format (`MiniDumpNormal`: thread stacks and the
  module list only, no heap memory), named `yyyymmdd-hhmmss.dmp`. Besides ordinary crashes, fatal
  C++ runtime errors (`abort` / `std::terminate`, an invalid-parameter call, a pure virtual call) are
  written the same way to the same folder; in that case the app ends at once without an error
  dialog and without invoking Windows Error Reporting. Any other crash is left to Windows after the
  dump is written, as for any program. **They are never uploaded automatically and there is no crash
  reporter.** You can delete the files at any time, and whether to attach one to a support
  request is your decision.
- **Retention**: every time it starts, the app **keeps only the 5 most recent dumps in that folder
  and deletes older ones itself.** It touches no other files.
- **Notice and folder shortcut**: when a new dump exists, the next launch shows a notice in the
  galaxy **once**. While the galaxy is hidden or the first-run guide or a confirmation is up, the
  same run tries again once it can be seen, and the notice counts as given only after it has
  actually been on screen. The tray menu item **"Open crash reports folder"** opens the folder at any time. Neither the
  notice nor the shortcut sends anything anywhere.

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
correct, or delete. Data on this PC can be deleted by removing the paths in §3, and the §2-1
connection can be ended at any time with **Disconnect** in Settings > Steam library (the key and the
saved library list are deleted from this PC); the key itself is revoked on the Steam page where you
got it. Rights over data held by Valve are exercised with Valve.

### 11. Changes and contact

When this policy changes, the new version is posted at the **hosted policy URL** in the table
at the top and on the store page, and the table's **effective date** is updated. Questions go
to the **contact email** in that table. This policy is governed by the **governing law and
venue** named in that table.
