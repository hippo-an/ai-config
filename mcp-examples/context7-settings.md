# Context7 MCP Server 설정

## 개요

Context7은 최신 라이브러리 문서와 코드 예제를 실시간으로 가져와서 Claude에게 제공하는 MCP 서버입니다.
- 공식 문서 기반의 최신 정보 제공
- 구버전 API나 잘못된 메서드 참조 방지
- 프롬프트에 자동으로 문서 주입

## 추천 설치 방법 (npx 방식)

가장 안정적으로 동작하는 방법입니다:

```bash
# 기본 설치 (API 키 없이)
claude mcp add context7 -- npx -y @upstash/context7-mcp

# API 키와 함께 설치 (높은 rate limit 제공)
claude mcp add context7 -- npx -y @upstash/context7-mcp --api-key YOUR_API_KEY
```

설치 후 `/mcp` 명령어로 연결 상태를 확인하세요.

## 수동 설정 (settings.json)

### 기본 설정 (API 키 없이)

```json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    }
  }
}
```

### API 키와 함께 사용

API 키는 https://context7.com/dashboard 에서 발급받을 수 있습니다.

```json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp", "--api-key", "YOUR_API_KEY_HERE"]
    }
  }
}
```

## 설정 파일 위치

### 프로젝트 설정 (현재 프로젝트만, 추천)
`.claude.json` - MCP 서버 설정은 이 파일에 자동으로 저장됩니다

### 전역 설정 (모든 프로젝트에 적용)
`~/.claude/settings.json`

### 프로젝트별 설정 파일
`.claude/settings.json`

### 로컬 개인 설정 (버전 관리 제외)
`.claude/settings.local.json`

## 사용 방법

MCP 서버가 활성화되면 Claude가 필요할 때 자동으로 최신 문서를 가져옵니다.

예시:
```
React 18에서 useEffect 사용법을 알려줘
```

명시적으로 MCP를 활성화/비활성화하려면:
- `/mcp` - MCP 서버 목록 확인 및 관리
- `@context7` - 프롬프트에서 직접 언급하여 사용

## MCP 서버 관리 명령어

```bash
# MCP 서버 목록 및 상태 확인
claude mcp list

# MCP 서버 제거
claude mcp remove context7

# MCP 서버 재연결
claude mcp reconnect
```

## 문제 해결

### "Failed to connect" 에러가 발생하는 경우

1. HTTP transport 방식 대신 stdio (npx) 방식 사용:
   ```bash
   claude mcp remove context7
   claude mcp add context7 -- npx -y @upstash/context7-mcp
   ```

2. 연결 상태 확인:
   ```bash
   claude mcp list
   ```

3. 프로젝트 디렉토리에서 사용하는 경우 `.claude.json` 파일 확인

## 유용한 점

- 실시간 문서 업데이트로 항상 최신 정보 제공
- Private repository 접근 (API 키 필요)
- 높은 rate limit (API 키 사용 시)
- 버전별 문서 제공
