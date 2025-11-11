
---

## ✅ 결론:

**Markdown → (Pandoc 변환) → Word(.docx)**
을 자동화하는 **GitHub Actions 워크플로우 파이프라인**이 가장 깔끔한 모범사례야.

즉,

* **작성 및 버전관리:** Markdown 파일 (`chapters/01.md` 등)
* **자동화:** Pandoc + CSL 스타일 + 참고문헌(`.bib`)
* **출력:** `.docx` (학교 docs 양식 템플릿 유지)

---

## 📁 추천 디렉토리 구조

```text
thesis/
├── chapters/
│   ├── 01-introduction.md
│   ├── 02-related-work.md
│   ├── 03-methodology.md
│   ├── 04-results.md
│   └── 05-conclusion.md
├── refs/
│   ├── references.bib
│   └── citation-style.csl        # (예: APA, IEEE 등)
├── templates/
│   └── thesis-template.docx      # 학교 제출용 Word 양식 (네가 업로드한 docs 기반)
├── .github/
│   └── workflows/
│       ├── build-chapters.yml    # 각 장별 docx 변환 + references 포함
│       └── merge-thesis.yml      # 전체 병합 논문 생성
└── docs/
    └── thesis.docx               # 최종 결과물
```

---

## 🧩 워크플로우 1 — 각 장별 `.docx` 생성 + 레퍼런스 포함

```yaml
# .github/workflows/build-chapters.yml
name: Build per-chapter docs

on:
  push:
    paths:
      - 'chapters/*.md'
      - 'refs/*.bib'
      - 'templates/thesis-template.docx'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install pandoc
        run: sudo apt-get update && sudo apt-get install -y pandoc

      - name: Build chapter Word files
        run: |
          mkdir -p build/chapters
          for f in chapters/*.md; do
            base=$(basename "$f" .md)
            pandoc "$f" \
              --from markdown \
              --to docx \
              --citeproc \
              --bibliography=refs/references.bib \
              --csl=refs/citation-style.csl \
              --reference-doc=templates/thesis-template.docx \
              -o build/chapters/${base}.docx
          done

      - name: Upload artifacts
        uses: actions/upload-artifact@v4
        with:
          name: chapter-docs
          path: build/chapters/
```

📘 **결과:**
각 장(`01-introduction.docx`, `02-related-work.docx` …)이
학교 제출용 `.docx` 스타일(`thesis-template.docx`)을 그대로 따르면서
각 장 끝에 인용문헌이 자동 정리돼.

---

## 🧩 워크플로우 2 — 모든 장 병합해서 하나의 논문(`docs/thesis.docx`)으로 생성

```yaml
# .github/workflows/merge-thesis.yml
name: Merge Thesis DOCX

on:
  push:
    branches: [ main ]
    paths:
      - 'chapters/*.md'
      - 'refs/*.bib'
      - 'templates/thesis-template.docx'

jobs:
  merge:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install pandoc
        run: sudo apt-get update && sudo apt-get install -y pandoc

      - name: Merge Markdown chapters to DOCX
        run: |
          mkdir -p docs
          pandoc chapters/*.md \
            --from markdown \
            --to docx \
            --citeproc \
            --bibliography=refs/references.bib \
            --csl=refs/citation-style.csl \
            --reference-doc=templates/thesis-template.docx \
            -o docs/thesis.docx

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: thesis-docx
          path: docs/thesis.docx
```

이렇게 하면 push할 때마다
📄 `docs/thesis.docx`가 자동으로 최신 Word 양식으로 생성돼.
그 문서가 바로 **제출 가능한 최종본**이 돼.

---

## 🧠 참고로 이런 세부 설정도 가능해

| 목적       | Pandoc 옵션                                        | 설명                 |
| -------- | ------------------------------------------------ | ------------------ |
| 문단 들여쓰기  | `--reference-doc=templates/thesis-template.docx` | 기존 Word 양식의 스타일 유지 |
| 인용 방식    | `--csl=refs/citation-style.csl`                  | APA, IEEE 등 지정 가능  |
| 그림/표 캡션  | `--filter pandoc-crossref`                       | 자동 번호 매김           |
| 수식 표현    | 기본 LaTeX 문법 사용 (`$...$`)                         | Word 내 수식으로 변환     |
| 목차 자동 생성 | `--toc --toc-depth=3`                            | Word 목차 자동 삽입      |

---

## 🧩 결과적으로 이런 파이프라인이 돼

```
(작성)
Markdown (*.md)
     ↓
(push 시 자동 실행)
GitHub Actions
     ↓
Pandoc 변환
     ↓
병합 및 스타일 적용
     ↓
docs/thesis.docx
     ↓
(제출)
학교 제출용 Word 문서
```

---

## 🚀 추가 팁

* **Word 템플릿(`thesis-template.docx`) 만들기 팁**

  1. 학교 양식 문서 열기
  2. `제목1`, `본문`, `인용`, `참고문헌` 등 스타일 지정
  3. `파일 > 다른 이름으로 저장 > Word Template` 으로 저장
  4. `templates/thesis-template.docx`로 프로젝트에 넣기

* **PR 기반으로 논문 검토 가능**

  * PR을 올리면 Actions가 자동으로 `thesis.docx`를 빌드해서 리뷰어가 확인 가능.

---

## 📘 결론 요약

| 구분    | 내용                                               |
| ----- | ------------------------------------------------ |
| 작성 포맷 | Markdown (`chapters/*.md`)                       |
| 인용 관리 | BibTeX (`refs/references.bib`)                   |
| 스타일   | Word Template (`templates/thesis-template.docx`) |
| 빌드 도구 | Pandoc + GitHub Actions                          |
| 결과물   | `docs/thesis.docx` (제출용)                         |
| 모범사례  | Markdown 원고 관리 + 자동 Word 변환 파이프라인 구축             |

---

원하면 지금 네가 업로드한 `tae.pdf`, `kangho.pdf`를 분석해서
그걸 그대로 재현하는 **Word 템플릿(`thesis-template.docx`)**을 만들어줄 수 있어.
그걸 기준으로 Pandoc 옵션을 딱 맞게 설정하면 “학교 양식 그대로 자동 생성”이 가능해.
그걸 진행해볼까?
