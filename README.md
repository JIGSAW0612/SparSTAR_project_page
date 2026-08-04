# SparSTAR 프로젝트 페이지 초안

이 폴더는 **GitHub Pages에 바로 올릴 수 있는 정적 웹사이트**입니다.

## 로컬 미리보기

```bash
python -m http.server 8000
```

브라우저에서 `http://localhost:8000`을 엽니다.

## GitHub Pages 배포

1. 새 공개/비공개 GitHub 저장소를 만듭니다.
2. 이 폴더의 내용을 저장소 루트에 업로드합니다.
3. `Settings → Pages → Deploy from a branch`에서 `main / root`를 선택합니다.
4. AAAI-27 리뷰 중에는 저장소를 **비공개로 유지하거나, 익명 버전만 배포**하는 편이 안전합니다.

## 공개 전 반드시 바꿀 항목

`index.html` 안의 아래 4곳만 고치면 됩니다 (모두 주석으로 표시해 두었습니다).

1. **저자 블록** (`<div class="authors">`) — 이름, 홈페이지 링크, 윗첨자 소속 번호, `*`/`†` 기호
2. **소속 줄** (`<div class="affiliation">`) — 소속명. 소속이 하나면 `<sup>N</sup>`을 전부 지우세요.
3. **버튼 행** (`.action-row`) — Code / arXiv 링크가 생기면 `<span class="button disabled">`를 `<a class="button" href="...">`로 교체
4. **BibTeX** — 현재 arXiv 프리프린트 형식. accept 후 `@inproceedings` AAAI 엔트리로 교체
5. **`review-banner`** — accept 후 해당 `<div>` 통째로 삭제

그 외:

- `static/images/qualitative_*.gif`: 현재는 supplementary PDF의 5개 샘플 프레임으로 만든 미리보기입니다. 최종 공개본에는 원본 MP4를 권장합니다.

## 현재 포함된 자산

- 논문 및 supplementary PDF
- Figure 1, 3, 4, 5, 6, 7 기반 이미지
- 480p T2V 정성 비교 GIF 3개
- 720p T2V/I2V 핵심 결과표

## 구조

```text
index.html
static/
  css/style.css
  images/
  papers/
```

JavaScript는 없습니다. 페이지는 순수 HTML/CSS로 동작합니다.

## 크레딧

Academic Project Page Template 계열의 학술 프로젝트 페이지 구성과 AgilePruner / EDAFormer 페이지를 참고해 새로 작성한 초안입니다. 공개 시 footer의 크레딧을 유지하는 것을 권장합니다.
