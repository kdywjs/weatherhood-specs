# 웨더후드 타이포그래피

웨더후드의 제품 서체는 **LINE Seed KR**로 통일한다. 새 화면·버튼·문구·디자인에도 같은 기준을 적용한다. 과거 폰트 비교 보드와 이미지 시안은 참고 자료이며 현재 기준을 대체하지 않는다.

## 사용 기준

- 제목, 주요 버튼, 강조 숫자: Bold.
- 본문, 설명, 보조 정보: Regular.
- 한글·영문·숫자 모두 동일 서체를 사용한다. Thin 및 합성 Bold는 사용하지 않는다.
- 앱에서는 공통 `WeatherhoodTypography`를 통해 사용하고 Dynamic Type을 지원한다. 이미 `ScaledMetric`으로 확대된 크기는 중복 확대하지 않는다.
- SF Symbols와 아이콘 글리프는 텍스트 서체로 교체하지 않는다.
- iOS가 관리하는 상태 표시줄, 시스템 경고창·메뉴 등 커스텀 폰트를 지원하지 않는 영역은 OS 기본 서체를 유지한다. 서체 변경을 위해 시스템 UI를 임의 재구현하거나 비공개 API를 사용하지 않는다.
- Figma는 편집 가능한 텍스트와 로컬 텍스트 스타일에 적용한다. 기존 컴포넌트 연결·문구·색상·크기는 유지하고, 글자 폭 변경에 따른 넘침을 검토한다.
- 이미지에 합쳐진 글자와 외부 참고 자료는 소급 편집하지 않는다. 새 시안을 만들 때는 LINE Seed KR를 기준으로 제작한다.

## 배포 및 명칭

- 공식 배포본의 수정하지 않은 `LINESeedKR-Rg.ttf`, `LINESeedKR-Bd.ttf`를 사용한다.
- iOS PostScript 이름: `LINESeedSansKR-Regular`, `LINESeedSansKR-Bold`.
- Figma는 설치된 폰트 목록에서 실제 family/style을 확인한다. TTF는 family에 Regular/Bold가 포함되고 style이 Regular일 수 있다.
- 폰트 누락 시 다른 서체를 적용 완료로 보고하지 않는다.
- SIL OFL 1.1 전문과 원저작자 고지를 앱 폰트 리소스에 함께 보관한다.

## 참고 및 검증

- 2026-11-08 사용자: “1번으로 전부 통일하자 … 앞으로 모든 폰트는 저거로”. 1번은 LINE Seed KR.
- 공식 배포: https://seed.line.me/index_kr.html
- 라이선스: https://openfontlicense.org/open-font-license-official-text/
- 앱 구현·검증 현황은 `weatherhood-app/README.md` 및 `design-qa.md`에서 관리한다.
