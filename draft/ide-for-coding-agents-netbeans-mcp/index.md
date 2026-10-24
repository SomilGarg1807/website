---
title: "An IDE for Coding Agents: NetBeans Refactorings over MCP"
date: "2026-01-01"
description: "Coding agents rename methods with grep and edit loops. A headless Apache NetBeans gives them real refactorings over MCP, with fewer turns and tokens."
authors:
  - "eppleton"
image: "hero.jpg"
categories:
  - "NetBeans"
  - "AI"
  - "Developer Tools"
canonical: "https://dukehoff.com/en/blog/dukehoff-mcp-for-java/"
---

Imagine being a Java developer in 1996, before modern IDEs, with nothing but command-line
tools and maybe Notepad. To rename a method, you search for its name, then open each of
maybe 22 files to check whether a hit refers to that method or to an unrelated one
with the same name, and replace each occurrence with sed or manually. You get there eventually,
and once it compiles, you're probably done. It just takes a lot of steps and time.

You may be surprised, but that's pretty much how coding agents work in 2026 when you ask them to rename a method.

<video controls muted playsinline preload="none" width="1280" height="804" poster="rename-without.jpg">
  <source src="rename-without.mp4" type="video/mp4">
</video>

*Without the server: Claude Code with Sonnet 5.5 renames a method in Apache Commons Lang. 32 s model time, 994 tokens.*

Dukehoff MCP for Java gives the agent the analysis and refactoring operations of
an IDE as tools: find usages, rename, move, change signature, safe delete, inline.
The agent passes the method's signature to the rename tool, and the server finds the method,
renames it everywhere, overrides included, writes the files, and returns the diff plus
a compile check that names any new errors.

<video controls muted playsinline preload="none" width="1280" height="804" poster="rename-with.jpg">
  <source src="rename-with.mp4" type="video/mp4">
</video>

*With the server: the same task. 10 s model time, 230 tokens.*

Some Java IDEs have integrated tools like this, but the way we develop software has
changed. Many developers now start an agent in a terminal, let it write the code,
and review the result in the pull request. Dukehoff MCP for Java runs headless,
so your agent doesn't need a running IDE to use these tools. And when you do work
in an IDE, the same tools are there too: in VS Code next to Copilot, or in a running NetBeans.

Be nice to your agent and give it the right tools for the tedious, repetitive and
error-prone tasks. The server is open source, and for exactly these tasks it saves
you and your agent turns, tokens and time.

## What it saves

- **Rename a method used in 22 files** (NetBeans, `java/` folder, Opus 5.5): 9 turns instead of 51, $0.22 instead of $0.56.
- **Find the callers of one overload** (Apache Commons Lang, Sonnet 5): 91% less cost, 85% less time.
- **Modernize 84 places among 144 candidates** (told to use the rules, Sonnet 5.5): 40% fewer turns, about 22% less cost and 39% less time than writing its own scripts.

Each figure compares the same agent with and without the server.

These are real numbers we measured with Claude models. Find the details
in [eval/RESULTS.md](https://github.com/dukehoff-gmbh/dukehoff-mcp/blob/main/eval/RESULTS.md).

We are not alone with this idea. Netflix and JetBrains recently reported that giving
agents the headless code intelligence of IntelliJ IDEA made them typically 20–30% more efficient,
and up to about 65% in the best case
([JetBrains blog](https://blog.jetbrains.com/ai/2026/10/how-netflix-made-ai-coding-agents-more-efficient/)).
Their tooling is proprietary: the editor extension needs an IntelliJ IDEA Ultimate subscription,
and support for terminal agents is still to come. Dukehoff MCP for Java is open source, free,
and works today with Claude Code, Codex, Copilot and others, in the terminal, in VS Code and in NetBeans.

## Why we hit the Jackpot with NetBeans

Apache NetBeans is free and open source, and it understands Java with javac itself,
the same compiler your build uses: types, overloads and overrides are resolved exactly,
not approximated. It runs without a window just as well; its LSP server, which powers
the Apache NetBeans extension for VS Code, does that every day. And it has a mature
refactoring engine, built over more than twenty years.

Beyond that, NetBeans has a hidden gem: Jackpot, a rule-based rewrite engine.
A Jackpot rule matches code by its structure and types, not by its text, so a
migration changes what it should and leaves look-alikes alone.
Agents can write such rules themselves, and we ship tested rule sets for common migrations:
JUnit 4 to 5, Java modernization, and javax to jakarta.

## Get it

Dukehoff MCP for Java is free and open source under the Apache License 2.0. Pick the way that fits how you work:

- **Claude Code, Codex, Copilot CLI and other terminal agents:** download `dukehoff-mcp-<version>.tar.gz` (macOS, Linux) or `.zip` (Windows) from the [latest release](https://github.com/dukehoff-gmbh/dukehoff-mcp/releases/latest), unpack it, and let it print the registration for your agent: `dukehoff-mcp --config claude` (see the [installation guide](https://github.com/dukehoff-gmbh/dukehoff-mcp#installation)).
- **VS Code with GitHub Copilot:** install *Dukehoff MCP for Java* from the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=dukehoff.dukehoff-mcp-java), or search for "Dukehoff MCP" in VS Code's Extensions view. It brings the Apache NetBeans extension along; the tools appear in Copilot's agent mode.
- **NetBeans:** download `dukehoff-mcp-netbeans-<version>.nbm` and `dukehoff-mcp-launcher-<version>.jar` from the [latest release](https://github.com/dukehoff-gmbh/dukehoff-mcp/releases/latest), install the plugin under Tools > Plugins > Downloaded, and connect your terminal agent to the running IDE with `--ide` (see [using it with a running NetBeans IDE](https://github.com/dukehoff-gmbh/dukehoff-mcp#with-a-running-netbeans-ide)).

You need a JDK 17 or newer; VS Code 1.101 or newer for the extension, NetBeans 31 or
newer for the plugin. To make your agent reach for the tools on its own, add the short
section from [Telling the agent when to use the tools](https://github.com/dukehoff-gmbh/dukehoff-mcp#telling-the-agent-when-to-use-the-tools) to
your `CLAUDE.md` or `AGENTS.md`.

If you have a migration where the meaning of a change depends on types,
such as an in-house framework, a vendor library swap or a Java EE to Jakarta EE
move beyond the APIs, we can help: [get in touch with Dukehoff](https://dukehoff.com/en/contact/).

---

*This article was first published on [dukehoff.com](https://dukehoff.com/en/blog/dukehoff-mcp-for-java/).*

Dukehoff MCP for Java runs on Apache NetBeans. It is not part of, nor endorsed by, the Apache
NetBeans project or the Apache Software Foundation. "Apache", "NetBeans" and "Apache NetBeans" are
trademarks of the Apache Software Foundation.
