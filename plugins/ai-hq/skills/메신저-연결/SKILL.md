---
name: 메신저-연결
description: 비서와 말할 메신저(텔레그램, 디스코드, 슬랙, Claude 앱)를 잇는 순서를 한 단계씩 안내한다. "텔레그램 연결해 줘", "디스코드로 비서랑 말하고 싶어", "슬랙 연결", "휴대폰에서 비서한테 말 걸고 싶어", "메신저 설정", "봇이 답을 안 해", "Claude 앱으로 쓰고 싶어" 같은 말에 쓴다.
---

# 메신저-연결

사용자가 고른 메신저를 한 단계씩 잇는다. 단계마다 무엇이 되고 무엇이 안 되는지 함께 말한다. 아래 사실은 공식 문서(code.claude.com/docs/en/channels, remote-control, mcp)와 공식 텔레그램, 디스코드 플러그인의 설명서에서 확인한 것만 적었다. 여기에 없는 것은 "공식 문서에 적혀 있지 않다"고 말하고 지어내지 않는다.

## 지킬 것

1. 한 번에 한 단계만 말한다. 사용자가 "됐어" 라고 하면 다음 단계로 간다.
2. 봇 토큰 같은 비밀값은 대화로 받지 않는다. 사용자가 그 메신저 플러그인의 설정 명령에 직접 넣는다.
3. 사용자가 비밀값을 그냥 대화에 붙여 넣으면 그 값을 되풀이해 적지 않고, 어디에도 저장하지 않는다. 그 토큰은 새로 바꾸라고 권한다(디스코드는 Reset Token, 텔레그램은 BotFather).
4. 비밀값이 든 파일(`.env`)은 열지도 고치지도 않는다. 메신저 플러그인의 설정 명령이 그 파일을 읽고 쓰므로, 설정 명령은 본부 폴더 밖에서 켠 Claude Code 에서 사용자가 친다(2절 3번).
5. 터미널 명령은 비서가 직접 돌릴 수 있는 것(예: `bun --version`)이면 직접 돌린다. 사용자가 쳐야 하는 것(Claude Code 다시 켜기, 설정 명령)은 그대로 붙여 넣을 한 줄로 준다.
6. 끝나면 본부 폴더의 `.harness/기록/YYYY-MM-DD.md` 에 한 줄을 남긴다.

## 0. 어느 메신저인지 정하기

본부 폴더의 `CLAUDE.md` 와 `rules/08` 에 처음 설정 때 고른 메신저가 적혀 있으면 그것으로 한다. 없거나 바꾸고 싶다고 하면 묻는다. 고르기 어려워하면 이렇게 알려 준다.

- 텔레그램, 디스코드: 휴대폰에서 말을 걸면 바로 답한다. 시험 공개 단계다. 컴퓨터에 Claude Code 세션이 열려 있어야 한다.
- 슬랙: 비서가 슬랙을 읽고 쓰기만 한다. 슬랙에서 비서에게 말을 걸 수는 없다.
- Claude 앱: 휴대폰 Claude 앱에서 내 컴퓨터의 세션을 이어서 쓴다. 허용 물음에도 휴대폰에서 답한다.

## 1. 텔레그램과 디스코드에 같이 필요한 것

1. 로그인: claude.ai 계정이나 Console 계정으로 로그인한 Claude Code. Team, Enterprise 요금제면 관리자가 'Channels' 를 켜 두어야 한다. 개인 요금제(Pro, Max, Ultra)는 따로 켤 것이 없다.
2. Bun: 비서가 `bun --version` 을 돌려 본다. 안 되면 사용자에게 Bun 을 설치하게 한다.
   - 맥: 터미널에 `curl -fsSL https://bun.sh/install | bash`
   - 윈도우: bun.sh 누리집의 윈도우 설치 안내를 따른다.
3. 공식 장터: 설치 중 `Marketplace "claude-plugins-official" not found` 가 나오면 `/plugin marketplace add anthropics/claude-plugins-official` 을 친 뒤 다시 설치한다.
4. 설치 범위를 물으면 'Install for you (user scope)' 를 고른다. 끝 글이 `Run /reload-plugins to apply.` 이면 `/reload-plugins` 를 친다.

## 2. 텔레그램

1. 텔레그램에서 BotFather 를 열고 `/newbot` 을 보낸다. 보이는 이름과, `bot` 으로 끝나는 아이디를 정한다. BotFather 가 주는 토큰을 복사해 둔다(숫자와 쌍점까지 통째로).
2. Claude Code 에서 `/plugin install telegram@claude-plugins-official`
3. 토큰 넣기는 본부 폴더 밖에서 사용자가 직접 한다. 사용자에게 이렇게 알린다: 터미널을 하나 더 열어 홈 폴더처럼 본부 폴더가 아닌 곳에서 `claude` 로 켜고, 거기서 `/telegram:configure <토큰>` 을 친 뒤 그 창을 닫는다.
   - 까닭: 이 명령은 토큰 파일(사용자 폴더의 `.claude/channels/telegram/.env`)을 읽고 쓴다. 본부 폴더에서는 권한 목록이 그 파일 읽기를 거부하고, 비서도 그 파일을 열지 않는다(rules/05-데이터와-비밀값.md 4번). 토큰 설정은 본부 폴더가 아니라 사용자 폴더에 저장되므로 다른 폴더에서 넣어도 된다.
   - 이 명령에 넣은 값은 그 세션 대화에도 남는다. 남들이 보는 화면에서는 하지 않는다.
   - 사용자가 원하면 공식 설명서대로 그 파일에 `TELEGRAM_BOT_TOKEN=토큰` 한 줄을 손으로 써도 된다. 비서가 대신 쓰거나 열어 보지 않는다.
   - 비서는 이 단계에서 토큰이 맞게 들어갔는지 파일을 열어 확인하지 않는다. 5번에서 봇이 답하는지로 확인한다.
4. Claude Code 를 끄고 본부 폴더에서 다시 켠다: `claude --channels plugin:telegram@claude-plugins-official`
5. 텔레그램에서 내 봇에게 아무 말이나 보낸다. 봇이 짝 맞추기 코드를 답한다. 답이 없으면 4번의 명령으로 켰는지 본다.
6. Claude Code 에서 `/telegram:access pair <코드>`
7. 잠그기: `/telegram:access policy allowlist`. 이제 나만 말을 걸 수 있다.

되는 것: 말 걸고 답 받기, 사진 보내기(받는 즉시 컴퓨터에 저장).
안 되는 것: 지난 대화 다시 읽기(텔레그램 봇은 지난 메시지를 볼 수 없다), 세션이 닫혀 있을 때 말 받기. 터미널에는 비서의 답 글이 보이지 않고 '보냈다'는 표시만 나온다.

## 3. 디스코드

1. Discord Developer Portal 에서 New Application 을 눌러 앱을 만든다. Bot 메뉴에서 봇 이름을 정한다.
2. 같은 Bot 화면의 Privileged Gateway Intents 에서 Message Content Intent 를 켠다. 안 켜면 봇이 빈 메시지만 받는다.
3. Bot 화면의 Token 에서 Reset Token 을 누르고 토큰을 복사한다. 한 번만 보인다.
4. 봇을 내 서버에 초대한다. OAuth2 의 URL Generator 에서 `bot` 을 고르고, 권한은 View Channels, Send Messages, Send Messages in Threads, Read Message History, Attach Files, Add Reactions 를 켠다. 만들어진 주소를 열어 내가 있는 서버에 넣는다. 디스코드는 같은 서버에 있는 봇에게만 1:1 메시지를 보낼 수 있다.
5. Claude Code 에서 `/plugin install discord@claude-plugins-official`
6. 본부 폴더 밖에서 켠 Claude Code 에서 사용자가 직접 `/discord:configure <토큰>` 을 친다. 토큰 파일은 `.claude/channels/discord/.env`, 손으로 쓸 때의 줄은 `DISCORD_BOT_TOKEN=토큰` 이다. 2절 3번의 주의가 모두 같이 걸린다.
7. 본부 폴더에서 다시 켠다: `claude --channels plugin:discord@claude-plugins-official`
8. 디스코드에서 내 봇에게 1:1 메시지를 보내 코드를 받고 `/discord:access pair <코드>`
9. 잠그기: `/discord:access policy allowlist`

되는 것: 말 걸고 답 받기, 그 방의 최근 메시지 다시 읽기(한 번에 100개까지), 첨부 파일을 필요할 때 내려받기.
안 되는 것: 세션이 닫혀 있을 때 말 받기. 터미널에는 답 글이 보이지 않는다.

## 4. 텔레그램과 디스코드를 이은 뒤 시험

1. 메신저에서 "안녕" 을 보내 답이 오는지 본다.
2. 허용 물음이 메신저로 오는지 본다. 공식 문서는 그 기능을 갖춘 채널만 물음을 메신저로 넘긴다고 밝힌다.
   - 메신저로 "바탕화면에 시험.txt 파일을 하나 만들어 줘" 라고 보낸다. 본부 폴더 밖이라 허용 물음이 뜬다.
   - 물음이 메신저로 오면 거기서 답한다. 안 오면 세션은 컴퓨터 앞에서 답할 때까지 멈춘다.
   - 결과를 `.harness/memory/MEMORY.md` 에 한 줄로 적는다(예: "텔레그램으로 허용 물음이 온다").
   - 시험 파일은 사용자가 직접 지운다.
3. 앞으로 켤 때마다 본부 폴더에서 같은 `claude --channels ...` 명령으로 켠다고 알려 준다. 명령을 바꾸지 않으면 메신저 말이 들어오지 않는다.
4. 컴퓨터가 잠들거나 꺼진 동안 메신저 말이 어떻게 되는지는 공식 문서에 적혀 있지 않다. 그동안에는 답을 기대하지 말라고 말한다.

## 5. 슬랙

1. 먼저 알린다: 슬랙에는 채널 기능이 없어서, 슬랙에서 말을 걸어 내 컴퓨터의 비서를 움직일 수 없다. 비서가 슬랙을 읽고, 승인을 받아 쓰는 것만 된다. 비서와 말하는 곳은 터미널이나 Claude 앱(6절)으로 함께 둔다.
2. 사용자가 claude.ai/customize/connectors 에서 슬랙을 연결한다. Team, Enterprise 요금제는 관리자만 커넥터를 더할 수 있다.
3. Claude Code 를 다시 켜고 `/mcp` 를 쳐서 claude.ai 쪽 목록에 슬랙이 보이는지 본다. 커넥터는 claude.ai 계정으로 로그인했을 때만 보인다. 안 보이면 `/status` 로 로그인 방식을 본다.
4. 슬랙의 보내기, 올리기, 지우기 도구를 권한 목록의 '묻기'에 넣는다. 처음 설정 뒤에 연결한 커넥터는 묻기 목록에 저절로 들어가지 않는다. 스킬 규칙-손질의 순서로 한다.
5. 시험: "슬랙에서 오늘 온 메시지 읽어 줘" 로 읽기를 확인한다.
6. 슬랙의 '@Claude' 는 Anthropic 서버에서 새 세션을 열고 GitHub 저장소로 일하는 다른 기능이다. 내 본부 폴더를 읽지 못하므로 여기서 잇지 않는다.

## 6. Claude 앱(원격 제어)

1. 조건: Pro, Max, Ultra, Team, Enterprise 요금제의 claude.ai 로그인. API 키 로그인은 안 된다. Team, Enterprise 는 관리자가 Remote Control 을 켜 두어야 한다.
2. 휴대폰에 Claude 앱을 설치하고 같은 계정으로 로그인한다. 앱이 없으면 Claude Code 에서 `/mobile` 을 쳐 QR 코드로 받는다.
3. 본부 폴더에서 `claude --remote-control` 로 켠다. 이미 켜 둔 세션이면 `/remote-control` 을 친다.
4. 앱이나 claude.ai/code 의 세션 목록에서 그 세션을 고른다.
5. 늘 켜지게 하려면 `/config` 에서 'Enable Remote Control for all sessions' 를 켠다.
6. 휴대폰 알림을 받으려면 `/config` 에서 'Push when actions required'(허용 물음과 질문)와 'Push when Claude decides' 를 켠다.

되는 것: 휴대폰에서 말 걸기, 허용 물음에 답하기, 사진과 파일 보내기, 알림 받기.
조건: 컴퓨터가 켜져 있고 Claude Code 가 돌고 있어야 한다. 터미널이나 앱을 닫으면 끊긴다. 컴퓨터가 잠들었다 깨면 저절로 다시 잇는다. 연결된 동안 대화 내용은 Anthropic 서버에 저장되고, 파일과 명령은 내 컴퓨터에 남는다.

## 7. 끝내기

1. 기록 한 줄: `- HH:MM · 비서 이름 · 메신저 연결(어느 메신저) · 됨 또는 막힌 단계 · 스킬 메신저-연결`
2. 답은 결과부터: 이어졌는지, 사용자가 할 일(예: 앞으로 켜는 명령), 되는 것과 안 되는 것 한 줄씩.
3. 예약을 아직 안 만들었으면 스킬 예약-만들기를 다음 할 일로 알린다.
