---
name: "essence"
description: "Use when the user asks for an Essence Card, 에센스 카드, '핵심만 압축', 'AI 에게 시킬 때 쓸 제약 문장', 'AI 협업 패턴 뽑아줘', or wants a Wiki Concept/Guide page (or a MOC's leaf pages) compressed into something they can hand to an AI while coding; also responds to /essence."
---

# essence

Use this skill when the user asks `/essence`, "에센스 카드 만들어줘", "이 개념 핵심만 AI 지시용으로 압축해줘", or wants a Wiki page turned into a card they reuse when instructing an AI.

## Command Template

Read `.codex/commands/essence.md` (Claude: `.claude/commands/essence.md`) and follow it as the runtime entrypoint. Fill the 재료 표 from the page first, write the card in the fixed H2 format, insert it before the retrospective callouts and `## Sources`, then run the Self-Check against the page.

## Non-Negotiables

- 카드는 페이지의 압축이지 보강이 아니다 — every line of §2 · §3 · §4 points to a line on the page. General knowledge, however correct, stays out; fewer lines plus a note beats invented ones.
- §1 copies the page's `한 문장 모델` 실정의 verbatim. A newly written sentence is tagged and reported.
- §5 wikilinks the exact `related:` filenames, MOCs excluded.
- §6 carries the `confidence` / `verificationStatus` tag line unless the page is `high` and `verified`.
- Touch only `updated` in frontmatter. Existing cards are skipped unless `--force`.
