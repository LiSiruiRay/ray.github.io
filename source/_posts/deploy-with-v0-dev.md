---
title: deploy with v0.dev
date: 2024-10-13 01:36:55
tags:
---

I am recently attending hackathon at Harvard. I was doing a project on personal assistant called [moodeng](https://github.com/LiSiruiRay/moodeng), in the process I was trying to use AI tools as much as possible.

I started trying [cursor](https://www.cursor.com/) but haven't get chance to delve deep.

I used a lot of [v0.dev](https://v0.dev/) since I suck at front end, and this really took me a while to debug and deploy. The tool itself is powerful but it require you to read the correct reources and fix some subtle bug:
- Don't ask ChatGPT. Their knowledge is too old. I tried doing this for several days but it won't work. Yet O1 somehow indeed provided some [useful guidence](https://chatgpt.com/share/6709f8d2-b314-8013-be0d-aedeeffb7c4d).

If you have no clue where to go. You never deployed any react app, then I suggest you to ask GPT cuz it caters every level of developer. Yet if you have some general idea of Javascript and have a touch on react app before, I recommend you to read those:

- [docs](https://v0.dev/docs#adding-v0-components)
- [shadcn: installation](https://ui.shadcn.com/docs/installation/manual)
- [shadcn: cli](https://ui.shadcn.com/docs/cli)
- [shadcn: toast](https://ui.shadcn.com/docs/components/toast)

Also, remember sometimes v0 generate the code like
```typescript
import { useState } from 'react'
import { format, addDays, isSameDay, parseISO } from 'date-fns'
import { Button } from "@/components/ui/button"
import { ScrollArea } from "@/components/ui/scroll-area"
import { toast } from "@/components/ui/use-toast"
```

For the code like these, you need to do the
```bash
npx shadcn@latest add scroll-area  use-toast
```
and
```bash
npx shadcn@latest add toast
```

Don't trust ChatGPT's suggest for doing `npx shadcn-ui@latest init`!!


After that, you need to change the line
```typescript
import { toast } from "@/components/ui/use-toast"
```
to 
```typescript
import { toast } from "@/hooks/use-toast";
import { Toaster } from "@/components/ui/toaster";
```