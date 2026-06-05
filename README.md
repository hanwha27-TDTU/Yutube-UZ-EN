# Pronounce Clip Lab MVP

YouTube 검색, 영상 재생, 단어/영상 저장, 녹음 연습, Supabase 기기 동기화를 위한 첫 실사용 MVP입니다.

## 구성

- `index.html`: 브라우저에서 실행하는 앱
- `supabase/schema.sql`: Supabase 테이블
- `supabase/functions/youtube-search/index.ts`: YouTube 검색 Edge Function

## 빠른 실행

1. Supabase SQL Editor에서 `supabase/schema.sql`을 실행합니다.
2. Supabase Edge Function으로 `youtube-search`를 배포합니다.
3. Edge Function 환경변수에 `YOUTUBE_API_KEY`를 넣습니다.
4. `index.html`을 GitHub Pages, Vercel, Netlify, 또는 로컬 서버로 엽니다.
5. 앱의 `설정` 탭에 아래 값을 입력합니다.
   - YouTube 검색 함수 URL
   - Supabase URL
   - Supabase anon key
   - 사용자 ID

## Supabase CLI 예시

```bash
supabase functions deploy youtube-search
supabase secrets set YOUTUBE_API_KEY=YOUR_YOUTUBE_API_KEY
```

## 동기화 규칙

이 앱은 각 레코드에 `id`, `created_at`, `updated_at`, `deleted_at`을 둡니다.

- 일반 자동 동기화: 로컬과 클라우드를 `updated_at` 최신 기준으로 병합한 뒤 클라우드에 upsert합니다.
- 이 기기를 최종본으로: 로컬 데이터를 클라우드에 올리고 `language_sync_meta`의 `canonical:<user_id>` 값을 갱신합니다.
- 다른 기기에서 canonical version 변경 감지: 로컬 전용 항목을 보존하지 않고 클라우드 기준으로 맞춥니다.
- 삭제: 즉시 제거하지 않고 `deleted_at`을 갱신하는 tombstone 방식입니다.

## 중요한 제한

YouTube 공식 Captions API는 공개 영상 자막을 임의로 다운로드하는 용도가 아닙니다. 실제 자막/문장 학습을 강화하려면 자체 문장 DB, 사용자가 직접 입력한 문장, 또는 합법적으로 사용할 수 있는 transcript 제공자를 연결해야 합니다.

녹음 파일은 현재 브라우저 로컬 URL로 저장됩니다. 여러 기기에서 녹음 오디오까지 공유하려면 Supabase Storage 업로드를 다음 단계로 추가하세요.
