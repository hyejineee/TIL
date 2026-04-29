# TIL

> 나는 오늘 무엇을 배웠는가...
> 나는 오늘 무엇을 느꼈는가...
> 나는 무엇인가...🤪

RAG로도 읽히는 학습 기록. 1 파일 = 1 주제 원칙, 모든 파일 frontmatter 필수.

## 🔎 탐색

**주제별·태그별·날짜별 인덱스 → [`INDEX.md`](./INDEX.md)**

## 📂 구조

```
notes/
├── ai-workflow/     # Claude Code·Cursor·AI 하네스 관련
├── android/         # Android 개발
├── devops/          # Docker·Elasticsearch·Serverless 등
├── git/             # Git 워크플로
├── javascript/      # JS 언어 기본
├── react/           # React·Hook
├── testing/         # Jest·Vitest·TDD·회귀 방지
└── tooling/         # 내부 도구·devtools 설계
```

## ✍️ 새 TIL 추가

1. `notes/<category>/<slug>.md` 생성
2. Frontmatter 필수:
   ```yaml
   ---
   title: ...
   date: YYYY-MM-DD
   category: <notes/ 하위 디렉토리>
   tags: [...]
   summary: 1~2문장 요약
   ---
   ```
3. [`INDEX.md`](./INDEX.md) 3곳 갱신 (By Topic / By Tag / By Date)
