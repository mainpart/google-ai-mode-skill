![google-ai-mode-skill](docs/banner.png)

# google-ai-mode-skill

**The problem.** You send an agent to research a topic. It fires thirty searches, fetches thirty pages, reads them all, stitches them together, decides what is still missing, and goes back for another round. Every page it reads passes through your context window and your bill, and most of it is discarded on the way to the summary.

**The fix.** Hand that loop to Google. Google AI Mode already does the reading and mixing on its side: one query returns a digest of many sources with links, and the thread accepts follow-ups — so the agent can push the topic from a different angle, ask for what was missed, or challenge a claim, and get the next digest back, without ever fetching a page itself. The agent only reads summaries, which saves tokens and time and leaves the source list for you to check.

This skill runs that loop from a background tab of your own Chrome — your account, your region, no focus stolen — and knows how to keep the follow-ups inside one Google thread.

## Install

```bash
npx skills add mainpart/google-ai-mode-skill -a claude-code -g -y
```

Or clone and run `claude --plugin-dir ./google-ai-mode-skill`.

## Requirements

A browser tool in the session that can open a tab in your logged-in Chrome and run JavaScript in it — four operations: list tabs, open a tab without focusing it, run a script, close the tab. Known mappings (mcp-chrome, chrome-devtools-mcp, Playwright MCP over CDP) are in [`skills/google-ai-mode/references/browser-tools.md`](skills/google-ai-mode/references/browser-tools.md); anything with the same effect works. Google AI Mode has to be available for your account and region.
