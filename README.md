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
- 480p T2V dense/ours 나란히 비교 영상 6개 (`static/videos/`) + 포스터 JPG
- 논문 Table 1/2/3 캡처 (`table1_720p`, `table2_compat`, `table3_480p`)

결과 표는 논문 PDF에서 300dpi로 크롭한 이미지입니다. 논문 수치가 바뀌면 아래로 재생성하세요.

```bash
pdftoppm -png -r 300 -f 6 -l 7 static/papers/SparSTAR_AAAI27_Submission.pdf hi
# Table 1: hi-6.png  y 225..1190,  x 225..2324
# Table 2: hi-6.png  y 1246..1663, x 225..2324
# Table 3: hi-7.png  y 204..780,   x 225..1220  (좌측 단만)
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

## 비교 영상 재생성

`t2v/480p_dense` 와 `t2v/480p_ours` (sparse-only = 논문 기준 SparSTAR) 를 좌우로 붙여 CRF 22로 인코딩했습니다.
프롬프트는 논문 선정 규칙(motion 하위 60% 제외 후 LPIPS 오름차순)으로 후보를 뽑은 뒤,
생성 자체가 깨진 클립(예: 축구)을 눈으로 걸러내고 6개를 골랐습니다.

> 주의: `*_ours` 는 sparse-only, `*_ours_goff` 는 guidance-off까지 포함한 변형입니다.
> 논문 Table 1의 SparSTAR(1.60×)는 `*_ours` 쪽이므로 영상도 이 폴더를 써야 합니다.

## 크레딧

Academic Project Page Template 계열의 학술 프로젝트 페이지 구성과 AgilePruner / EDAFormer 페이지를 참고해 새로 작성한 초안입니다. 공개 시 footer의 크레딧을 유지하는 것을 권장합니다.
