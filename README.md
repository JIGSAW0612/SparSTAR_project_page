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
4. **공개 페이지에 심사 중인 학회명을 쓰지 마세요.** 비익명 페이지가 특정 학회 제출 사실을 명시하면
   blind-review 정책상 제출 광고로 해석될 수 있습니다. arXiv 프리프린트 자체는 보통 허용됩니다.
   venue 배너·meta keywords·PDF 파일명·BibTeX 주석을 전부 venue-neutral하게 유지하세요.

## 공개 전 반드시 바꿀 항목

`index.html` 안의 아래 4곳만 고치면 됩니다 (모두 주석으로 표시해 두었습니다).

1. **저자 블록** (`<div class="authors">`) — 이름, 홈페이지 링크, 윗첨자 소속 번호, `*`/`†` 기호
2. **소속 줄** (`<div class="affiliation">`) — 소속명. 소속이 하나면 `<sup>N</sup>`을 전부 지우세요.
3. **버튼 행** (`.action-row`) — Code / arXiv 링크가 생기면 `<span class="button disabled">`를 `<a class="button" href="...">`로 교체
4. **BibTeX** — 현재 arXiv 프리프린트 형식. 게재 확정 후 `@inproceedings` 엔트리로 교체
5. **`review-banner`** — accept 후 해당 `<div>` 통째로 삭제

## PDF는 이 저장소에 두지 않습니다

논문·supplementary PDF는 **작업 트리와 git 이력 양쪽에서 제거**했고 `.gitignore` 로 재유입을 막아뒀습니다.
공개본은 arXiv가 호스팅하고, 페이지의 Paper/arXiv 버튼은 arXiv 링크를 겁니다.
로컬 사본은 저장소 밖 `../_papers_local/` 에 있습니다 (표·그림 재생성용).

## 현재 포함된 자산

- Figure 1~7 기반 이미지 (`attention_map`, `teaser`, `cross_scale_mass`, `clip_boundary`, `recalibration`, `method_overview`, `density_schedule`)
- 480p T2V dense/ours 나란히 비교 영상 4개 (`static/videos/`) + 포스터 JPG
- 표 이미지 (`table1_720p`, `table2_compat` = 본문 Table 1/2 크롭, `table3_480p_5s` = supplementary Table 7을 Table 1 템플릿으로 재조판)
- Method 그림은 논문 Figure 6을 400dpi로 추출 (`method_overview`)

결과 표와 method 그림은 논문 PDF에서 직접 크롭한 이미지입니다.
**표 이미지에는 논문 캡션을 포함하지 않습니다** — 설명은 페이지의 HTML 캡션이 담당합니다.

```bash
pdftoppm -png -r 300 -f 6 -l 6 ../_papers_local/SparSTAR_preprint.pdf hi
pdftoppm -png -r 400 -f 5 -l 5 ../_papers_local/SparSTAR_preprint.pdf m
pdftoppm -png -r 400 -f 5 -l 5 ../_papers_local/SparSTAR_Supplementary.pdf s

# Table 1        hi-6.png  300dpi  y  225..1062  x  225..2324
# Table 2        hi-6.png  300dpi  y 1246..1535  x  225..2324
# Figure 6       m-5.png   400dpi  y  313.. 917  x 1799..3077   (우측 단)
# (Supp Table 7은 크롭하지 않고 재조판 — 아래 참고)
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

### 480p 표 재조판

supplementary Table 7은 단일 컬럼 조판이라 본문 Table 1과 폭·서체 크기가 맞지 않습니다.
같은 템플릿(booktabs, Category 열, `#E5E5FF` 강조행, `\textwidth` = 7in)으로 다시 조판했습니다.
소스는 `static/papers/table3_480p_5s.tex` 입니다.

```bash
pdflatex table3_480p_5s.tex && pdftoppm -png -r 300 table3_480p_5s.pdf t && # 잉크 bbox 크롭 후 1600px로 리사이즈
```

> `tabular*` + `\extracolsep{\fill}` 을 쓰면 `\rowcolor` 가 열 사이 여백을 칠하지 못해 강조행이 끊깁니다. `tabularx` 를 쓰세요.

## Abstract

`index.html` 의 Abstract는 **논문 원문 그대로**입니다. 요약·재작성하지 마세요.
논문이 바뀌면 아래로 다시 뽑아 교체하고, 문자 단위로 일치하는지 확인하세요.

```bash
pdftotext -f 1 -l 1 ../_papers_local/SparSTAR_preprint.pdf - | sed -n '/^Abstract/,/^Introduction/p'
```

> pdftotext는 줄바꿈에서 공백을 흘립니다 (`asequence`, `cross-clipcontext`). 붙여넣기 전에 복원하세요.

## 비교 영상 재생성

**720p** `t2v/720p_dense` 와 `t2v/720p_ours` (sparse-only = 논문 Table 1의 SparSTAR, 1.60x) 를
좌우로 붙여 CRF 15로 인코딩했습니다. 파일명에 `_720p` 를 붙여 브라우저 캐시를 우회합니다.

| # | 프롬프트 | s_idx | LPIPS | PSNR | 최악 프레임 |
|---|---|---|---|---|---|
| 1 | A person is doing aerobics | 2 | 0.0391 | 31.52 | 30.62 |
| 2 | a refrigerator and skis | 1 | 0.0436 | 31.97 | 32.02 |
| 3 | A cute fluffy panda eating Chinese food in a restaurant | 2 | 0.0475 | 29.80 | 30.11 |
| 4 | a truck | 0 | 0.0455 | 29.73 | 28.49 |

> **480p가 아니라 720p를 쓰는 이유.** 720p는 LPIPS가 0.04 수준으로 480p(0.055~0.097)의 절반이고
> PSNR도 5dB 이상 높아 dense와의 차이가 훨씬 덜 보입니다. 무엇보다 페이지가 내세우는 수치
> (1.60x, PSNR 31.22, VBench 83.83)가 **720p Table 1 기준**이라, 480p 영상을 쓰면 표와 영상의
> 설정이 어긋납니다.
>
> 주의: `720p_ours` 는 sparse-only, `720p_ours_goff` 는 guidance-off까지 포함한 2.50x 변형입니다.
> 논문 `qualitative_paper` 의 720p **그림**은 goff를 썼지만, 페이지는 Table 1과 맞추기 위해
> sparse-only를 씁니다.
>
> 720p는 프롬프트당 샘플이 5개(`s_idx` 0~4)이므로 파일명이 `<stem>-<s_idx>.mp4` 입니다.
> 480p는 s_idx 0 하나뿐이라 이 구분이 없었습니다.

## 상단 갤러리

`static/gallery/` 는 **SparSTAR 단독 출력**입니다 (dense 비교 없음). 854px(대형 타일만 1280px), CRF 23.
레이아웃은 4열 CSS 그리드에 좌상단만 2x2로 키운 모자이크입니다.

선정 기준이 두 가지입니다.

1. **`psnr_last10` 상위** — 열화가 클립 후반에 누적되므로, 전체 평균 PSNR보다 마지막 10프레임
   PSNR이 "끝까지 안 깨지는" 클립을 더 잘 골라냅니다.
2. **카메라 이동이 겹치지 않을 것** — numpy FFT 위상상관으로 프레임 간 전역 이동을 추정해
   pan L->R / pan R->L / tilt up / tilt down / static / subject 로 분류한 뒤,
   같은 유형이 몰리지 않도록 배분했습니다.

| 파일 | 카메라 | psnr_last10 | PSNR |
|---|---|---|---|
| g1_vangogh (대형) | static | 31.70 | 33.13 |
| g2_rollerskate | tilt up | 31.60 | 31.27 |
| g3_panda | static | 31.61 | 32.37 |
| g4_cake | tilt up | 32.39 | 33.21 |
| g5_treadmill | static + 피사체 | 33.11 | 33.54 |
| g6_skateboard | pan R->L | 33.01 | 34.41 |
| g7_arabian | pan L->R | 32.06 | 33.18 |
| g8_flute | 피사체 | 31.52 | 31.87 |
| g9_clapping | static + 피사체 | 33.13 | 33.50 |

> 초판은 서재·미술관·파르테논·상하이를 넣었는데 넷 다 정적인 배경을 가로로 패닝하는
> 같은 성격이라 단조로웠습니다. 가로 패닝은 서로 반대 방향 2개만 남겼습니다.

되돌리려면:

```bash
git reset --hard before-gallery && git push --force origin main
```

## 크레딧

Academic Project Page Template 계열의 학술 프로젝트 페이지 구성과 AgilePruner / EDAFormer 페이지를 참고해 새로 작성한 초안입니다. 공개 시 footer의 크레딧을 유지하는 것을 권장합니다.
