# Context7 MCP Server 설정

## 개요

Context7은 최신 라이브러리 문서와 코드 예제를 실시간으로 가져와서 Claude에게 제공하는 MCP 서버입니다.
- 공식 문서 기반의 최신 정보 제공
- 구버전 API나 잘못된 메서드 참조 방지
- 프롬프트에 자동으로 문서 주입

## 기본 설정 (API 키 없이)

```json
{
  "mcpServers": {
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp"
    }
  }
}
```

## API 키와 함께 사용 (높은 rate limit 제공)

API 키는 https://context7.com/dashboard 에서 발급받을 수 있습니다.

```json
{
  "mcpServers": {
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "YOUR_API_KEY_HERE"
      }
    }
  }
}
```

## 설정 파일 위치

### 전역 설정 (모든 프로젝트에 적용)
`~/.claude/settings.json`

### 프로젝트별 설정 (현재 프로젝트만)
`.claude/settings.json`

### 로컬 개인 설정 (버전 관리 제외)
`.claude/settings.local.json`

## 사용 방법

프롬프트에 "use context7"를 추가하면 작업 중인 라이브러리의 최신 문서를 자동으로 가져옵니다.

예시:
```
React 18에서 useEffect 사용법을 알려줘. use context7
```

## 설치 명령어 참고

```bash
# HTTP transport 방식 (추천)
claude mcp add --transport http context7 https://mcp.context7.com/mcp

# API 키와 함께
claude mcp add --transport http context7 https://mcp.context7.com/mcp --header "CONTEXT7_API_KEY: YOUR_API_KEY"

# npx 방식
claude mcp add context7 -- npx -y @upstash/context7-mcp --api-key YOUR_API_KEY
```

## 유용한 점

- 실시간 문서 업데이트로 항상 최신 정보 제공
- Private repository 접근 (API 키 필요)
- 높은 rate limit (API 키 사용 시)
- 버전별 문서 제공
