---
title: "The Mystery of the Invisible Tab: A YAML Debugging Tale"
draft: false
devto_id: 3023493
slug: "the-mystery-of-the-invisible-tab-a-yaml-debugging-tale-3e3l"
cover_image: "/devto-assets/the-mystery-of-the-invisible-tab-a-yaml-debugging-tale-3e3l/cover-1.png"
devto_url: "https://dev.to/david_shimon/the-mystery-of-the-invisible-tab-a-yaml-debugging-tale-3e3l"
devto_published: false
date: "2025-11-15T17:14:25Z"
description: "TL;DR   Spent hours debugging why my OpenFGA CLI config file wasn't working. Turned out YAML..."
tags:
  - "openfga"
  - "debugging"
  - "yaml"
  - "cli"
---

## TL;DR
Spent hours debugging why my OpenFGA CLI config file wasn't working. Turned out YAML doesn't support tabs. Good error messages matter.

## The Setup

This week, I started using [OpenFGA](https://openfga.dev/), an open-source authorization system. It offers multiple ways to configure connection settings:
- Command-line flags
- Environment variables  
- A YAML configuration file (`.fga.yaml`)

The YAML option caught my attention. Why? Because I work with multiple OpenFGA servers. With YAML configs, I could create separate `.fga.yaml` files in different directories and just run the CLI from the appropriate folder. Elegant!

Or so I thought.

## The Problem

I created my config file, added the server URL, API token, and other settings. Ran the CLI. Nothing.

The CLI completely ignored my configuration file and fell back to defaults. No errors. No warnings. Just... silence.

## The Debugging Session

Enter debugging mode - with some LLM assistance, naturally. After lots of trial and error:
- ✅ The file path was correct
- ✅ The YAML syntax looked valid
- ✅ All values were properly formatted

Yet somehow, it didn't work.

Plot twist: I created a config file for my local environment, and **that one worked perfectly**. Same format, same structure, different values. Very strange!

At this point, I cloned the OpenFGA CLI repository, ready to hunt down this mysterious bug in the source code.

## The Breakthrough

I asked Cursor to generate a sample config file. After replacing the values with my own, this generated file worked flawlessly.

Time for a `diff`:
```bash
diff working-config.yaml my-config.yaml
```

The output? Two "blank lines" at the end of my file. I deleted those lines, and suddenly everything worked.

But wait - if I added blank lines back to the working file, it continued to work fine. What was going on?

## The Culprit

Closer inspection revealed the truth: those weren't blank lines. One of them contained a **tab character**.

I discovered that YAML doesn't support tabs. In fact, it's [FAQ #2 on yaml.org](https://yaml.org/faq.html) (right after "What's the official file extension?").

Mystery solved. The culprit found. And as usual in these stories: it was me.

## The Lesson: Error Messages Matter

Here's what bothered me most about this experience: **the CLI found my config file but silently ignored it when parsing failed**.

If a configuration file exists but contains invalid syntax, the tool should tell me! A simple error message:

```
Error: Failed to parse configuration file '.fga.yaml'
YAML parsing error: tabs are not allowed
```

That's why I [opened an issue](https://github.com/openfga/cli/issues/608) suggesting this improvement.

## The Takeaway

Good error messages are crucial for Developer Experience (DX). When building CLI tools:

1. **Fail loudly when something is wrong** - Silent failures lead to frustrating debugging sessions
2. **Be specific** - "Config file not found" vs "Config file found but failed to parse"  

This is especially important for configuration files that developers might edit in different environments, with different editors, and different settings.

## In Conclusion

YAML doesn't like tabs. CLI tools should tell you when your config is broken. And when in doubt, blame the developer (me) first - but make it easier for developers to catch their mistakes.

Have you had similar debugging experiences with invisible characters or silent failures? Share your stories in the comments!
