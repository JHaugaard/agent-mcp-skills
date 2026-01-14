## 1. Home Infrastructure MCP

**What it does:** Gives an agent control over your homelab.

**Tools:**

- `deploy(service, version)` — Deploy/update a Docker container on vps8
- `status(service)` — Check if a service is running, get logs
- `backup(target)` — Trigger backup of Supabase, volumes, configs
- `dns(action, record)` — Manage Cloudflare DNS records
- `ssl_status()` — Check cert expiry across domains

**Use case:** "Deploy the latest version of my blog to vps8" or "Is anything about to expire?" The agent calls tools, gets results, reports back.

------

## 2. Reading List / Bookmarks MCP

**What it does:** Manages a personal archive of saved articles and references.

**Tools:**

- `save(url, tags)` — Fetch, extract text, store with embedding
- `search(query)` — Semantic search across saved content
- `summarize(url_or_id)` — Get summary of saved item
- `list(tags, unread)` — Browse by tag or read status
- `mark_read(id)` — Update status

**Use case:** "Save this article about distributed systems" or "What did I save about database sharding?" Lightweight cousin to Knowledge MCP but single-purpose.

------

## 3. Finance / Expense MCP

**What it does:** Personal expense tracking and queries.

**Tools:**

- `log(amount, category, note)` — Record an expense
- `query(category, date_range)` — "How much on groceries this month?"
- `budget_status(category)` — Check against monthly budget
- `recurring(action, item)` — Manage subscriptions
- `export(format, date_range)` — CSV/JSON for tax time

**Use case:** "Log $45 for dinner, tag it date-night" or "Am I over budget on subscriptions?" Agent becomes your bookkeeper.

------

## 4. Calendar / Scheduling MCP

**What it does:** Bridges your calendar (Google, iCal, etc.) to agent control.

**Tools:**

- `events(date_range)` — List upcoming events
- `free_slots(date, duration)` — Find available times
- `create(title, time, duration, attendees)` — Schedule something
- `reschedule(event_id, new_time)` — Move an event
- `cancel(event_id)` — Remove event

**Use case:** "What's on my calendar tomorrow?" or "Find me 90 minutes for deep work this week." The MCP handles API auth and translation.

------

## 5. Writing Projects MCP

**What it does:** Manages drafts, notes, and publishing for a blog or newsletter.

**Tools:**

- `drafts(status)` — List drafts by status (idea, writing, review, published)
- `create(title, content, status)` — Start a new piece
- `update(id, content, status)` — Edit or advance status
- `publish(id, target)` — Push to blog, Substack, etc.
- `stats(id)` — Word count, reading time, revision history

**Use case:** "Show me drafts I haven't touched in a week" or "Publish the distributed-systems post to my blog." Agent as writing assistant with publishing powers.

------

## The Pattern

Each MCP server:

1. **Owns a domain** — infrastructure, reading, money, time, writing
2. **Exposes 4-7 tools** — focused, composable operations
3. **Hides complexity** — API auth, database queries, external services
4. **Returns structured data** — agent interprets and acts

The agent decides *what* to do. The MCP does *how*.