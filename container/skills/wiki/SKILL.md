# Wiki Skill

You maintain a persistent, compounding knowledge wiki. Sources go in, structured knowledge comes out.

## Architecture

- `sources/` — raw, immutable source material (articles, PDFs, images, transcripts). Never modify these.
- `wiki/` — your maintained knowledge base. You own this entirely: create pages, update cross-references, resolve contradictions.
- `wiki/index.md` — content catalog of every wiki page (link + one-line summary), organized by category. Read this first when answering queries.
- `wiki/log.md` — append-only chronological log of all operations.

## Operations

### Ingest

When the user provides a source (URL, file, text, image, voice note):

1. Save or download the raw source to `sources/` (immutable copy)
2. Read it thoroughly and discuss key takeaways with the user
3. Create or update wiki pages:
   - **Summary page** for the source itself
   - **Entity pages** for people, organizations, tools, projects mentioned
   - **Concept pages** for ideas, patterns, techniques
   - Cross-reference all related existing pages (add links in both directions)
4. Update `wiki/index.md` with new/changed pages
5. Append to `wiki/log.md`: `## [YYYY-MM-DD] ingest | Source Title`

**Critical: one source at a time.** When given multiple files or a folder, process each source individually and completely (read, discuss, create all pages, update index and log) before moving to the next. Never batch-read then batch-process — this produces shallow, generic pages.

#### URL sources

For URLs where full text matters, download the content rather than using WebFetch (which returns summaries):

```bash
curl -sLo sources/filename.html "https://example.com/article"
```

For webpages, use `agent-browser` to open the page and extract full text if available.

### Query

When the user asks a question:

1. Read `wiki/index.md` to find relevant pages
2. Read those pages and synthesize an answer with citations
3. If the answer is substantial and reusable, offer to file it as a new wiki page (explorations compound rather than disappearing into chat)

### Lint

Periodic health check of the wiki:

1. Scan for contradictions between pages
2. Find orphan pages (no inbound links)
3. Identify important concepts lacking dedicated pages
4. Check for stale claims superseded by newer sources
5. Look for missing cross-references
6. Suggest sources to pursue for gaps
7. Report findings and offer to fix issues
8. Log: `## [YYYY-MM-DD] lint | Summary of findings`

## Page conventions

- Use standard markdown with YAML frontmatter (title, date, source, tags)
- Organize wiki pages in subdirectories by category when they grow beyond ~20 pages
- Link between pages using relative markdown links: `[Entity Name](../entities/entity-name.md)`
- Keep pages focused — one concept/entity per page, split when they grow large
