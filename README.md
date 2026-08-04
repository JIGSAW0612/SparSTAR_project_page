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

## 현재 포함된 자산

- 논문 및 supplementary PDF
- Figure 1~7 기반 이미지 (`attention_map`, `teaser`, `cross_scale_mass`, `clip_boundary`, `recalibration`, `method_overview`, `density_schedule`)
- 480p T2V dense/ours 나란히 비교 영상 4개 (`static/videos/`) + 포스터 JPG
- 표 캡처 (`table1_720p`, `table2_compat` = 본문 Table 1/2, `table_480p_5s` = supplementary Table 7)
- Method 그림은 논문 Figure 6을 400dpi로 추출 (`method_overview`)

결과 표와 method 그림은 논문 PDF에서 직접 크롭한 이미지입니다.
**표 이미지에는 논문 캡션을 포함하지 않습니다** — 설명은 페이지의 HTML 캡션이 담당합니다.

```bash
pdftoppm -png -r 300 -f 6 -l 6 static/papers/SparSTAR_AAAI27_Submission.pdf hi
pdftoppm -png -r 400 -f 5 -l 5 static/papers/SparSTAR_AAAI27_Submission.pdf m
pdftoppm -png -r 400 -f 5 -l 5 static/papers/SparSTAR_Supplementary.pdf s

# Table 1        hi-6.png  300dpi  y  225..1062  x  225..2324
# Table 2        hi-6.png  300dpi  y 1246..1535  x  225..2324
# Figure 6       m-5.png   400dpi  y  313.. 917  x 1799..3077   (우측 단)
# Supp Table 7   s-05.png  400dpi  y  925..1755  x 1774..3099   (우측 단)
```

## 구조

```text
index.html
static/
  css/style.css
  images/
  papers/
  videos/
```

레이아웃은 Bulma(CDN) + Font Awesome + academicons를 쓰고, `static/css/style.css` 는 그 위의 커스텀만 담습니다.
AgilePruner / EDAFormer 계열 Academic Project Page Template 구조를 따릅니다. 자체 JavaScript는 없습니다.

## Abstract

`index.html` 의 Abstract는 **논문 원문 그대로**입니다. 요약·재작성하지 마세요.
논문이 바뀌면 아래로 다시 뽑아 교체하고, 문자 단위로 일치하는지 확인하세요.

```bash
pdftotext -f 1 -l 1 static/papers/SparSTAR_AAAI27_Submission.pdf - | sed -n '/^Abstract/,/^Introduction/p'
```

> pdftotext는 줄바꿈에서 공백을 흘립니다 (`asequence`, `cross-clipcontext`). 붙여넣기 전에 복원하세요.

## 비교 영상 재생성

`t2v/480p_dense` 와 `t2v/480p_ours` (sparse-only = 논문 기준 SparSTAR) 를 좌우로 붙여 CRF 22로 인코딩했습니다.
프롬프트는 motion 하위 60%를 제외한 뒤 **PSNR 내림차순**으로 후보를 뽑고,
카메라 이동이 아닌 실제 피사체 운동이 있으면서 생성이 깨지지 않은 4개를 눈으로 골랐습니다.
(car accelerating / ballet / sword fighting / skateboarding)

> 주의: `*_ours` 는 sparse-only, `*_ours_goff` 는 guidance-off까지 포함한 변형입니다.
> 논문 Table 1의 SparSTAR(1.60×)는 `*_ours` 쪽이므로 영상도 이 폴더를 써야 합니다.

## 크레딧

Academic Project Page Template 계열의 학술 프로젝트 페이지 구성과 AgilePruner / EDAFormer 페이지를 참고해 새로 작성한 초안입니다. 공개 시 footer의 크레딧을 유지하는 것을 권장합니다.
