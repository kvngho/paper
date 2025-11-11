# 논문 작성 워크플로우

이 레포지토리는 Markdown으로 논문을 작성하고, Pandoc을 통해 자동으로 Word 문서로 변환하는 파이프라인을 제공합니다.

## 📁 디렉토리 구조

```
.
├── chapters/                    # 논문 각 장 마크다운 파일
│   ├── 01-introduction.md
│   └── 02-related-work.md
├── refs/                        # 참고문헌 관리
│   ├── references.bib          # BibTeX 형식 참고문헌
│   └── citation-style.csl      # 인용 스타일 (IEEE)
├── templates/                   # Word 템플릿
│   └── thesis-template.docx    # 학교 제출용 양식
├── .github/workflows/           # GitHub Actions 자동화
│   ├── build-chapters.yml      # 각 장별 docx 변환
│   └── merge-thesis.yml        # 전체 논문 병합
└── docs/                        # 최종 결과물
    └── thesis.docx             # 자동 생성된 논문 (병합본)
```

## 🚀 사용 방법

### 1. 논문 작성

`chapters/` 디렉토리에 마크다운 파일을 작성합니다.

```bash
# 새 장 추가
vim chapters/03-methodology.md
```

### 2. 참고문헌 추가

`refs/references.bib` 파일에 BibTeX 형식으로 참고문헌을 추가합니다.

```bibtex
@article{author2024title,
  title={논문 제목},
  author={저자},
  journal={학술지},
  year={2024}
}
```

### 3. 마크다운에서 인용하기

```markdown
선행 연구에 따르면 [@author2024title], ...
```

### 4. 자동 변환

`main` 브랜치에 push하면 GitHub Actions가 자동으로:
- 각 장별 Word 파일 생성 (`build/chapters/`)
- 전체 병합 논문 생성 (`docs/thesis.docx`)

## 📝 로컬에서 테스트

Pandoc이 설치되어 있다면 로컬에서 직접 변환할 수 있습니다:

```bash
# Pandoc 설치 (macOS)
brew install pandoc

# 전체 논문 생성
pandoc chapters/*.md \
  --from markdown \
  --to docx \
  --citeproc \
  --bibliography=refs/references.bib \
  --csl=refs/citation-style.csl \
  --reference-doc=templates/thesis-template.docx \
  -o docs/thesis.docx

# 특정 장만 변환
pandoc chapters/01-introduction.md \
  --from markdown \
  --to docx \
  --citeproc \
  --bibliography=refs/references.bib \
  --csl=refs/citation-style.csl \
  --reference-doc=templates/thesis-template.docx \
  -o docs/01-introduction.docx
```

## 🔧 커스터마이징

### 인용 스타일 변경

`refs/citation-style.csl` 파일을 다른 스타일로 교체:
- APA: https://raw.githubusercontent.com/citation-style-language/styles/master/apa.csl
- IEEE: https://raw.githubusercontent.com/citation-style-language/styles/master/ieee.csl
- Chicago: https://raw.githubusercontent.com/citation-style-language/styles/master/chicago-author-date.csl

### Word 템플릿 수정

1. `templates/thesis-template.docx` 파일을 Word로 엽니다
2. 스타일 (제목1, 본문 등)을 학교 양식에 맞게 수정합니다
3. 저장 후 commit & push하면 자동으로 반영됩니다

## 📚 마크다운 팁

### 이미지 삽입
```markdown
![캡션](이미지경로.png)
```

### 표 작성
```markdown
| 항목 | 값 |
|------|-----|
| A    | 1   |
| B    | 2   |
```

### 수식
```markdown
인라인 수식: $E = mc^2$

블록 수식:
$$
\frac{1}{n}\sum_{i=1}^{n}x_i
$$
```

## 🎯 워크플로우 흐름

```
(작성) Markdown (*.md)
    ↓
(push) GitHub Actions
    ↓
(변환) Pandoc + BibTeX
    ↓
(출력) docs/thesis.docx
    ↓
(제출) 학교 제출용 Word 문서
```

## 📌 참고사항

- 인용문헌은 각 장 끝에 자동으로 추가됩니다
- Word 템플릿의 스타일이 최종 문서에 적용됩니다
- GitHub Actions는 `chapters/`, `refs/`, `templates/` 파일 변경 시에만 실행됩니다
