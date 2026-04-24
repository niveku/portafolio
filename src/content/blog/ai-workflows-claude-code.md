---
title: "Building AI Workflows with Claude Code and MCPs"
summary: "How I use Claude Code, custom skills, and Model Context Protocol servers to operate across development, writing, and knowledge management — and what the experience reveals about how AI tooling actually compounds."
date: 2026-04-23
tags: ["AI", "claude-code", "automation", "workflow", "MCP"]
---

Most AI tools are used as standalone assistants: you open a chat, ask a question, get an answer, and move on. Each session starts cold. Context resets. The AI knows nothing about your project, your codebase, your decisions from yesterday.

Claude Code changes this — but only if you build around it intentionally.

## What Claude Code actually is

Claude Code is a CLI for Claude that runs in your terminal with direct access to your file system, shell, and tools. Unlike a chat interface, it can read your code, run commands, write files, and chain operations across a session. It's agentic — it takes actions, not just generates text.

The interesting layer on top of this is the plugin and skill system. Skills are reusable instruction sets stored as Markdown files. MCPs (Model Context Protocol servers) are integrations that give the AI direct access to external systems — your Obsidian vault, your calendar, a database, an API.

## The compounding effect

A skill is written once and invoked many times. Once I have a skill for "write a cover letter from this job description using my career materials," I don't have to re-explain the task, the format, or the context. The skill carries it.

MCPs add persistent context. When Claude Code can read my Obsidian vault directly, it isn't working from copy-pasted notes — it's reading the actual source of truth. When I ask it to check my career narrative against a new role, it pulls the current version, not a version I remembered to paste.

The result is that each improvement to the system compounds. A better career OS in Obsidian means better AI-assisted career work. A better skill for code review means faster, more consistent reviews across every future project.

## What breaks

The weak point is context accumulation over long sessions. Long conversations fill the context window and degrade performance. Claude Code has compaction built in, but the discipline of keeping sessions focused — one task, one context — still matters.

Skills also require maintenance. An instruction that worked for one version of a workflow may need updating as the workflow evolves. I treat skills the same way I treat code: they need review, they accumulate debt, and a bad skill causes consistent bad outputs.

## The practical setup

My current stack:

- **Claude Code** as the primary interface for development and writing work.
- **Custom skills** for career OS operations, code review, session management, and content production.
- **Obsidian MCP** for direct vault access — career materials, project notes, and knowledge base.
- **File-based memory** so context accumulates across sessions rather than resetting daily.

The system is called OpenClaw. It's personal infrastructure — not a product, not a framework — just a set of conventions that makes AI assistance practical for how I actually work.

## What geospatial engineers can use this for

If you work in GIS, data engineering, or scientific software:

- **Documentation automation:** generate technical documentation from code structure + domain context from your notes.
- **Data pipeline review:** describe your pipeline in natural language, have the AI identify edge cases and failure modes.
- **Spatial analysis documentation:** connect the AI to your project files and have it describe what each processing step does.
- **Career materials:** maintain a living technical portfolio that the AI can query when tailoring applications.

The tooling is early. The patterns are forming. But the compounding effect is already real — and the gap between people who build workflows around AI and people who use it ad-hoc is widening fast.
