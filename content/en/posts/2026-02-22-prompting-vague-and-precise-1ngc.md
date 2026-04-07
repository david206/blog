---
title: "Prompting, Vague and Precise"
draft: false
devto_id: 3271276
slug: "prompting-vague-and-precise-1ngc"
cover_image: "/devto-assets/prompting-vague-and-precise-1ngc/cover-1.png"
devto_url: "https://dev.to/david_shimon/prompting-vague-and-precise-1ngc"
devto_published: false
date: "2026-02-22T13:13:29Z"
description: "I was in a session with Shiry, our sharp product manager, demoing Claude Code Web's capabilities. We..."
tags:
  - "claudecode"
  - "ux"
  - "promptengineering"
---

I was in a session with Shiry, our sharp product manager, demoing [Claude Code Web](https://claude.ai/code)'s capabilities. We chose a nice-to-have feature to work on: reflecting the active filter state in our data table.

The product has a table with dozens of columns. Users can filter rows by values in one or more columns, and they can show or hide columns as they like.

The problem: a user can end up with active filters and have no idea which columns they're filtering on. To find the filter indicator in a column header, they'd have to scroll the table horizontally, and maybe unhide columns that are currently hidden. A real game of hide and seek.

## **First Attempt: Vague about the Problem, Precise About the Solution**

I asked Claude Code to add a list of filtered fields above the table:

```
Add next to the text of "Showing 21 items", the fields we are filter 
the items by (if any). e.g. "Showing 21 items, filtered by Name and Country".
```

It wrote the code, committed, pushed. We waited for the build, checked the preview environment, and got exactly what I asked for: an ugly list of field names.

## **Second Attempt: Precise About the Problem, Vague About the Solution**

We started fresh.

Shiry, who's a master at building prototypes using LLMs, suggested I try again. This time she dictated the prompt:

```
On the items table, I want to help the user understand that there is an active filter and what it is.
(without having to look for it on the columns' headers, to scroll, or find  hidden column with filter, etc.)
```

This time we were much vaguer about the solution, but we described the problem precisely, the same way I described it at the top of this post.

The result impressed me.

Instead of a plain text list, Claude Code rendered a row of chips. Each chip showed the field name and the selected filter values. If more than two values were selected, it showed the count instead. Each chip had a small X to remove that field's filter. And when multiple filters were active, a "clear all" button appeared.

![Image description](/devto-assets/prompting-vague-and-precise-1ngc/inline-1.png)


Night and day difference. The second implementation was better by an order of magnitude. It looked familiar, it looked professional, far more useful. It looked like something a designer would design.

## **The Lesson**

When I described the solution, the model built the solution I described.

When I described the problem, the model solved the problem. And it knew a better solution than the one I thought of at first.

Be precise about the problem. Be vague about the solution.
