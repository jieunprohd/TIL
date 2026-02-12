# 1. 에이전틱 개발자의 사고방식
우리는 더이상 혼자 코드를 작성하지 않고 AI라는 파트너와 일하는 **에이전틱 개발자**이다.

## **분해하고 정복하라**
AI는 명확하고 구체적인 지시를 받았을 때 효율이 극대화되기 때문에, AI 협업의 핵심은 큰 문제를 잘게 쪼개는 능력에 있다.

> 로그인 페이지 만들어줘.
> 
> A -> B (X)
> 
> 1. 사용자 모델을 위한 DB 스키마 설계해줘. 필드는 ...
> 2. 해당 스키마를 기반으로 ORM 마이그레이션 파일을 만들어줘.
> 3. React와 TailwindCSS를 사용하여 로그인 폼 UI 컴포넌트르 만들어줘. 이메일과 비밀번호 입력 필드 ....
> 
> A -> A1 -> A2 -> A3 ... -> B (V)

작업을 분해하여 하나씩 완성하고 검증하고 문제가 발생할 시 해당 단계만 수정하면 되므로 디버깅이 훨씬 쉬워진다.

## **계획 모드 vs 욜로 모드: 언제 생각하고 언제 실행할 것인가**
Claude Code는 두 가지 작업 방식을 제공하고 상황에 따라 적절한 모드를 선택해야 생산성을 극대화할 수 있다.

### 계획 모드 (Plan Mode) -> Shift + Tab
계획 모드에서는 승인하기 전까지 아무것도 편집하지 않는다.

**동작**
- 코드베이스를 읽고 분석
- 아키텍처 파악
- 의존성 탐색
- 구현 계획을 단계별로 작성

**사용 예시**
- 처음 해보는 복잡한 작업
- 여러 파일에 걸친 대규모 리팩토링
- 아키텍처 변경이 필요한 작업
- 실수 시 복구 비용이 큰 작업

### 욜로 모드 (YOLO Mode) -> claude --dangerously-skip-permissions
욜로 모드에서는 승인과 관계없이 작업한다.

**사용 예시**
- 간단하고 명확한 작업
- 실험적인 프로토타입 작업
- 컨테이너 안에서 격리된 환경에서 작업할 때
- 반복적이고 시간이 많이 걸리는 작업


## **컨텍스트: AI의 기억력을 지배하는 기술**
컨텍스트는 AI의 작업 기억과 같지만 이 공간이 시스템 프롬프트, 코드 파일, 대화 기록 등으로 가득 차면 초기의 중요한 지식을 잊는 컨텍스트 드리프트 (Context Drift) 현상을 겪을 수 있다.

### 컨텍스트 관리 전략
1. 단일 목적 대화 - 하나의 대화에서는 하나의 명확한 목표에만 집중
2. 선제적 압축 - 대화가 길어질 경우, HANDOFF.md 파일을 작성하도록 지시하고 /clear로 새로 시작
3. /context로 X-Ray 비전 - /context 명령어에서 제공하는 정보들을 바타으로 불필요한 MCP를 비활성화하거나 사용하지 않는 스킬 언로드
4. 자동 압축 비활성화 - 압축 시점을 직접 제어해 중요한 정보가 손실되지 않도록 하고, HANDOFF.md 방식이 더 명확하고 검증 가능

## 올바른 추상화 수준 선택
멀리서 전체를 조망하는 Vibe Coding과 깊이 잠수하여 세부 사항을 파악하는 Deep Dive 중 적절한 추상화 수준을 선택해야 한다.

### Vibe Coding (높은 추상화)
전체 구조와 흐름만 파악하고 개별 코드 라인은 신경쓰지 않는다.

**사용 예시**
- 일회성 프로젝트나 프로토타입
- 중요하지 않은 코드베이스 부분
- 빠른 실험이 필요할 때

### Deep Dive (낮은 추상화)
파일 구조, 함수, 개별 코드라인, 의존성까지 꼼꼼히 검토한다.

**사용 예시**
- 프로덕션 코드
- 보안이 중요한 부분
- 복잡한 버그 디버깅ㅇ
- 성능 최적화

> 높은 추상화 - 사용자 프로필 페이지를 만들어줘 -> 전체 구조 파악
> 중간 추상화 - 프로필 편집 폼의 유효성 검사 로직을 보여줘 -> 특정 기능 검토
> 낮은 추상화 - 이 정규식이 왜 이메일 유효성 검사에 실패하는지 설명해줘 -> 세부 디버깅

## 미지의 영역에서 더 용감하게
AI와의 협업은 단순히 생산성 향상을 넘어 개발자의 자신감과 학습 능력까지 확장시키고 있다.

# 2. 기초부터 탄탄하게 - 환경 설정과 필수 명령어
AI와 최상의 시너지를 낼 수 있는 초기 설정을 알려준다.

## 커스텀 상태 라인으로 모든 것을 한눈에
터미널 하단에 실시간으로 중요한 정보를 표출하는 기능

**설치 방법**
~~~shell
# 1. 저장소 클론
git clone https://github.com/ykdojo/claude-code-tips.git

# 2. 스크립트 심볼릭 링크 생성
ln -s $(pwd)/claude-code-tips/scripts/status-line.sh ~/.claude/scripts/status-line.sh

# 3. Claude Code 재시작
~~~

**~/.claude/settings.json에서 커스터마이징이 가능하다**
~~~json

{
    "statusLine": {
        "format": "{{dir}} {{git}} | {{tokens}} | {{model}}",
        "updateInterval": 1000
    }
}
~~~

## 필수 슬래시 명령어 마스터
**생존 필수 명령어**

| 명령어 | 설명 | 사용 시점 |
|-|-|-|
| /usage | 현재 토큰 사용량과 리셋 시간을 시각적으로 표시 | 매 세션 시작 시, 대화가 길어질 때 |
| /clear | 대화 내용을 지우고 새로운 컨텍스트 시작 | 컨텍스트 오염 시, 새 작업 시작 시 |
| /stats | Github 스타일 활동 그래프, 즐겨찾는 모델, 연속 사용일 등 분석 | 주간 회고 시, 사용 패턴 분석 시 |
| /context | 컨텍스트 사용 현황 X-Ray | 성능 저하 느낄 때, 최적화 필요 시 |

**생산성 향상 명령어**

| 명령어 | 설명 | 사용 시점 |
|-|-|-|
| /chrome | 크롬 브라우저 통합 시작 | 웹 스크래핑, UI 테스트, 디버깅|
| /mcp | MCP 서버 목록 및 활성화/비활성화 | MCP 관리, 컨텍스트 최적화 |
| /permissions | 승인된 명령어 목록 및 관리 | 보안 감사, 위험한 명령어 제거 |
| /export | 내화 내역 마크타운 내보내기 | 문서화, 팀 공유, 학습 자료 |

**! Prefix**
! 접두사를 붙이면 Claude의 처리 없이 바로 셀 명령을 실행하고 결과를 컨텍스트에 주입한다.

## CLAUDE.md: AI를 위한 프로젝트 설명서
CLAUDE.md 파일은 AI가 프로젝트의 기술 스택, 코딩 스타일, 주요 라이브러리, 해서는 안 될 일들을 파악하는 프로젝트 설명서이자 행동 지침이다.

/init 명령어를 통해 Claude가 코드베이스를 분석해 초안을 자동으로 생성한다.
CLAUDE.md를 직접 편집하지 않고 자연어 명렁어를 통해 업데이트를 지시할 수 있다.

**Tip**
- CLAUDE.md는 최대한 간결하고 명확하게 유지
- 처음에는 CLAUDE.md 없이 시작하고, 이후 같은 말을 반복하게 되면 추가

## 터미널 별칭으로 빠른 접근
~/.zshrc 설정을 통해 타이핑을 최소화 할 수 있다.

~~~shell
# Claude Code 기본 별칭
alias c='claude'
alias cc='claude --continue' # 마지막 대화 이어가기
alias cr='claude --resume' # 대화 목록에서 선택
alias ch='claude --chrome' # 크롬 통합 모드

# Git 관련 별칭 (Claude와 함께 사용)
alias gb='git branch'
alias gco='git checkout'
alias gst='git status'
alias gd='git diff'

# 빠른 종료
alias q='exit'
~~~

## 세션 관리: 대화를 잃지 않는 법
Claude Code는 모든 대화를 자동으로 저장하지만, 효과적응로 관리하지 않으면 이후 찾기 어렵다.

**Tip**
- claude --continue를 통해 마지막으로 작업하던 대화를 이어나갈 수 있다.
- /rename <이름> 명령어를 통해 세션에 의미있는 이름을 붙일 수 있고, claude --resume <이름>을 통해 세션을 재개할 수 있다.
- claude --teleport <세션 ID>를 통해 웹 브라우저에서 시작한 대화를 터미널로 가져올 수 있다.
- /export 명령어를 통해 대화 내역을 마크다운 파일로 내보낼 수 있다.

# 3. 생산성을 극대화하는 핵심 기술
타이핑 시간을 줄이고, 정보 전달 속도를 높이며, 반복 작업을 자동화하는 방법에 대해 알려준다.

## 음성으로 코딩하기
타이핑보다 음성이 3.75배 빠르다.

**유용한 상황**
- 복잡한 요구사항을 설명할 때
- 여러 단계의 작업을 한 번에 지시할 때
- 손이 피곤하거나 손묵 터널 증후군이 있을 때
- 산책하거나 이동 중일 때

## 터미널 출력 추출의 기술
Claude의 출력을 효율적으로 추출하고 활용하는 방법이다.

### pbcopy/pbpaste
~~~shell
# Claude의 마지막 출력을 클립보드로 복사
> "마지막 출력을 pbcopy로 복사해줘"

# 명령어 실행
> !claude -r | tail -n 50 | pbcopy
~~~

### VS Code 바로 열기
~~~shell
# Claude가 생성한 코드를 임시 파일로 저장하고 VS Code에서 열기
> "이 코드를 temp.ts 파일로 저장하고 VS Code로 열어줘"

# Claude 실행:
# cat > temp.ts << 'EOF'
# [코드 내용]
# EOF
# code temp.ts
~~~

### Github Desktop으로 변경 사항 검토
~~~shell
# Claude가 여러 파일을 수정한 후
> "GitHub Desktop을 열어줘"

# 또는
> !open -a "GitHub Desktop"
~~~

## Cmd + A: 전체 선택의 힘
접근히 막힌 웹 페이지나 긴 문서의 내용을 빠르게 컨텍스트로 넣고 싶으 경우 전체 선택하고 복사해 붙여넣는다.

## 마크다운과 Notion 활용
Claude Code는 마크다운을 완벽하게 지원하기 때문에, 복잡한 구조의 정보 전달 시 마크다운을 활용하면 더 정확하게 이해한다.
링크가 포함된 텍스트를 복사해 Claude Code로 붙여넣으면 링크가 사라지지만 Notion에서 복사해 붙여넣는 경우 링크 보존된다.

## 키보드 단축키 완전 정복
| 단축키 | 기능 | 사용 시점 |
|-|-|-|
| Esc Esc | 대화/코드 되감기 | 잘못된 변경 즉시 되돌리기 |
| Ctrl + R | 역방향 검색 | 이전에 사용한 프롬프트 재사용 |
| Ctrl + S | 프롬프트 임시 저장 | 작성 중인 프롬프트를 나중을 위해 보관 | 
| Tab / Enter | 프롬프트 제안 수락 | Claude가 제안한 다음 작업 즉시 실행 |

## Vim 모드로 프롬프트 편집
프롬프트 입력 박스에서 Vim 키 바인딩을 사용할 수 있다.

**사용 가능한 Vim 명령**
- hjkl : 커서 이동
- w, b : 단어 단위 이동
- 0, $ : 줄 시작/끝으로 이동
- dd : 줄 삭제
- yy : 줄 복사
- p : 붙여넣기
- i, a : 삽입 모드
- Esc : 일반 모드

## 입력 박스 탐색 및 편집
Claude Code의 입력 박스는 일반적인 터미널/readline 단축키를 지원한다.

**탐색 단축키**
- Ctrl + A : 줄 시작으로 이동
- Ctrl + E : 줄 끝으로 이동
- Option + Left/Right : 단어 단위 이동

**편집 단축키**
- Ctrl + W : 이전 단어 삭제
- Ctrl + U : 커서부터 줄 시작까지 삭제
- Ctrl + K : 커서부터 줄 끝까지 삭제
- Ctrl + C / Ctrl + L : 현재 입력 지우기
- Ctrl + G : 외부 에디터에서 프롬프트 편집

**이미지 붙여넣기**
- Ctrl + V (Cmd X)

# 4. 컨텍스트 관리의 예술
컨텍스트는 AI의 작업 기억이고, 이를 효과적으로 관리하는 것이 핵심이다.

## 선제적 컨텍스트 압축
대화가 길어지면 컨텍스트가 가득 차 성능이 저하되기 때문에 HANDOFF.md 기법으로 해결할 수 있다.

~~~shell
# 1. 대화가 50k 토큰 이상 사용 중일 때
> "/context로 현재 사용량 확인"

# 2. HANDOFF.md 생성 요청
> "지금까지의 작업 내용을 HANDOFF.md 파일로 정리해줘.
다음 에이전트가 이 파일만 읽고 작업을 이어갈 수 있도록
시도한 것, 성공한 것, 실패한 것, 다음 단계를 명확히 작성해줘."

# 3. 새 세션 시작
> "/clear"

# 4. HANDOFF.md 로드
> "@HANDOFF.md 이 파일을 읽고 작업을 이어가줘"
~~~

## 터미널 탭으로 멀티태스킹
하나의 대화에서 여러 작업을 섞으면 컨텍스트가 오염되기 때문에 여러 탭을 동시에 여러 각각 독립적인 작업을 수행하도록 해야한다.

## 대화 복제 및 반복제
현재 대화를 유지하면서 다른 접근 방식을 시도하고 싶을 때 대화 복제 기능을 사용할 수 있다.

### **Clone: 전체 복제**
/clone 명령어로 현재 대화의 모든 내용을 새로운 UUID로 복제한다.

**사용 사례**
- A 방식과 B 방식 중 뭐가 더 나은 지 확인
- 위험한 변경 시도 전 백업
- 팀원과 공유하기 위한 스냅샷

### Half-Clone: 반복제
/half-clone 명령어로 대화의 후반부만 유지하고 전반부는 삭제해 컨텍스트 사용량은 절반으로 줄이면서 최근 작업은 유지한다.

**사용 사례**
- 대화가 너무 길어져 성능이 저하될 때
- 초기 실험 단계는 필요 업고, 현재 구현만 중요할 때

### 설치 방법 (dx 플러그인 사용)
~~~shell
claude plugin marketplace add ykdojo/claude-code-tips
claude plugin install dx@ykdojo
~~~

## /context로 X-Ray 비전
**최적화 전략**
- 사용하지 않는 MCP 정리
- CLAUDE.md 간소화
- 대화 압축

## realpath로 절대 경로 얻기
다른 디렉토리의 파일을 Claude에게 알려줄 때 절대 경로를 사용하면 혼란을 방지할 수 있다.

# 5. Git과 Github 워크플로우 완전 정복
Claude Code는 Git 및 Github와 완벽하게 통하ㅂ된다.

## Git과 Github CLI 프로 활용
- 자동 커밋 메시지 생성
- Draft PR 자동 생성
- PR 템플릿 활용

## Git worktrees로 병렬 브랜치 작업
여러 브랜치에서 동시 작업해야 할 때 git worktree를 사용하여 브랜치 전환 없이 병렬 작업이 가능하다.

## 대화형 PR 리뷰
PR을 Claude Code로 개요 파악, 파일별 심층 리뷰, 테스트 커버리지 확인, 개선 제안, 자동 수정 등의 처리할 수 있다.

## 승인된 명령어 감사
cc-safe를 통해 ./claude/settings.json 파일 중 위험한 승인 명령어를 감지한다.

**설치 방법**
~~~shell
# 설치
npm install -g cc-safe

# 현재 디렉토리 스캔
npx cc-safe .

# 전체 프로젝트 폴더 스캔
npx cc-safe ~/projects
~~~

# 6. 고급 기능 - MCP, Hooks, Agents
Claude 코드를 단순 코딩 도우미에서 AI 에이전트 팀으로 만드는 기능을 소개한다.

## MCP (Model Context Protocol)
MCP는 Claude가 외부 서비스 및 API와 직접 통신할 수 있도록 만드는 프로토콜이다.

**설치 방법**
~~~shell
claude mcp add -s user <mcp 이름> <설치 명령어>
~~~

**Tip**
MCP는 강력하지만 많아질 수록 컨텍스트 윈도우를 많이 차지한다.
- 10개 미만의 MCP 서버
- 80개 미만의 활성 도구
- 사용하지 않는 MCP는 비활성화

## Hooks
특정 이벤트 발생 시 자동으로 실행되는 셀 명령어이다.

| Hook | 실행 시점 | 사용 사례 |
|-|-|-|
| PreToolUse | 도구 실행 전 | 위험한 명령어 차단 |
| PostToolUse | 도구 실행 후 | 로그 기록, 알림 전송 |
| PermissionRequest | 권한 요청 시 | 자동 승인/거부 |
| Notification | Claude의 알림 시 | 외부 시스템 통합 |
| SubagentStart | 서브에이전트 시작 시 | 모니터링 |
| SubagentStop | 서브에이전트 종료 시 | 결과 수집 |

**예시**
~~~shell
.claude/settings.json

# 위험한 명령어 차단
{
"hooks": {
    "PreToolUse": {
        "command": "bash",
        "args": ["-c", "if echo $TOOL_INPUT | grep -q 'rm -rf /'; then echo 'BLOCKED: Dangerous command'; exit 1; fi"]
        }
    }
}
~~~

## Skills
재사용 가능한 지식 조각으로 Claude가 자동으로 호출하거나 사용자가 수동으로 호출할 수 있다.

~/.claude/skills 에 스킬과 관련된 문서를 추가하여 스킬을 추가할 수 있다.

## Agents
메인 Claude가 작업을 위임할 수 있는 특정 작업에 전문화된 독립적인 AI 프로세스이다.

**특징**
- 각자 독립적인 200k 컨텍스트 보유
- 병렬 실행 가능
- 전문화된 시스템 프롬프트
- 작업 완료 후 결과를 메인 에이전트에게 반환

## Plugins
Hooks, Skills, Agents, MCP 서버를 하나의 패키지로 묶어 배포하고 설치하는 방법이다.
/plugin 명령어를 통해 Marketplace 검색 및 설치가 가능하다.

| 기능 | 로딩 시점 | 주요 사용자 | 토큰 효율성 | 사용 사례 |
|-|-|-|-|-|
| CLAUDE.md | 모든 대화 시작 시 | 개발자 | 낮음 (항상 로드) | 프로젝트 설명, 코딩 스타일, 금지 사항 |
| Skills | 필요 시 자동 | Claude | 높음 (필요 시만) | 특정 작업 자동화 |
| Slash Commands | 수동 호출 시 | 개발자 | 높음 (호출 시만) | 반복 작업 |
| Plugins | 설치 시 | 개발자 | - | 여러 기능을 하나로 묶어 배포 |

# 7. 시스템 최적화와 자동화
## 시스템 프롬프트 슬림화
Claude Code의 모든 행동은 시스템 프롬프트에 의해 결정되고, 이를 줄이면 더 많은 코드 파일과 대화 기록을 컨텍스트에 담을 수 있고 응답 속도 향상 및 비용 절감 효과를 볼 수 있다.
단, 시스템 프롬프트를 잘못 수정하면 성능이 심각하게 저하될 수 있기 때문에 충분한 이해 후 시도해야 한다.

## 장시간 작업을 위한 수동 지수 백오프
장시간 실행되는 작업이 상태 확인은 지수 백오프 전략을 사용하면 토큰을 절약하고, 다른 작업을 병렬로 처리할 수 있다.

**지수 백오프 전략**
처음에는 짧은 간격으로 확인하다가 점차 간격을 늘려가는 방식

## 백그라운드에서 bash 명령 및 에이전트 실행
장시간 실행되는 명령어나 에이전트를 백그라운드로 보내고, 다른 작업을 계속하게 할 수 있다.
명령어 실행 중 Ctrl + B를 누르면 백그라운드로 이동한다.

## Headless 모드로 CI/CD 통합
Claude Code는 CI/CD 파이프라인과 통합이 가능하다.

~~~shell
# 기본 사용
claude -p "Fix the lint errors"

# 파이프라인 통합
git diff | claude -p "Explain these changes"

# JSON 출력
echo "Review this PR" | claude -p --json
~~~

**다음과 같이 사용할 수 있다.**
~~~yaml
name: Claude Code Review

on:
    pull_request:
      types: [opened, synchronize]
      
jobs:
    review:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
            - name: Install Claude Code
              run: curl -fsSL https://claude.ai/install.sh | sh
            - name: Review PR
              env:
                ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
              run: |
                git diff origin/main...HEAD | \
                claude -p "Review this PR and identify potential issues" \
                > review.md
            - name: Comment on PR
              uses: actions/github-script@v6
              with:
                script: |
                    const fs = require('fs');
                    const review = fs.readFileSync('review.md', 'utf8');
                    github.rest.issues.createComment({
                        issue_number: context.issue.number,
                        owner: context.repo.owner,
                        repo: context.repo.repo,
                        body: review
                    });
~~~

# 8. 컨테이너와 샌드박스
안전한 실험과 위험한 작업을 위한 격리 환경 구축 방법을 설명한다.

## 컨테이너로 위험한 작업 격리
별도의 Docker 컨테이너를 생성하여 그 안에서 YOLO 모드로 실행하여 안전하게 작업한다.

## Sandbox 모드와 권한 정리
/sandbox 명령어를 통해 한 번 경계를 정의하고, 그 안에서 자유롭게 작업할 수 있도록 할 수 있다.
    
**설정 예시**
~~~shell
> /sandbox

> "npm install, npm test, git status, git diff를 자동 승인해줘"
# 이제 위 명령어들은 매번 물어보지 않고 자동 실행됩니다.
~~~

** Sandbox는 속도와 보안을 동시에 제공함으로 신뢰할 수 있는 작업에는 Sandbox를, 실험적인 작업에는 컨테이너를 사용하면 된다.

## YOLO 모드
YOLO 모드는 모든 권한 요청에 자동으로 **예**라고 답하여 동작한다.

**사용해야 하는 경우**
- 컨테이너 안에서 실험할 때
- 장시간 자율 작업이 필요할 때
- 신뢰할 수 있는 반복 작업

**절대 사용하지 말아야 하는 경우**
- 호스트 시스템에서 직접 실행
- 중요한 데이터가 있는 디렉토리
- 프로덕션 환경

# 9. 브라우저 통합과 웹 자동화
Claude Code는 브라우저를 직접 제어하여 웹 스크래핑, UI 테스트, 디버깅을 수행할 수 있다.

## 네이티브 브라우저 통합
Chrome에서 Claude Code 확장 설치할 수 있다.

**가능한 작업**
- 페이지 탐색
- 버튼 클릭 및 폼 작성
- 콘솔 에러 읽기
- DOM 검사
- 스크린샷 촬영

### Playwright MCP
동적 웹사이트와 상호작용이 필요할 때 사용한다.

**설치**
~~~shell
claude mcp add -s user playwright npx @playwright/mcp@latest
~~~

**장점**
- 헤드리스 브라우저 지원
- 여러 브라우저 지원
- 네트워크 요청 가로채기
- 파일 다운로드 자동화

## Gemini CLI를 대체 수단으로
네이티브 브라우저나 Playwright으로도 접근이 막힌 웹사이트가 있을 때 Gemini CLI를 대체 수단으로 사용할 수 있다.

# 10. 고급 패턴과 철학
기술을 넘어, 에이전틱 개발자로서의 사고방식과 철학에 대해 설명한다.

## 계획과 빠른 프로토타이핑의 균형
계획은 결과물의 퀄리티를 결정하고, 프로토타이핑은 빠른 MVP를 만들어 다양한 시도가 가능하다.
둘 다 중요하지만 계획이 결과물의 퀄리티를 결정하기에 더 신경쓰는 편이 좋다.

## 개인화된 소프트웨어 시대
각자의 워크플로우에 맞추어 개인화하는 시대에 살고 있고, 원하는 것이 있다면 Claude Code에게 생성을 요청하면 된다.

## 사용이 최고의 학습
AI를 진정으로 이해하고 직관을 얻고 싶다면 많은 토큰을 사용하는 것이 최고의 학습법이다.

## 지식 공유 및 기여
자신이 배운 팁을 공유하게 되면 다른 사람들로부터 더 많은 새로운 것을 배울 수 있다.

## 계속 학습하기
**학습 방법**
- Claude Code에게 직접 물어보기 - Claude는 자신의 기능에 대한 서브 에이전트를 가지고 있다.
- 릴리스 노트 확인 - /release-note로 확인 가능
- 커뮤니티 학습
- Ado 팔로우

# 11. 고급 기능과 SDK
최신 기능과 개발자를 위한 SDK를 설명한다.

## Extended Thinking
ultrathink 키워드를 포함하면, 응답 전 최대 32k의 토큰을 내부 추론에 활용해 복잡한 아키텍처 결정이나 까다로운 디버깅에 유리하다.

~~~shell
.claude/settings.json

{
  "thinking": {
    "maxTokens": 5000, # ultrathink 키워드보다 우선할 수 있다.
  }
}
~~~

## LSP (Language Server Protocol) 통합
Claude에게 IDE 수준의 코드 인텔리전스를 제공한다.

**LSP가 제공하는 것**
- 각 편집 후 에러와 경고를 즉시 진단
- 코드 탐색을 통해 정의 이동, 참조 찾기, 호버 정보 제공
- 언어를 인식해 타입 정보 및 문서 제공

## Claude Agent SDK
Claude Code의 에이전트 루프, 도구, 컨텍스트 관리를 SDK로 사용할 수 있다.

## 팀 설정과 공유 워크플로우
팀 저장소에 .claude/ 폴더를 커밋해 모든 팀원이 동일하게 사용할 수 있다.
~~~shell
.claude/team-settings.json

{
  "permissions": {
    "allow": ["Read(src/)", "Write(src/)", "Bash(npm test)"]
  },
  "hooks": {
    "PreToolUse": {
      "command": "bash",
      "args": ["-c", "echo 'Team hook: validating...'"]
    }
  },
  "mcpServers": {
    "company-db": {
      "command": "npx",
      "args": ["@company/db-mcp"]
    }
  }
}
~~~

# 기타
**공식 문서 및 가이드**
1. Claude Code 공식 문서: https://code.claude.com/docs/en/overview - 설치, 기본 사용법, 모든 명령어 레퍼런스
2. Anthropic Engineering 블로그: https://www.anthropic.com/engineering/claude-code-best-practices - 베스트 프랙티스, 아키텍처 설명, 최신 기능 소개
3. Claude Code GitHub: https://github.com/anthropics/claude-code - 이슈 트래커, 기능 요청, 커뮤니티 논의

**커뮤니티 자료**
1. ykdojo의 claude-code-tips: https://github.com/ykdojo/claude-code-tips - 43가지 파워 팁, 커스텀 스크립트, dx 플러그인
2. Ado의 Advent of Claude 2025: https://adocomplete.com/advent-of-claude-2025/ - 31일간의 팁 시리즈, 초급부터 고급까지
3. r/ClaudeAI 서브레딧: https://www.reddit.com/r/ClaudeAI/ - 커뮤니티 질문, 워크플로우 공유, 최신 소식

**실전 사례 및 경험담**
1. Jacob’s Tech Tavern: https://blog.jacobstechtavern.com/p/claude-code-productivity - “Claude Code가 나를 50-100% 더 생산적으로 만들었다”
2. The Pragmatic Engineer: https://newsletter.pragmaticengineer.com/p/how-claude-code-is-built - Claude Code가 어떻게 만들어졌는지 내부 구조 분석
3. Lenny’s Newsletter: https://www.lennysnewsletter.com/p/everyone-should-be-using-claude-code - 비개발자도 Claude Code를 사용해야 하는 이유