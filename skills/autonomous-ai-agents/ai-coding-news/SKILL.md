---
name: ai-coding-news
description: "Landscape and news tracking for AI coding assistants/agents (Claude Code, Codex, Cursor, Antigravity, Copilot, Windsurf, Kiro) and underlying models (Claude, GPT, Gemini). Use when asked about AI coding tool comparisons, new model releases, or building a coding-AI news digest."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [ai-coding, claude-code, codex, cursor, agentic-coding, llm-releases, news-digest]
    related_skills: [claude-code, codex, opencode, hermes-agent, blogwatcher, github-trending-spider]

---

# AI Coding Tools & Models — Landscape Tracker

Reference skill for the fast-moving agentic-coding space: which tools exist, how they're positioned, and what to check for fresh news. Pair with a research/scraping skill (`blogwatcher`, `github-trending-spider`, `anysearch`) for live updates — this file is the baseline map, not a substitute for a fresh search when asked "what's new."

## When to use

- User asks to compare AI coding tools/agents, or "what should I use for X"
- User asks about a new model or coding-agent release
- Building a recurring "AI coding news" digest

## The field as of mid-2026 (baseline — verify dates before quoting)

Seven serious agentic-coding contenders, each with a different philosophy:

- **Claude Code** (Anthropic) — deepest reasoning tier via Opus, large context window, subagent/parallel-workflow model, terminal-first. This Hermes install itself is built on the same underlying agent-loop philosophy — see the `hermes-agent`, `claude-code` skills.
- **OpenAI Codex** — ships as CLI + desktop app (macOS/Windows), tracks OpenAI's latest GPT release.
- **Cursor** (Anysphere) — IDE-first, fast inline editing; ownership/market situation shifts periodically (check for current parent company before quoting pricing/roadmap).
- **Google Antigravity** — multi-surface (desktop app, CLI, SDK, Managed Agents API, enterprise layer on Google Cloud), built around Gemini.
- **GitHub Copilot** — deepest VS Code integration, strongest at inline completion rather than autonomous multi-file work.
- **Windsurf**, **Kiro** — smaller but actively developed alternatives; check current positioning, they move fast.

**Common developer pattern**: most professional users run 2-3 tools in parallel rather than picking one — e.g. an IDE-first tool for editing, a terminal agent for architecture/debugging/CI, and inline completion for boilerplate. Don't recommend "the one true tool" without asking about the user's actual workflow first.

## What counts as news worth surfacing

- New foundation model releases relevant to coding (Claude, GPT, Gemini families) — note exact version/codename and GA date, not just announcement date
- New agentic-coding product features: parallel/background agents, context window changes, new IDE/terminal surfaces, pricing changes
- Ownership/organizational changes (acquisitions, funding) when they affect roadmap or pricing
- Benchmark shifts (SWE-bench and similar) only when tied to a specific named release — raw leaderboard churn without a release attached isn't worth a digest entry

## Researching what's new

This space changes weekly. When building a digest or answering a "what's new" question, search current sources (official release notes/changelogs for each tool, plus general tech coverage) rather than relying on this file's snapshot — verify the date of any article against the current month before citing it as current.
