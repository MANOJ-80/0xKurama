# Kurama prototype

A local Ubuntu prototype for text and voice commands. Sarvam interprets requests and selects from bounded tools: launch an installed app, open a website or web search, list folders, search filenames, open a file in its default application, and inspect desktop windows or accessibility trees through [`computer-use-linux`](https://github.com/agent-sh/computer-use-linux). It does not send messages, edit or delete files, read file contents, or run shell commands.

## Run

The project uses Python's standard library. Python 3.10+, Node.js/npm, `gtk-launch`, and `xdg-open` are needed. A Sarvam API key is required for chat and speech.

```bash
cp .env.example .env
# Put your Sarvam key in .env, then run:
python3 kurama.py
```

Open [http://127.0.0.1:8765](http://127.0.0.1:8765) in a browser. Press the microphone button to record for up to 25 seconds, stop to transcribe, review or edit the transcript, then press **Send**. Select **Speak replies with Sarvam** to hear replies. `Ctrl+C` stops the server.

The key is read from the root `.env` file as `SARVAM_API_KEY`. Keep that file private; it is ignored by Git. Do not put the key in the browser or source files. Kurama starts the computer-use-linux MCP package through `npm exec` on first run if it is not already cached.

## Current boundaries

- Chat uses Sarvam Chat Completions V2 with `glm5.3-flash`.
- Voice uses the Sarvam Saaras V4 transcription and Bulbul V4 Flash speech APIs. Audio is uploaded to Sarvam only when you record or request spoken output.
- File listing and search operate under your home folder and return filenames only. Sensitive key folders are skipped. Kurama does not read file contents.
- Tool calls are dynamically selected by the model, but only the operations defined in `kurama.py` are available. There is no unrestricted shell, messaging, form submission, file write, or deletion tool.
- The upstream `computer-use-linux` 0.7.59 MCP server provides read-only app/window discovery and accessibility trees. App launching is enabled. Clicks, typing, field replacement, scrolling, key presses, and window focus are enabled behind an exact per-action approval card. In this GNOME Wayland session, the Calculator accessibility tree is readable, but GNOME has not loaded the installed extension's window-control D-Bus service; even an approved background AT-SPI action was refused. Start a fresh GNOME login before retrying. Screenshot capture is also denied by the current desktop portal/Shell permissions.
- When asked to inspect a specific app, its accessibility labels and visible text are sent to Sarvam along with the conversation. Screenshot capture is disabled in Kurama.
- Keep this server bound to localhost. It rejects non-local host headers and cross-origin browser requests, but it has no login and is not intended to be exposed to the network.

## Check the server

With Kurama running, `http://127.0.0.1:8765/api/health` reports server status, the selected model, whether a key is configured, and the number of available tools. It never returns the key.
