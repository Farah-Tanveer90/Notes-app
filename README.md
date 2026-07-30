# Notes App — Forms & Local Storage

A single-file notes app: create, edit, delete, and search notes, with inline validation and full persistence via `localStorage`. No dependencies, no build step — just open `index.html`.

## How I structured the localStorage data

All notes are stored under a single key (`notes-ledger:entries`) as one JSON-stringified array of note objects, rather than one localStorage key per note — that keeps reads and writes atomic, so there's never a risk of one note saving while another is mid-write. Each note is a flat object — `{ id, title, content, createdAt, updatedAt }` — where `id` is a locally generated timestamp+random string standing in for a database primary key, and `updatedAt` is an ISO timestamp refreshed on every edit and used both to render "last edited" and to sort the list newest-first. On load, the array is parsed once into an in-memory `notes` array that the UI reads and mutates directly; every add/edit/delete calls a single `saveNotes()` helper that re-serializes the whole array back to localStorage, so the on-disk shape and the in-memory shape never drift apart. Validation happens before any mutation reaches that array, so what's persisted is always a title and content that have already passed the empty-check.
