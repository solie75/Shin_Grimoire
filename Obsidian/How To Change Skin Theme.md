# 다크 모드

setting -> Appearance -> Base color scheme 를 Dark 로 설정.

# Theme 변경

setting -> Appearance -> Theme -> Manage -> Minimal 검색 -> Install and use
https://minimal.guide/home

# Theme 커스텀

1. vault/.obsidian/snippets 폴더에 css 파일 생성
```css
/* 공통 스타일: H1 ~ H6에 진한 남색 배경과 글자 스타일 적용 */

.markdown-preview-view h1,
.markdown-preview-view h2,
.markdown-preview-view h3,
.markdown-preview-view h4,
.markdown-preview-view h5,
.markdown-preview-view h6,
.cm-s-obsidian .HyperMD-header-1,
.cm-s-obsidian .HyperMD-header-2,
.cm-s-obsidian .HyperMD-header-3,
.cm-s-obsidian .HyperMD-header-4,
.cm-s-obsidian .HyperMD-header-5,
.cm-s-obsidian .HyperMD-header-6 {
  font-weight: bold; /* 글자 굵기 */
  color: #d6d4d4; /* 글자 색상: 밝은 회색 */
  background-color: #011a33; /* 배경색: 진한 남색 */
  border-radius: 8px; /* 모서리 둥글게 */
  padding: 10px 15px; /* 내부 여백 */
  margin: 0.5em 0; /* 상하 여백 */
  line-height: 1.4; /* 줄 간격 */
  display: inline-block; /* Flexbox 사용 */
  align-items: center; /* 세로 중앙 정렬 */
  justify-content: flex-start; /* 왼쪽 정렬 */
  transition: all 0.3s ease; /* 부드러운 전환 효과 */
}


/* 미리보기 모드 스타일 */
.markdown-preview-view h1 { font-size: 3em; }
.markdown-preview-view h2 { font-size: 2.75em; }
.markdown-preview-view h3 { font-size: 2.5em; }
.markdown-preview-view h4 { font-size: 2.25em; }
.markdown-preview-view h5 { font-size: 2em; }
.markdown-preview-view h6 { font-size: 1.5em; }  

/* 편집 모드 스타일: Flexbox와 패딩 적용 */

.cm-s-obsidian .HyperMD-header-1 {
  font-size: 3em;
  padding: 10px 15px;
}

  
.cm-s-obsidian .HyperMD-header-2 {
  font-size: 2.75em;
  padding: 10px 15px;
}
  

.cm-s-obsidian .HyperMD-header-3 {
  font-size: 2.5em;
  padding: 10px 15px;
}

  
.cm-s-obsidian .HyperMD-header-4 {
  font-size: 2.25em;
  padding: 8px 12px;
}
  

.cm-s-obsidian .HyperMD-header-5 {
  font-size: 2em;
  padding: 8px 12px;
}

  
.markdown-preview-view h1:hover,
.markdown-preview-view h2:hover,
.markdown-preview-view h3:hover,
.markdown-preview-view h4:hover,
.markdown-preview-view h5:hover,
.markdown-preview-view h6:hover,
.cm-s-obsidian .HyperMD-header-1:hover,
.cm-s-obsidian .HyperMD-header-2:hover,
.cm-s-obsidian .HyperMD-header-3:hover,
.cm-s-obsidian .HyperMD-header-4:hover,
.cm-s-obsidian .HyperMD-header-5:hover,
.cm-s-obsidian .HyperMD-header-6:hover {
  background-color: #011e44; /* Hover 시 밝은 남색으로 변경 */
  transform: translateY(-3px); /* 살짝 위로 이동 */
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2); /* 그림자 추가 */
  color: #f3f1f1; /* 글자 색상 변경 */
}
```

2. setting -> Appearance -> CSS snippets 에서 생성한 css 를 enable