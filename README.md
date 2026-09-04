# social-reader

**Marginalia** — read a book alongside a friend. Share where you are in the book,
and start a conversation pinned to any page.

Live app (Claude Artifact): https://claude.ai/code/artifact/ea65f71d-b9f3-4b55-86eb-cd20b125b868

## How it works

1. Enter your name (stored in your browser).
2. Create a reading: title, author, page count. You get a six-character code.
3. Send the code to whoever you're reading with; they enter it to join.
4. Everyone updates their current page, and can leave a note on any page.
   Notes are threaded — replies live under the note.

Notes on pages ahead of you are hidden behind a spoiler guard by default.

## About `index.html`

`index.html` is the app, written as a **Claude Artifact body fragment** — it has no
`<!doctype>`, `<html>`, `<head>` or `<body>` tags, because the Artifact runtime wraps
it at publish time. It uses the artifact `db` capability (`claude.use("db")`) for the
shared store, so it will *not* work as a plain static page on GitHub Pages: without
`window.claude` there is no shared database and it renders a "not connected" screen.

To publish an updated copy, republish this file as an Artifact with
`capabilities: {db: {}}`.

### Data model

```
sessions/{CODE}                    { title, author, totalPages, createdBy, createdAt }
sessions/{CODE}/readers/{readerId} { name, page, updatedAt }
sessions/{CODE}/threads/{id}       { page, authorId, authorName, text, createdAt }
sessions/{CODE}/replies/{id}       { threadId, authorId, authorName, text, createdAt }
```

Replies are a flat sibling collection rather than an array on the thread, because the
store is last-writer-wins and two friends replying at once would clobber an array.
