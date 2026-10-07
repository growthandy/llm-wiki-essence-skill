# llm-wiki-essence-skill

Obsidian LLM Wiki 의 개념 페이지를 **AI 에게 지시할 때 꺼내 쓰는 카드**로 압축하는 Claude Code / Codex 스킬.

`/essence {페이지명}` 한 번이면 페이지 끝에 `## Essence Card` 섹션이 붙는다. 카드는 여섯 칸이다.

| § | 내용 | 어디서 오는가 |
|---|---|---|
| 1 | 한 문장 정신 모델 | 페이지의 `한 문장 모델` 콜아웃을 **글자 그대로** |
| 2 | 이 개념으로 AI 에게 시킬 수 있는 것 · 산출물에서 검사할 수 있는 것 | 본문이 "할 수 있다 / 보장한다" 고 말하는 대목 |
| 3 | AI 협업 패턴 — 작성 지시 · 오류 수정 지시 프롬프트 2종 | `실전 함정` · `비유의 경계` |
| 4 | 문제 해결 레버 (증상 → 먼저 확인할 것) | 페이지에 적힌 오류 코드·실패 사례 |
| 5 | 관계 | `related:` 를 정확한 파일명으로 wikilink |
| 6 | AI 제약 — 반복해서 쓸 한 문장 | §1 + `confidence` / `verificationStatus` 꼬리표 |

실제 출력은 [`examples/async-defer-essence-card.md`](examples/async-defer-essence-card.md) 에 있다.

## 왜 만들었나

Karpathy 의 LLM Wiki 패턴으로 수백 페이지를 컴파일하고 나면, 정작 코딩하면서 AI 에게 "이 개념으로 이렇게 해줘" 라고 말할 때 꺼낼 것이 없다. 페이지는 **이해하기 위한** 문서지 **지시하기 위한** 문서가 아니기 때문이다. Essence Card 는 그 간극을 메운다. 요약이 아니라 **AI 협업 문해력** 을 남기는 것이 목적이다.

## 핵심 원칙 — 카드는 페이지의 압축이지 보강이 아니다

카드의 모든 줄은 페이지 본문의 어느 줄에서 왔는지 가리킬 수 있어야 한다. 페이지에 없는 능력·증상·오류 메시지는 일반 지식으로 아무리 옳아도 쓰지 않는다. 출처 없는 주장이 Wiki 에 들어가는 통로가 되기 때문이다.

이 규칙은 실측에서 나왔다. 자유형 프롬프트("핵심만 압축해줘")로 같은 페이지를 돌리면 LLM 은 페이지에 없는 `<head>` 배치, "가장 빨리 실행", `"X is not defined"` 같은 오류 문구를 지어 넣었다. 그래서 스킬은 **재료 표** (페이지의 어디 → 카드의 어디) 와 **Self-Check** (출처를 못 대는 줄은 지운다) 로 동작한다. 페이지가 부족하면 카드도 짧고, 그 사실을 보고한다.

## 설치

볼트(Obsidian vault) 루트에서:

```bash
git clone https://github.com/growthandy/llm-wiki-essence-skill /tmp/essence
cp -r /tmp/essence/.claude /tmp/essence/.codex /tmp/essence/.agents .
```

세 파일이 들어간다. 셋은 같은 내용의 미러다.

| 하네스 | 파일 |
|---|---|
| Claude Code | `.claude/commands/essence.md` |
| Codex | `.codex/commands/essence.md` |
| Codex skill (agentskills.io) | `.agents/skills/essence/SKILL.md` |

한 하네스만 쓰면 그 폴더만 복사해도 된다.

## 사용

```text
/essence {페이지명}                 # 한 페이지 또는 여러 페이지
/essence MOC-{이름}                 # MOC 의 leaf 페이지(개념·가이드) 전체
/essence {페이지명} --force         # 이미 있는 카드를 다시 쓴다
```

기존 카드가 있으면 건너뛰고 보고만 한다. frontmatter 는 `updated` 만 바뀐다.

실행이 끝나면 페이지마다 한 줄짜리 보고 표가 나온다. **§1 출처가 "신규 작성"** 이거나 **§5 생략** 으로 표시된 페이지는 손으로 한 번 보는 것이 좋다. 전자는 페이지에 한 문장 모델 콜아웃이 없어 카드가 지은 문장이고, 후자는 `related:` 가 비어 있다는 뜻이다.

## 페이지에 무엇이 있어야 하나

스킬은 [cmds-llm-wiki](https://github.com/johnfkoo951/cmds-llm-wiki) 계열 볼트의 페이지 관례를 재료로 읽는다. 전부 **있으면 쓰고 없으면 건너뛰는** 선택 사항이다.

| 페이지 요소 | 있으면 | 없으면 |
|---|---|---|
| `> [!tip] 한 문장 모델` (실정의 · 비유 · 비유의 경계) | §1 복사, §3·§6 제약 | 본문 첫 정의에서 한 문장을 쓰고 `(카드에서 작성)` 표시 |
| `> [!warning] 실전 함정` | §3 제약, §4 레버 | 본문의 "…하면 깨진다" 류 문장으로 대체 |
| `related:` (frontmatter) | §5 wikilink | §5 생략, 보고에 기록 |
| `confidence:` · `verificationStatus:` | high + verified 가 아니면 §6 에 꼬리표 | 꼬리표 생략 |
| `## 육성 인용` (강사 발화 인용) | §6 어조 | — |
| 회고성 콜아웃 (`Baseline` · `Bias Check` · `Update`) · `## Sources` | 카드를 그 **앞** 에 삽입 | 파일 끝에 삽입 |

평범한 마크다운 노트에도 동작하지만, 그럴수록 §1 은 "신규 작성", §4 는 3 건 미만으로 짧아진다. 그것이 정상이다.

## 병렬 실행 시 주의

MOC 단위로 여러 에이전트에 나눠 돌릴 때 카드를 임시 파일에 써 두었다가 옮기지 않는다. 공유 scratch 가 서로 덮여 다른 페이지의 카드가 들어간 사례가 있어, 스킬은 페이지에 직접 삽입하도록 되어 있다.

## English

A Claude Code / Codex skill that compresses an Obsidian LLM-Wiki concept page into an in-page **Essence Card**: a verbatim one-sentence mental model, what the concept lets you ask an AI to do, two reusable prompt templates, a symptom-to-lever debugging map, wikilinked relations, and one constraint sentence to repeat to an AI.

The governing rule is **compression, not augmentation**: every line of the card must trace to a line on the page. The skill reads the page's existing callouts (`한 문장 모델`, `실전 함정`, `related:`, `confidence`) as its raw material and deletes anything it cannot source. Pages without those callouts still work; the card just gets shorter and the report says so.

Install by copying `.claude/`, `.codex/` and `.agents/` into your vault root. Run `/essence {page}` or `/essence MOC-{name}`. See `examples/` for real output. The card body is Korean because the author's vault is; the structure and the sourcing rules are language-independent, and translating the six section headings in the command file is all it takes to localize.

## License

MIT
