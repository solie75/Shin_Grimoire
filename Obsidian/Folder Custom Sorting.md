1. 설치
	- option -> Community plugins -> Browse -> Custom File Explorer sorting -> install -> enable

2. 설정 문서 추가.
	1. 해당 vault 의 어떤 위치든 상관 없이 파일을 하나 생성하고 sortspec 로 명명한다.
	2. sortspec 문서에 다음의 형식으로 작성한다. 작성하면 사진과 같이 보인다.

```md
---
sorting-spec: |
  target-folder: /*       # 적용할 폴더의 범위를 지정 (여기서는 모든 폴더)
  /folders TIL            # 첫 번째 폴더
  /folders Unreal         # 두 번째 폴더
  /folders Obsidian       # 세 번째 폴더
  /folders Resource       # 네 번째 폴더
  /folders ...            # 나머지 폴더 (와일드카드)
---
```

![obsidian folder custom sort](/Image/Obsidian/custom_sort.png)
