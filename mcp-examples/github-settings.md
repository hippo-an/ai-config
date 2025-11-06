# GitHub MCP Server 설정

## 개요

GitHub MCP Server는 Claude가 GitHub API에 직접 접근할 수 있게 해주는 공식 MCP 서버입니다.
- Repository 정보 조회
- Issue 및 Pull Request 관리
- 프로젝트 구조 파악
- Commit 히스토리 조회

## 추천 설치 방법 (Remote HTTP)

GitHub에서 호스팅하는 원격 서버를 사용하는 방식으로, 가장 안정적입니다.

### 1. GitHub Personal Access Token (PAT) 생성

1. GitHub 웹사이트 접속 → Settings → Developer settings → Personal access tokens → Tokens (classic)
2. "Generate new token (classic)" 클릭
3. 필요한 권한 선택:
   - `repo` - Repository 접근 (필수)
   - `read:org` - Organization 정보 읽기 (선택)
4. 토큰 생성 후 **반드시 복사해두기** (다시 볼 수 없음)

### 2. MCP 서버 설치

```bash
# PAT를 사용하여 GitHub MCP 서버 추가
claude mcp add --transport http github https://api.githubcopilot.com/mcp --header "Authorization: Bearer YOUR_GITHUB_PAT"
```

**예시:**
```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp --header "Authorization: Bearer ghp_xxxxxxxxxxxxxxxxxxxx"
```

설치 후 연결 상태 확인:
```bash
claude mcp list
```

## 수동 설정 (settings.json 또는 .claude.json)

프로젝트별로 설정하려면 `.claude.json`에 다음과 같이 추가됩니다:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_GITHUB_PAT"
      }
    }
  }
}
```

**⚠️ 보안 주의사항:**
- `.claude.json` 파일은 **절대 git에 커밋하지 마세요**
- `.gitignore`에 추가하여 실수로 커밋되지 않도록 하세요
- PAT는 정기적으로 교체하는 것이 좋습니다

## 로컬 서버 방식 (Alternative)

원격 서버 대신 로컬에서 실행하려면:

### npx 방식

```bash
claude mcp add github --env GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_PAT -- npx -y @modelcontextprotocol/server-github
```

### Docker 방식

```bash
claude mcp add github --env GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_PAT -- docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN ghcr.io/github/github-mcp-server
```

## 설정 파일 위치

### 프로젝트 설정 (현재 프로젝트만)
`~/.claude.json` - MCP 서버 설정이 프로젝트별로 저장됩니다

### 전역 설정 (모든 프로젝트)
`~/.claude/settings.json`

### 로컬 프로젝트 설정
`.claude/settings.local.json` - 프로젝트별 로컬 설정

## 사용 방법

MCP 서버가 활성화되면 Claude가 필요할 때 자동으로 GitHub API를 사용합니다.

예시 프롬프트:
```
이 프로젝트의 최근 commit 히스토리를 보여줘
```

```
anthropic/claude-code 레포지토리의 README를 읽어줘
```

```
현재 오픈된 issue 목록을 보여줘
```

명시적으로 MCP를 관리하려면:
- `/mcp` - MCP 서버 목록 확인 및 관리
- `@github` - 프롬프트에서 직접 언급하여 사용

## MCP 서버 관리 명령어

```bash
# MCP 서버 목록 및 상태 확인
claude mcp list

# MCP 서버 상세 정보 확인
claude mcp get github

# MCP 서버 제거
claude mcp remove github

# MCP 서버 재연결
claude mcp reconnect
```

## 문제 해결

### "Failed to connect" 에러가 발생하는 경우

1. PAT가 올바른지 확인:
   ```bash
   # GitHub API 직접 테스트
   curl -H "Authorization: Bearer YOUR_PAT" https://api.github.com/user
   ```

2. PAT 권한 확인:
   - GitHub Settings → Developer settings에서 토큰 권한 재확인
   - 최소한 `repo` 권한이 필요합니다

3. MCP 서버 재등록:
   ```bash
   claude mcp remove github
   claude mcp add --transport http github https://api.githubcopilot.com/mcp --header "Authorization: Bearer YOUR_PAT"
   ```

### PAT 업데이트 방법

```bash
# 기존 서버 제거
claude mcp remove github

# 새 PAT로 다시 추가
claude mcp add --transport http github https://api.githubcopilot.com/mcp --header "Authorization: Bearer NEW_PAT"
```

## 보안 권장사항

1. **PAT 관리**
   - 최소 권한 원칙 적용 (필요한 권한만 부여)
   - 정기적인 토큰 교체 (3-6개월)
   - 사용하지 않는 토큰은 즉시 삭제

2. **파일 보안**
   - `.claude.json` 파일을 `.gitignore`에 추가
   - `.claude/settings.local.json`을 `.gitignore`에 추가
   - 팀과 공유할 설정은 PAT를 제외하고 예시 파일로 제공

3. **토큰 유출 시**
   - GitHub Settings에서 즉시 토큰 삭제
   - 새 토큰 생성 및 재설정
   - Git 히스토리에 토큰이 커밋된 경우 히스토리 정리 필요

## 사용 가능한 기능

- **Repository 조회**: 레포지토리 정보, 파일 구조, README 등
- **Issue 관리**: Issue 목록, 생성, 업데이트
- **Pull Request**: PR 목록, 상태, 변경사항 조회
- **Commit 히스토리**: 커밋 로그, diff 확인
- **Search**: 코드, Issue, PR 검색
- **User/Organization**: 사용자 및 조직 정보

## GitHub Enterprise 사용자

GitHub Enterprise Server를 사용하는 경우:

```bash
# 환경 변수로 호스트 지정
claude mcp add github --env GITHUB_HOST=https://github.yourcompany.com --env GITHUB_PERSONAL_ACCESS_TOKEN=YOUR_PAT -- npx -y @modelcontextprotocol/server-github
```

## 유용한 점

- GitHub 정보를 수동으로 복사-붙여넣기 할 필요 없음
- 실시간으로 최신 레포지토리 정보 접근
- Private repository 접근 가능 (PAT 권한에 따라)
- 여러 레포지토리를 자유롭게 탐색
