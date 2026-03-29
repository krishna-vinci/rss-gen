# RSS Gen

Docker-first FastAPI app for generating and discovering RSS feeds for:
- YouTube channels
- Reddit subreddits
- Websites via Feedsearch

Lightweight frontend included. No Node build step.

## Features

- YouTube channel resolution with feed variants: `all`, `videos`, `shorts`, `live`
- Reddit subreddit feed discovery
- Website feed discovery via Feedsearch
- JSON API + XML feed responses
- Simple HTML/CSS/JS UI served by FastAPI

## Project layout

```text
app.py
requirements.txt
fastapi_backend/
├── main.py
├── services.py
├── models.py
├── cache.py
├── feedsearch_api_docs.md
└── frontend/
    ├── index.html
    └── assets/
        ├── app.js
        └── styles.css
```

## Quick start

```bash
docker compose up --build
```

Open:
- App: `http://127.0.0.1:3464/`
- API docs: `http://127.0.0.1:3464/docs`
- API root: `http://127.0.0.1:3464/api/v1`

## Local development

If you want to run outside Docker:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app:app --reload --proxy-headers
```

Then open:
- App: `http://127.0.0.1:8000/`
- API docs: `http://127.0.0.1:8000/docs`
- API root: `http://127.0.0.1:8000/api/v1`

## API usage

### Health

```bash
curl http://127.0.0.1:3464/api/v1/health
```

### Unified resolver

Auto-detects YouTube, Reddit, or website input.

```bash
curl "http://127.0.0.1:3464/api/v1/resolve?query=@mkbhd"
curl "http://127.0.0.1:3464/api/v1/resolve?query=r/selfhosted"
curl "http://127.0.0.1:3464/api/v1/resolve?query=arstechnica.com"
```

Query params:
- `reddit_limit=10`
- `include_preview=true|false`
- `preview_limit=6`
- `feedsearch_info=true|false`
- `feedsearch_favicon=true|false`
- `feedsearch_skip_crawl=true|false`

### YouTube

Resolve a channel:

```bash
curl "http://127.0.0.1:3464/api/v1/youtube/resolve?query=@mkbhd"
```

Fetch XML feeds:

```bash
curl "http://127.0.0.1:3464/api/v1/youtube/feed/videos/UCBJycsmduvYEL83R_U4JriQ.xml"
curl "http://127.0.0.1:3464/api/v1/youtube/feed/shorts/UCBJycsmduvYEL83R_U4JriQ.xml"
curl "http://127.0.0.1:3464/api/v1/youtube/feed/live/UCBJycsmduvYEL83R_U4JriQ.xml"
```

Legacy paths are also supported:

```bash
curl "http://127.0.0.1:3464/feeds/videos.xml?channel_id=UCBJycsmduvYEL83R_U4JriQ&type=videos"
curl "http://127.0.0.1:3464/feeds/UCBJycsmduvYEL83R_U4JriQ"
curl "http://127.0.0.1:3464/feed/videos/UCBJycsmduvYEL83R_U4JriQ"
```

### Reddit

```bash
curl "http://127.0.0.1:3464/api/v1/reddit/resolve?query=r/selfhosted&limit=10"
```

### Feedsearch

```bash
curl "http://127.0.0.1:3464/api/v1/feedsearch/search?url=arstechnica.com"
curl "http://127.0.0.1:3464/api/v1/feedsearch/search?url=arstechnica.com&opml=true"
```

## Environment variables

Optional:

```bash
RSS_GEN_APP_NAME="RSS Gen"
RSS_GEN_CACHE_DIR=".cache"
RSS_GEN_CACHE_MAX_AGE_SECONDS=86400
RSS_GEN_CHANNEL_CACHE_TTL_SECONDS=1800
RSS_GEN_FEEDSEARCH_CACHE_TTL_SECONDS=900
RSS_GEN_REQUEST_TIMEOUT_SECONDS=15
RSS_GEN_PREVIEW_TIMEOUT_SECONDS=5
RSS_GEN_USER_AGENT="rss-gen-fastapi/1.0"
RSS_GEN_FEEDSEARCH_URL="https://feedsearch.dev/api/v1/search"
RSS_GEN_CORS_ORIGINS="*"
```

## Docker

```bash
docker compose up --build
```

The container listens on port `3460`; Compose maps it to host port `3464`.

## Notes

- YouTube resolution uses `yt-dlp`, so no YouTube API key is required.
- Website discovery uses Feedsearch and includes attribution in responses.
- Keep `--proxy-headers` enabled behind a proxy so generated feed URLs use the public host.

## Credits

- Discovery strategy notes: [`discovery_feed_strategy.md`](./discovery_feed_strategy.md)
- Feedsearch API docs: [`fastapi_backend/feedsearch_api_docs.md`](./fastapi_backend/feedsearch_api_docs.md)

## License

MIT
