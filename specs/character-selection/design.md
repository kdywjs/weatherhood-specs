# 캐릭터 선택 설계

요구사항 원본: [requirements.md](requirements.md).

## 화면 제안
- 제품 화면은 430×932이며 좌우 여백은 20px이다. Noto Sans KR, 옅은 하늘색 배경과 청색 강조를 재사용한다.
- 상단에 뒤로가기와 `캐릭터 선택` 제목, 중앙에 큰 캐릭터 미리보기, 아래에 종류 썸네일을 배치한다.
- 하단 `적용` 버튼은 흐름 제안이다. 썸네일을 누르면 임시 선택과 미리보기가 바뀌고, 적용하면 선택을 반영해 이전 화면으로 돌아가며, 뒤로가기는 미적용 선택을 취소하는 방안을 제안한다. 실제 동작은 합의 전이다.
- 예시 썸네일은 기존 캐릭터 한 개와 A·B 자리 표시 두 개다. 세 슬롯은 배치 검토용이며 출시 종류·개수를 뜻하지 않는다.
- Figma 선택 화면에서는 기존 캐릭터 벡터를 큰 미리보기와 작은 썸네일에 재사용한다. 화면 배치용 벡터와 아래의 Blender 의상 시안을 구분하며, 기존 Figma 화면에 3D 자산을 자동 반영하지 않는다.
- 선택 상태는 `✓ 선택됨`과 테두리로 표현한다. 옷 편집, 눈·몸체 편집 조작은 이 종류 선택 시안에 넣지 않는다. 눈·몸체 편집의 제품 범위는 미정이다.
- 900×932 비교 프레임에 제품 화면과 화면 밖 검토 메모를 함께 둔다. 기존 Figma 프레임을 보존하고 별도 비교 시안으로 구분한다.

## 데이터와 제약
캐릭터 설정은 로그인한 사용자별로 서버 DB에 저장한다. 종류 식별자·목록 공급·저장 API·동기화·실패 처리는 미정이다. 날씨 표현의 입력 기준은 [날씨 표현 설계](../weather-presentation/design.md)를 따르며, 기온별 의상 구간·대기질 등급별 마스크·자산 조합은 미정이다. 정적 시안에는 데이터 계약이나 동작 연결을 추가하지 않는다.

마이페이지의 기존 `캐릭터 꾸미기` 메뉴는 MVP에 맞게 `캐릭터 선택`으로 정리할 필요가 있다. 이 작업에서는 필요 사항만 보고하며 마이페이지 문서와 화면을 변경하지 않는다.

## 날씨별 3D 의상 시안

의상 검토에는 기존 보라색 오리너구리를 사용한다. 선택한 머리·얼굴 형태를 유지하고 몸에 맞는 둥근 의상으로 제작한다. 이는 출시 캐릭터 목록이나 앱용 완성 자산의 확정을 뜻하지 않는다.

기본 옷 4종과 날씨 대응 5종을 대표 착장으로 비교한다. 계절 이름은 시안 구분이며 실제 적용 기온의 경계는 아직 정하지 않는다.

| 시안 | 구성 |
| --- | --- |
| 봄 · 온화함 | 얇은 긴팔티, 긴바지 |
| 여름 · 더움 | B1 반팔티, 반바지 |
| 가을 · 쌀쌀함 | A1 더플코트, 긴바지 |
| 겨울 · 추움 | C2 패딩, 도톰한 긴바지 |
| 비 | 우비, 장화, 굵은 J자 손잡이·우산대와 둥근 캐노피의 우산 |
| 눈 | 겨울옷, 군밤 모자·목도리·장갑·방한화. 모자는 갈색 겉감과 크림색 털 앞판·귀덮개로 구성한다. |
| 진눈깨비 | 따뜻한 옷, 방수 외투·장화 |
| 강한 바람 | 바람막이, 쓴 후드 |
| 황사·미세먼지 | 계절 옷, 부리에 맞춘 입체 마스크 |

- `BODY`, `CLOTHES`, `GEAR` 컬렉션으로 몸·옷·날씨 장비를 구분한다. 장비는 별도 오브젝트로 관리하되, 다른 착장과의 모든 조합에서 간섭 없는 착용을 보장하는 런타임 시스템은 이번 범위에 포함하지 않는다.
- 기존 캐릭터·의상 버전을 보존한다. 새 9종을 같은 비교 공간에 배치하고 원형 받침대·글자·보조선 없이 캐릭터를 볼 수 있게 한다.
- 맑음·구름 많음·흐림·밤은 기본 옷을 재사용하고 배경·조명으로 구분하는 방향이다. 해당 배경·조명의 개별 자산 제작은 이번 9종에 포함하지 않는다.
- 기온·풍속·대기질 기준, 중복 날씨의 우선순위, 결측 시 의상 유지 규칙은 미정이다. 황사·미세먼지 데이터 공급 연결도 별도 범위다.
- 이번 산출물은 정적 Blender 의상 시안이다. 새 의상의 애니메이션 스키닝, 앱용 메시 최적화·내보내기, 자동 의상 변경과 앱 렌더링은 별도 작업이다.

## 확장 옷장 시안

사용자가 제안한 전체 옷·아이템 제작을 요청한 범위다. 기존 9종에 아래 21개 착장을 추가한다. 원본 얼굴·몸과 기존 의상은 보존하고, 같은 Blender 파일에서 계절·날씨별로 비교한다. 독립된 소품 오브젝트는 편집할 수 있지만 모든 의상 조합의 자동 피팅이나 앱용 아이템 시스템을 의미하지 않는다.

| 분류 | 착장 | 제작 구성 |
| --- | --- | --- |
| 봄 | E01–E03 | 둥근 가디건·꽃 크로스백, 줄무늬 반팔·멜빵바지, 후드 집업·작은 백팩 |
| 여름 | E04–E06 | 민소매·둥근 반바지·버킷햇, 반팔·선글라스·손선풍기, 배색 민소매·물병 |
| 가을 | E07–E09 | 꽈배기 니트·베레모, 니트 조끼·체크 머플러, 오버롤·도토리 가방 |
| 겨울 | E10–E12 | 떡볶이 코트·두꺼운 목도리, 양털 재킷·귀마개·벙어리장갑, 롱패딩·털 안감 바지 |
| 눈·한파 | E13–E15 | 스키복·이마 위 고글, 퀼팅 조끼·방울 비니, 주머니와 손에 든 핫팩 |
| 비 | E16–E17 | 판초 우비·방수 물방울 가방·무늬 장화, 멜빵 레인팬츠·투명 우산·꼬리 리본 |
| 바람 | E18–E19 | 아노락·넥워머, 배색 바람막이·조임끈 모자·부리 모양 가방 |
| 황사·미세먼지 | E20–E21 | 높은 지퍼 재킷, 가벼운 후드 외투, 입체 마스크·보관 파우치 |

- 큰 단추·둥근 주머니와 물고기 패치를 일부 착장에 적용한다. 소품이 작게 보이지 않도록 굵은 손잡이·가방끈과 둥근 모서리를 사용한다.
- `Wardrobe • ALL 30 looks`는 기존 9종과 새 21종을 함께 보여주는 기본 비교 장면이다. 위쪽은 기존 날씨 착장, 아래쪽은 봄/여름·가을/겨울·눈/비·바람/황사 묶음이다.
- `Wardrobe • 21 new looks`는 새 착장만 비교한다. `Expanded • Spring Summer Autumn`, `Expanded • Winter Snow`, `Expanded • Rain Wind Dust`는 확대 비교용 장면이다. 모두 동일 파일 안에서 원본 컬렉션을 참조한다.
- 새 자산도 정적 시안이다. 옷·소품의 애니메이션 스키닝, 전체 조합의 간섭 검사, 메시 최적화, 앱 내보내기는 별도 범위다.

## 남녀 한 쌍 의상 시안

- 대표 날씨 9종과 확장 21종, 총 30개 착장에 남녀 얼굴을 적용한 60개 캐릭터를 비교한다. 각 쌍은 왼쪽 남자, 오른쪽 여자이며 같은 의상을 착용한다.
- 남자는 작은 타원형 눈과 짧은 눈썹, 여자는 둥근 눈·아치형 눈썹·볼 표현으로 구분한다. 기존 보라색 머리 형태와 부리는 유지한다.
- 새 쌍에서는 꼬리와 머리 옆 하트·원형 장식을 표시하지 않는다. 이전 원본과 실험 장면은 같은 파일에 보존한다.
- 전체 비교는 `Wardrobe • ALL 60 paired looks`, 확대 비교는 `Wardrobe • Original weather pairs`, `Wardrobe • Spring Summer Autumn pairs`, `Wardrobe • Winter Snow pairs`, `Wardrobe • Rain Wind Dust pairs` 장면을 사용한다.
- 출시 성별 선택 정책이나 앱 기능의 확정이 아닌 정적 외형·착장 검토 자료다.

## 참고 및 검증
- 현재 검토본 v002: [남녀 30쌍 파일 모음](https://drive.google.com/drive/folders/1L3u0A8ROlkLnqvVipTdhwW0i_cCA0VLW). `kim946598@gmail.com` 소유, 링크가 있는 누구나 뷰어로 열람한다. v001은 이전 비교본으로 보존한다.
- v002 Blender 원본: [Blender 원본](https://drive.google.com/file/d/1FjsSiRZQpliRH1vrQXHWO9HZJnUb35Cj/view?usp=drivesdk).
- v002 전체 60개 미리보기: [전체 60개 미리보기](https://drive.google.com/file/d/1s2VyMTEPv-Cl6aDcrf5a1EGknAqgH-by/view?usp=drivesdk).
- v002 대표 날씨: [대표 날씨](https://drive.google.com/file/d/1_sSf2zWxfy5MxC9cTDuvSoLGd8plFNJM/view?usp=drivesdk).
- v002 봄·여름·가을: [봄·여름·가을](https://drive.google.com/file/d/1mQx2FRJM_vn7Ht08s6uiS80Vs7AGytFq/view?usp=drivesdk).
- v002 겨울·눈: [겨울·눈](https://drive.google.com/file/d/1sVRNRE5q-1uBV4LIXQ-iNC1VF7x4q3bI/view?usp=drivesdk).
- v002 비·바람·황사: [비·바람·황사](https://drive.google.com/file/d/1xH_sS5tuA78eYVm2sbjIvi-KNJCovhAA/view?usp=drivesdk).
- v002 사용 안내: [사용 안내](https://drive.google.com/file/d/1DLqggYzdpnWtxN064R7QBnbvSxrsApwP/view?usp=drivesdk).
- v002 제작 스크립트·검증 기록: [제작 스크립트·검증 기록](https://drive.google.com/file/d/10cKLZkrf3tpXIu9y9tfT12EihDp7iQPK/view?usp=drivesdk).
- v002 검증: 30개 착장·60개 새 캐릭터와 기존 4,738개 오브젝트의 로컬 변환·표시 상태 보존을 확인했다. 전체 카메라 구도와 대표 날씨 쌍을 확인하고 미리보기 5장을 렌더했다. 업로드 8개 파일의 로컬/Drive 바이트 크기와 누구나 읽기 권한을 대조했다. 전체 각도·애니메이션·앱 동작 검증은 포함하지 않는다.
- 이전 비교본 v001: [파일 모음](https://drive.google.com/drive/folders/1hsgIgDX2Y_PI9pjgL-mXbbEplloFIBiX). 업로드 계정은 `kim946598@gmail.com`이며 Blender 원본·미리보기 5장·제작 스크립트·사용 안내를 포함한다. 출시 확정본이 아닌 검토용 스냅샷이다. v001의 공유 권한은 이번 작업에서 변경하지 않았다. 현재 공유에는 v002 링크를 사용한다.
- 사용 안내: [README](https://drive.google.com/file/d/1hoj9cmIgQiiErZ7XX7hq66iQsm2IjFFV/view?usp=drivesdk). 제작 자료: [스크립트 및 아이템 목록 ZIP](https://drive.google.com/file/d/1V-qBdqbUD3WLGIJU-gXwPbGiaPmKq_ID/view?usp=drivesdk). 스크립트는 기존 씬과 실행 순서에 의존하며 독립 재생성 패키지가 아니다.
- 확장 제작 근거: 사용자가 제안한 전체 옷·아이템을 서브에이전트에게 분담해 제작하도록 요청했다. 봄·여름·가을, 겨울·눈, 비·바람·황사의 세 작업으로 분담하고 부모 작업에서 통합 및 피팅을 확인했다.
- 확장 비교: [새 21종](https://drive.google.com/file/d/1gyG_2U_fGWJFi6o5GiVGqEdO9UGBMSEp/view?usp=drivesdk), [기존 포함 30종](https://drive.google.com/file/d/1pN6eHM9OyGHcUcNP_QhD_H3bjjX45b3O/view?usp=drivesdk). 세부 비교는 [봄·여름·가을](https://drive.google.com/file/d/1HWTW9fpOgXs6w8gUp5_BSg-2Y__I3uy-/view?usp=drivesdk), [겨울·눈](https://drive.google.com/file/d/1N5bMtqvjYFDswopWMLy--LmYyzZugRKw/view?usp=drivesdk), [비·바람·황사](https://drive.google.com/file/d/1a84CL_YoKUZwL2aoILUuLO7Iah9zV4Ij/view?usp=drivesdk).
- 확장 검증: [검증 기록](../../../design/blender/character-gallery/expanded-wardrobe-validation.json). 제작 직전 2,394개 기존 오브젝트의 존재·로컬 변환·표시 상태·메시 정점 및 면을 대조해 변경 없음, 새 착장 컬렉션 21개를 확인했다. 정면 비교에서 모자·멜빵·조끼 피팅을 수정하고 백팩 뒷면·옆면을 확인했다. 모든 각도·동작·아이템 조합의 무간섭을 보장하는 검증은 아니다.
- 의상 제작 근거: 사용자가 A1 코트·B1 반팔티·C2 패딩을 선택하고 기존 버전 보존·동일 공간 비교를 요청했다. 이어 기본 옷 4종과 날씨 대응 5종의 구성을 확인하고 제작을 요청했다.
- 이전 v001 Blender 원본: [공유 캐릭터 갤러리](https://drive.google.com/file/d/1hLqRaJ-GDKtpyHsJFSwBj5xuFty_Xb0x/view?usp=drivesdk). 새 시안은 `S01`–`S09` 컬렉션이며 `Weather • 9 looks` 장면에서 비교한다.
- 9종 이전 배치·색상 비교 렌더: [날씨별 의상 시안](../../../design/blender/character-gallery/weather-nine-looks.png). 위에서부터 봄·여름·가을 / 겨울·비·눈 / 진눈깨비·강풍·황사 순서다.
- 우산·모자 이전 단색 우산 상세 비교: [수정 시안](../../../design/blender/character-gallery/umbrella-trapper-review.png). 사용자의 우산 두께 수정 및 군밤 모자 요청을 반영했다. 형태 참고는 [스타일화된 우산 3D 에셋](https://www.renderhub.com/shruti-s/stylized-umbrella-pack-yellow-green-3d-model)과 [군밤 모자 3D 에셋](https://sketchfab.com/3d-models/ushanka-trapper-hat-1046cd56a67d4b79902e47aa0daf3ccb)이며 외부 에셋을 다운로드하거나 모델에 포함하지 않았다.
- 3D 시안 검증: 기존 13개 캐릭터·의상 컬렉션의 오브젝트 구성·변환·표시 상태·메시·셰이프키를 작업 전 사본과 대조해 변경 없음을 확인했다. 새 9종의 머리 정점이 기존 원본과 동일하며 `BODY`·`CLOTHES`·`GEAR` 구분이 존재함을 확인했다. 마스크의 사선·뒷면과 눈 착장의 정면·측면을 시각적으로 확인했다. 모든 장비 조합의 간섭, 애니메이션과 앱 실행은 검증하지 않았다.
- 구성 참고: 사용자가 지정한 Finch·듀오링고의 큰 미리보기와 썸네일 선택 방식. 외부 제품의 상세 화면을 별도로 조사·검증하지 않았다.
- 재사용 원본: 통합 작업 공간 `design/figma/my-page-v1.json`, `design/figma/main-v3-simple.json`.
- 전달 JSON: 통합 작업 공간 `design/figma/character-selection-v1.json`. 내부 작업자의 기존 파일을 검토해 재사용했다.
- 현재 시안: [캐릭터 선택 v2 · sub01 인계 반영](https://www.figma.com/design/nIUPdpIqFoqtPBCBPwOJ3N?node-id=127-202). 인계 후 root가 기존 원본 파일에 JSON Import / Export로 가져와 확인했다. 이전 캐릭터 시안은 비교용으로 보존했다.
- JSON 파싱, 900×932 비교 프레임과 430×932 제품 화면, 총 41개 노드(프레임 11·텍스트 15·벡터 15), 모든 자식의 선언 좌표가 부모 범위 안에 있는지 확인했다. 썸네일 라벨의 선언 너비를 카드 안으로 수정했다. 선언 좌표 검사는 실제 텍스트 렌더링 검증이 아니다.
- 텍스트 15개의 Noto Sans KR 지정과 한글 보존, SVG 15개의 XML 파싱을 확인했다. 캐릭터 벡터 12개는 기존 원본의 SVG 문자열과 일치한다.
- 인계 원본의 높이 0 수평 벡터 두 개는 가져오기용 사본에서 높이 1로 보정했다. 플러그인이 노드 회전과 축소 크기를 그대로 적용하지 않아 뒤로가기 SVG에 회전을 반영하고 썸네일 SVG의 표시 크기를 명시했다. 원본 경로 데이터는 유지했다.
- 전달 JSON SHA-256: `62b4bdc6bbf30691f37df113176287c412799dc48d56bc93ed75cb626bfdb0c1`.
- 가져오기용 사본: 통합 작업 공간 `design/figma/character-selection-v2-import.json`. sub01 인계 파일과 위 SHA-256은 변경하지 않았다.
- Figma를 다시 내보내 확인한 실제 레이어는 FRAME 26개, TEXT 15개, VECTOR 35개다. 서체는 Noto Sans KR, 이미지 채우기는 없다. 실제 캔버스에서 큰 미리보기·작은 썸네일·뒤로가기·선택 표시·검토 메모의 배율과 잘림을 확인했다. 검증에 사용한 실패한 새 가져오기 사본은 정리했고 작업 전 존재한 시안은 보존했다.
- 증적: 통합 작업 공간 `design/figma/character-selection-v2-validation.json`, `character-selection-v2-preview.png`.
- 위 Figma 검증은 선택 화면의 정적 배치에 한정하며 실제 선택·적용·뒤로가기·저장 동작과 3D 자산의 앱 연동은 검증하지 않았다.
