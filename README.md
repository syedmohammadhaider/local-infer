# Local Inference Console

A single-file, fully offline chat interface for running local LLMs through [Ollama](https://ollama.com) — built for working with models like Qwen2.5-Coder when you don't have, or don't want, an internet connection.

No server, no build step, no npm install, no CDN calls. One HTML file that talks directly to your local Ollama instance. Nothing leaves your machine.

## Features

- Streams responses token-by-token from Ollama's native `/api/chat` endpoint
- Auto-detects installed models and prioritizes Qwen2.5-Coder if present
- Adjustable temperature, max tokens, and context window
- Editable system prompt
- Live tok/s and generation-time readout, pulled directly from Ollama's own stats
- Lightweight syntax highlighting and one-click copy on code blocks
- Exports the conversation log to Markdown
- Zero dependencies — everything is inline

## Requirements

- [Ollama](https://ollama.com) installed and running
- At least one pulled model, e.g. `ollama pull qwen2.5-coder:7b`

## Setup

1. Download `ollama-coder-console.html` (or clone this repo)
2. Start Ollama: `ollama serve`
3. Open the file in a browser — double-click it, or serve it locally with `python3 -m http.server` from the same folder

## CORS

Browsers block cross-origin requests by default, and Ollama only allows specific origins out of the box. If the status indicator in the left panel says OFFLINE, allow all origins and **restart Ollama** — setting the variable alone does nothing until the process restarts:

- **macOS:** `launchctl setenv OLLAMA_ORIGINS "*"` then `pkill Ollama; open -a Ollama`
- **Linux (systemd):** `sudo systemctl edit ollama.service`, add `Environment="OLLAMA_ORIGINS=*"` under `[Service]`, then `sudo systemctl daemon-reload && sudo systemctl restart ollama`
- **Windows:** set `OLLAMA_ORIGINS` under System Environment Variables, then fully quit and relaunch Ollama from a new shell

Verify it worked, independent of the browser:

```
curl -I -X OPTIONS http://localhost:11434/api/tags -H "Origin: http://example.com"
```

If the response includes `Access-Control-Allow-Origin`, the server is configured correctly and any remaining issue is on the browser side (e.g. opening the file via `file://` — try serving it over `http://localhost` instead).

## Configuration

Everything is in the left panel and applies per session:

- **Endpoint** — point at a different host if Ollama runs elsewhere on your network
- **Model** — auto-populated from `/api/tags`
- **Temperature / max tokens / context window**
- **System prompt**

## How it works

The page calls Ollama's native `/api/chat` with `stream: true` and reads the response as newline-delimited JSON, appending each token as it arrives. Markdown rendering, code-block highlighting, and the tok/s readout are all done with a few hundred lines of vanilla JS — no frameworks, no external requests of any kind.

## License

MIT — see [LICENSE](LICENSE) for details.

---

Built with [Claude](https://claude.ai).
