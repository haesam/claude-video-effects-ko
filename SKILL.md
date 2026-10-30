---
name: effects
description: Add any of 45 video effects to a talking-head video (orbit, spotlight, glass widgets, timeline, checklist, headline wall, comment box, phone fan, liquid glass graph, match-cut, iPhone toggles, LED sign, over-the-shoulder zoom, iMessage pop-ups, place-in-grid and more). Effects track the speaker's face and can sit behind their head. Use when the user types /effects, names one of the effects, or asks to add an effect, zoom, grade, sticker or animation to their video.
---

# Effects 한글판: 영상 효과 45종 (원작 @vibha.in.progress, 한글판 인디힌터 @31brand)

**항상 한국어로 답하세요.** 사용자는 한국어 사용자예요. 짧고 친근한 존댓말로 답하고, 명령어·`fx` 이름·옵션 이름은 영어 그대로 두세요.

**한글 글자:** 동봉 폰트는 한글을 지원하지 않아요. 화면에 넣을 `text`가 한글이면 시스템에 NanumGothic 또는 Noto Sans KR(Noto Sans CJK)이 있는지 확인하고, 글자가 네모(□)로 깨지면 사용자에게 알리고 영어로 바꾸거나 한글 폰트 설치를 도와주세요(Mac: `brew install --cask font-noto-sans-kr`, Linux: `fonts-noto-cjk` / `fonts-nanum`).

사용자는 대부분 비개발자예요. 답은 짧고 친근하게, 작업은 대신 해주고, 사용자에게 명령어를 직접 실행하라고 하지 마세요.

All paths below are relative to this skill's folder (the folder this SKILL.md is in). Use its absolute path in commands.

## 1. First run: set up (once)
Run `bash scripts/setup.sh`. It prints `READY` when done (the first time takes 1 to 3 minutes).
- `NEED_FFMPEG`: install it for them. On a Mac: `brew install ffmpeg` (if `brew` is missing, tell them to install Homebrew from brew.sh first). On Windows: `winget install ffmpeg`.
- `NEED_PYTHON`: install Python 3.12 (Mac: `brew install python@3.12`, Windows: `winget install Python.Python.3.12`), then run setup again.
- On Windows, the Python is at `.venv\Scripts\python.exe` instead of `.venv/bin/python`.

## 2. Get the video and the effect
- Video: the user drags the file into the chat, or gives a path. A vertical phone video works best.
- Effect: from `/effects <name>`, or from what they describe. Match their words to the table below. Unsure? Show them the list.

## 3. Find the timing
- If they say "on the word X" or "when I say ...", run `.venv/bin/python scripts/words.py <video>` and use those times.
- If they give no timing, apply the effect for the whole clip, or ask which part (one short question).
- Short windows look best: 1.5 to 4 seconds per effect.

## 4. Draft, check, render
1. Write the plan next to their video as `<video-name>_effects.json`:
   ```json
   {"effects": [
     {"fx": "orbit", "start": 3.2, "end": 6.0},
     {"fx": "backdrop-word", "start": 14.0, "end": 16.3, "text": "CLAUDE"}
   ]}
   ```
2. Check frames first (fast): `.venv/bin/python scripts/fx.py --plan <plan.json> <video> <folder>/check.png --still 4.5,15.2`, using the middle of each effect. Look at the PNGs yourself. Fix the text, timing or options if something looks off (covers the face, cut off by the edge, unreadable).
3. Render: `.venv/bin/python scripts/fx.py --plan <plan.json> <video> <video-name>_effects.mp4`. The output keeps their original audio. It renders at 1080p by default; add `--width 2160` for 4K (slower).
4. Tell them where the file is. Remind them in one line to watch it before posting: it drafts, they approve.

A single effect needs no plan: `.venv/bin/python scripts/fx.py orbit <video> <out.mp4> --start 3 --end 6 --text "..." --opt key=value`.

## The 45 effects
| # | `fx` 이름 | 효과 | 옵션 (모두 선택) |
|---|---|---|---|
| 1 | `noise` | 얼굴 뒤로 릴스가 0.25초마다 하나씩 쌓여요 | `images` (folder of screenshots or clips), `size` |
| 2 | `lineup` | 번호 붙은 타일이 머리 위로 부채꼴처럼 펼쳐져요 | `text` (tile names joined with `\|`) |
| 3 | `locked-on` | 모서리 브래킷이 얼굴에 착 붙고 카메라가 따라와요 | `zoom` (default 1.35), `text` (tag) |
| 4 | `orbit` | 내 릴스들이 머리 주위를 돌아요(아래는 앞, 위는 뒤) | `images`, `count`, `speed`, `size` |
| 5 | `lean-in` | 한 단어에서 빠르게 확 줌인돼요 | `zoom` (default 1.45, max 2), `speed` |
| 6 | `backdrop-word` | 거대한 단어가 내 뒤로 떨어져요 | `text`, `color` (hex), `font` (`bold` or `serif`) |
| 7 | `spotlight` | 조명이 꺼지고 나만 밝게 남아요 | `text` (glowing word behind you), `color`, `push` |
| 8 | `the-bill` | 지시 사항이 영수증처럼 머리 위 벽에 출력돼요 | `text` (lines joined with `\|`) |
| 9 | `moody` | 무드 있는 색보정 | `grade` (`moody`, `bw`, `warm`, `cool`, `pop`) |
| 10 | `swipe` | 비포/애프터 슬라이더가 화면을 가로질러요 | `grade` (the "after" look) |
| 11 | `todo` | 말하는 동안 머리 옆 체크리스트가 하나씩 체크돼요 | `text` (items with `\|`), `ticks` (video seconds with `\|`), `title` |
| 12 | `comment-box` | 인스타 댓글창에 단어가 입력되고 게시돼요 | `text` (default EFFECTS) |
| 13 | `tap` | 팔로우 버튼이 떠서 팔로잉으로 바뀌어요 | `at` (video second of the flip), `x`, `y` (0 to 1), `color` |
| 14 | `pause-point` | 화면을 멈추고 손글씨 화살표로 나를 가리켜요 | `text` (label), `color`, `label_x` |
| 15 | `uninstall` | 글리치 '삭제 중' 바가 화면을 깨뜨려요 | `text` |
| 16 | `reel-frame` | 영상이 인스타 릴스 카드 안으로 작아져요 | `text` (handle) |
| 17 | `shortcut-rain` | 키보드 키가 내 뒤로 비처럼 쏟아져요 | `text` (keys with `\|`), `count` |
| 18 | `reject-stamp` | 큰 빨간 도장이 화면에 쾅 찍혀요 | `text`, `color`, `angle`, `y` |
| 19 | `countdown-flip` | 머리 위에 3, 2, 1 플립 시계 | `from` |
| 20 | `rabbit-hole` | 화면이 회전하며 안쪽으로 빨려 들어가요 | `ratio`, `speed`, `spin` |
| 21 | `cover-star` | 내가 잡지 표지가 되고 제목이 머리 뒤에 깔려요 | `text` (title), `lines` (with `\|`), `issue`, `color` |
| 22 | `scribble` | 마커 낙서가 내 주변에 저절로 그려져요 | `text` (label), `color` |
| 23 | `glass-widgets` | 반투명 유리 위젯과 미니 아이템이 머리 주위에 떠다니고 'Edited by Claude' 배지가 붙어요 | `text` (big number), `name`, `handle`, `look` (`simple` for the 3-widget version) |
| 24 | `red-collage` | 흑백의 나, 뒤에 거대한 빨간 단어, 문장 3줄이 쾅쾅 등장 | `text` (red word), `lines` (with `\|`), `tag` |
| 25 | `split-headline` | 헤드라인 카드가 내려오며 나를 화면 아래 절반으로 밀어요 | `text` (lines with `\|`), `hl` (highlighted word), `pill` |
| 26 | `pastel-word` | 라벤더 배경, 뒤에 거대한 단어, 프로필 카드가 팔로우돼요 | `text`, `name`, `handle` |
| 27 | `check-pills` | 모눈종이 배경, 뒤에 릴스 보드, 커서가 파란 알약 버튼을 클릭해요 | `text` (pills with `\|`), `images` |
| 28 | `timeline` | 진한 레드 필름 톤에 편집 타임라인, REC 타임코드, 빛나는 재생 헤드 | `text` (title), `sub`, `tc` |
| 29 | `headline-box` | 줌인과 함께 두 줄 헤드라인(둘째 줄은 흰 박스) | `text` (lines with `\|`), `zoom` |
| 30 | `profile-wall` | 뒤 벽이 내 릴스 그리드로 채워지고 숫자가 올라가요 | `images`, `count`, `label` |
| 31 | `tier-list` | 티어 리스트가 내려오고 Claude가 S티어로 날아가요 | `title`, `item`, `others` |
| 32 | `broll-split` | 위에는 B롤과 가격표 스티커, 아래에는 나 | `images`, `text` (tag) |
| 33 | `phone-fan` | 헤드라인 아래로 내 릴스가 손에 쥔 폰들처럼 펼쳐져요 | `images`, `text` (lines with `\|`) |
| 34 | `dot-reveal` | 빛나는 점들이 모여 화면을 만든 뒤 원래 화면으로 전환돼요 | `color`, `cell` |
| 35 | `headline-wall` | 내 헤드라인들이 머리 뒤에 쌓이고 앞에 합계가 떠요 | `text` (`headline::meta\|...`), `total`, `label` |
| 36 | `checklist` | 턱 아래 Claude 작업 목록: 단계마다 돌다가 초록 체크 | `text` (`step::meta\|...`), `title` |
| 37 | `liquid-glass-graph` | 턱 아래 리퀴드 글래스 카드: 그래프가 오르고 숫자가 올라가요 | `text` (label), `from`, `to` (e.g. `197K`, `59%`), `color` |
| 38 | `carousel-spin` | 내 릴스가 3D 회전목마처럼 돌다가 멈춰요 | `images`, `count`, `size`, `turns` |
| 39 | `match-cut` | 한 단어에서 뉴스 페이지가 컷 전환, 머리 뒤 빨간 박스에 고정 | `text` (the word, default Claude), `color` |
| 40 | `apple-toggles` | 턱 아래 아이폰 설정 카드 스위치가 한 줄씩 켜져요 | `text` (rows with `\|`), `ticks` (seconds with `\|`), `color` |
| 41 | `led-sign` | 조명이 꺼지고 빛나는 LED 간판 글자가 내 뒤에 입력돼요 | `text` (lines with `\|`), `color`, `push` |
| 42 | `slow-push` | 배경이 흐려지면서 얼굴로 천천히 시네마틱 줌인 | `zoom` (default 1.6), `blur` |
| 43 | `over-the-shoulder-zoom` | 한쪽 위 모서리로 줌인하며 머리 옆에 그래프가 뜨고, 다시 빠졌다가 반대쪽에 두 번째 막대그래프(머리는 항상 앞) | `text` (two labels with `\|`), `to` (two numbers with `\|`, e.g. `197K\|672K`), `zoom` (default 2.0) |
| 44 | `messages` | 아이메시지 알림이 부드러운 빛과 함께 하나씩 쌓여요 | `text` (messages with `\|`), `title` (default Messages), `y` |
| 45 | `place-in-grid` | 3x3 프로필 그리드 가운데 칸으로 작아지고 주변에 릴스 8개가 조회수와 함께 떠요 | `images` (folder of your reels or covers), `views` (9 counts with `\|`, the 9th is yours) |

**Named looks** (same effect, a fancier look; they don't add to the count):
- `glitter-spotlight` = `spotlight` with `look=sparkle`: the room goes dark, golden rays burst from behind your head, a big gold word with a shine sweep, twinkling sparkles and a glow around you. `text` (the word), `color`, `push`.
- `cover-star` with `look=editorial`: a soft warm film look, a classic serif masthead behind your head, an issue line and elegant cover lines. `text` (masthead), `issue`, `lines` (3 lines with `\|`), `right` (lines with `\|`), `color`, `accent`, `lines_y`, `right_y`.
- `liquid-glass-graph` with `look=bento`: the climbing graph moves to a smoked glass screen behind your head, with glass bento tiles in front of you (a donut, a KPI, bars). `text`, `from`, `to`, `donut` (`29%::label`), `kpi` (`672K::label`), `bars` (`197\|140\|124::label`).
- `apple-toggles` now sits above you, tucked behind your head, with a gentle zoom (`pos=below` for the old under-the-chin card; `overlap`, `zoom`).

Aliases work too: `bill`, `to-do`, `follow`, `stamp`, `countdown`, `cover`, `rain`, `pause`.

## Rules
- Never cover the face. Everything either goes behind the person or avoids the face by itself; if a check frame still shows text over the face, move it (`x`, `y`) or shorten the text.
- Effects run in the order listed. Put camera effects (`locked-on`, `lean-in`, `spotlight`) first when they overlap others.
- Zoom never goes past 2x, so the video stays sharp.
- This skill adds visuals only. Sound effects and captions are up to the user (CapCut works well).
- These are starter versions. Be honest about that if the user compares them to a reel they saw.
