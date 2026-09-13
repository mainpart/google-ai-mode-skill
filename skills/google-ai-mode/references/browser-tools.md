# Browser tools: how known servers map onto the four operations

The skill needs four operations — **list tabs**, **open tab** (in the background, in an existing window), **run script** (async JavaScript in that tab, ≥ 60 s), **close tab**. This table is a set of worked mappings, not a whitelist. Any tool set with the same effect works; when yours isn't listed, find the closest match by effect and note where it differs.

What matters most: the browser must be the user's own logged-in Chrome. Google AI Mode answers depend on the signed-in account and region; a fresh headless profile often gets "AI Mode is not currently available" or a captcha.

| | mcp-chrome | chrome-devtools-mcp | Playwright MCP |
|---|---|---|---|
| Project | [hangwin/mcp-chrome](https://github.com/hangwin/mcp-chrome), npm `mcp-chrome-bridge`, a Chrome extension + local bridge; streamable HTTP on `http://127.0.0.1:12306/mcp` | [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) by Google | [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) |
| Uses the user's logged-in Chrome | Yes, always — it lives inside the running browser | Yes with `--autoConnect` (Chrome 144+), `--browserUrl http://127.0.0.1:9222` or `--wsEndpoint`; without them it launches its own instance | Only with `--cdp-endpoint <url>` or `--extension` (Playwright Extension in Chrome/Edge); `--user-data-dir` keeps a profile but is still a separate instance |
| list tabs | `get_windows_and_tabs` → `windows[].windowId`, `tabs[].tabId` | `list_pages` → `pageId` | `browser_tabs {action: "list"}` → index |
| open tab, no focus | `chrome_navigate {url, windowId, background: true}`; returns `tabId`. Never pass `newWindow: true`. Re-navigate: `{url, tabId, background: true}` | `new_page {url, background: true}` ("open the page in the background without bringing it to the front"); re-navigate: `navigate_page {pageId, type: "url", url}` | `browser_tabs {action: "new", url}` — **no background option is documented**; the new tab becomes the active one. Warn the user first. Re-navigate: `browser_navigate {url}` on the selected tab |
| run script | `chrome_javascript {tabId, code, timeoutMs}`; `code` is an async function body (`await` and `return` work) | `evaluate_script {pageId, function}`; `function` is a function declaration — wrap the skill's body as `async () => { … }`; supports async | `browser_evaluate {function}`; `function` is `() => { … }` — wrap as `async () => { … }`; no timeout parameter documented |
| close tab | `chrome_close_tabs {tabIds: [id]}` | `close_page {pageId}` | `browser_tabs {action: "close", index}` |
| Switch-tab tool to avoid | `chrome_switch_tab` | `select_page` with `bringToFront: true` | `browser_tabs {action: "select"}` |
| Output filter | **Yes.** Script results are sanitised: text dense with `=`/`&` becomes `[BLOCKED: Cookie/query string data]`, long letter/digit/slash runs become `<redacted_base64>`. That is why every script returns through `safe()` | None documented | None documented |

Tool names and parameters above were read from each project's README / tool reference at the time of writing. When a name doesn't match what your session shows, trust the session: the operation is what matters.

## Other browser agents and extensions

Anything that (a) drives the user's real Chrome and (b) can evaluate JavaScript in a tab and return the value fits — for example a browser-agent extension that exposes an "execute script" action. Map its actions onto the four operations the same way. If it can only read the page as text or take screenshots, steps 3–4 still work in a degraded form (read `main-col` and the source panel as text), but step 5 (follow-up in the same thread) does not: it is a `fetch()` made from inside the page and needs script evaluation.
