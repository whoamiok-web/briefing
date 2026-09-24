---
name: daily-briefing
description: CIS케미칼 이차전지 재활용 업계 일일 뉴스 브리핑을 생성·커밋·알림까지 수행한다. "브리핑 만들어줘", "오늘 업계 동향", "daily briefing", 정기 실행(매일 08:00 KST) 시 사용.
---

# 일일 업계 동향 브리핑

회사 목록·보고서 구조·스타일 가이드의 기준은 저장소 루트 `CLAUDE.md`이다. 이 스킬은 그 절차를 실행 순서대로 정리한 것이며, 충돌 시 `CLAUDE.md`를 따른다.

## 0. 날짜 확정
- `TZ=Asia/Seoul date +%F` 로 오늘(KST) 날짜 `TODAY`, `TZ=Asia/Seoul date -d yesterday +%F` 로 전일 `YESTERDAY` 확정.
- 출력 파일: `briefings/${TODAY}.html`. 이미 있으면 덮어쓰기 전에 내용을 확인한다.

## 1. DART 공시 (전일 기준)
- 도구: PlayMCP `opendart-search_disclosures` (필요 시 `opendart-find_company`로 corp_code 확인).
- 대상 10개사: 성일하이텍, 새빗켐, 재영텍, 아이에스에코솔루션, 에코프로이노베이션, 에코프로씨엔지, 오르타머티리얼즈, 에너지머티리얼즈, 엘앤에프, 포스코필바라리튬솔루션.
- 기간: `YESTERDAY` 하루. 주요사항보고·투자결정·공급계약·자기주식·임원변경 등 중요 공시 위주.
- 공시가 없는 회사는 표에서 생략. 전부 없으면 "전일 신규 주요 공시 없음" 표기.

## 2. 광물 가격 (전일 기준)
- WebSearch로 조회: 탄산리튬(SMM, USD/톤), 니켈(LME, USD/톤), 코발트(LME, USD/톤).
- 가격·기준일·출처 링크를 함께 기재. 확인 불가 시 "확인 불가"로 명시하고 추정치를 쓰지 않는다.

## 3. 뉴스 수집 (당일 KST 발행분만)
- 자사(CIS케미칼) + 경쟁사 8개사 + 고객사 2개사를 각각 WebSearch (필요 시 PlayMCP `NaverSearch-search_news` 병행).
- 업계 동향 키워드: "이차전지 재활용", "배터리 리사이클링", "리튬 회수", "흑연 음극재", "양극재 업계".
- metal.com: `site:news.metal.com 양극재`, `전구체`, `MHP`, `탄산리튬`, `수산화리튬`.
- **발행일이 `TODAY`가 아니거나 날짜 확인이 불가한 기사는 제외.** 해당 회사는 `<p>오늘 신규 뉴스 없음</p>`.

## 4. HTML 작성
- 직전 브리핑(`ls briefings | sort | tail -1`)의 `<head>`/CSS를 그대로 재사용해 스타일 일관성을 유지.
- 섹션 순서: Executive Summary(표) → 1. 자사 → 2. 경쟁사 동향 → 3. 고객사 동향 → 4. 건의 사항(즉시/단기/중기) → 5. 이차전지 업계 동향(5~10건) → 6. DART 공시 모니터링 → 7. 광물 가격.
- 스타일: `h2` 네이비(#1e3a5f) 배경·흰 글씨, `h3` 왼쪽 #2e6da4 보더, `h3` 바로 아래 `blockquote` 한 줄 요약, 뉴스는 `<a href="URL">내용</a>` 인라인, 인쇄 CSS 포함.

## 5. 커밋 & 푸시
```bash
git add briefings/${TODAY}.html
git commit -m "briefing: ${TODAY} 업계 동향 보고"
git push origin main
```
- `main`에 `briefings/*.html`이 push되면 `.github/workflows/send-briefing-email.yml`이 수신자에게 메일을 자동 발송한다.

## 6. 카카오톡 알림
- PlayMCP `KakaotalkChat-MemoChat`으로 나에게 보내기:
  `https://whoamiok-web.github.io/briefing/briefings/${TODAY}.html`

## 점검 체크리스트
- [ ] 모든 뉴스 링크의 발행일이 `TODAY`인가
- [ ] 10개사 DART 조회를 모두 수행했는가
- [ ] 광물 가격 3종 모두 기준일·출처가 있는가
- [ ] 섹션 순서·스타일이 CLAUDE.md와 일치하는가
