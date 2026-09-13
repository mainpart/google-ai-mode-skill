![google-ai-mode-skill](docs/banner.png)

# google-ai-mode-skill

An agent skill that asks Google AI Mode from a background tab of your own Chrome, keeps the follow-ups in one Google thread, and returns the answer with source links.

## Install

```bash
npx skills add mainpart/google-ai-mode-skill -a claude-code -g -y
```

Or clone and run `claude --plugin-dir ./google-ai-mode-skill`.

## Requirements

A browser tool in the session that can open a tab in your logged-in Chrome and run JavaScript in it — four operations: list tabs, open a tab without focusing it, run a script, close the tab. Known mappings (mcp-chrome, chrome-devtools-mcp, Playwright MCP over CDP) are in [`skills/google-ai-mode/references/browser-tools.md`](skills/google-ai-mode/references/browser-tools.md); anything with the same effect works. Google AI Mode has to be available for your account and region.

## What it does

- Rewrites the question into a precise query (technology, version, year, aspects, answer format), opens `google.com/search?udm=50` in a background tab, waits for the answer and pulls its text plus up to 15 sources.
- Asks follow-ups through the page's own thread endpoint, so "and how does that differ from X" is understood in context — no clicks, no focus stolen from you.
- On a research question it interrogates the premises of the query (definitions, grounds, objections, consequences) over 2–4 turns rather than accepting the first overview.
- Called with no arguments, it takes the topic of the current conversation and checks it from several angles, then reports what diverged from what was said.

## Examples

> /google-ai-mode Give me the patient classifications used in homeopathy and what other classifiers exist for choosing a remedy

> (after a discussion about MCP browser servers) /google-ai-mode

The second form produces "Topic: … Checking from 4 angles" and a write-up that names which claims from the conversation the search did not confirm.
