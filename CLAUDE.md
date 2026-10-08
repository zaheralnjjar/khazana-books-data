# CLAUDE.md

Guidance for working in this repository.

## What this repo is

A data-only repository. It holds one file, `khazana_data.json` (~2.7 MB), with metadata about the episodes of the Arabic radio/TV program *برنامج الخزانة* (Khazana program, hosted by Sheikh Dr. Abdullah bin Salim Al-Batati) and the books discussed in those episodes. There is no code, build system, tests, or dependencies.

Remote: `https://github.com/zaheralnjjar/khazana-books-data`. Default branch: `main`.

## Files

### `khazana_data.json`

Top-level object with two arrays:

| Key | Rows | Row shape |
|---|---|---|
| `episodes` | 924 | `[episode_no, title, youtube_url]` (3 fields) |
| `books` | 13,742 | 8 positional fields (see below) |

**`episodes`**
- `episode_no` is an integer for numbered episodes (1–924). Two rows (the first two) have `null`, and they are a promo/trailer and the first episode's preview, not numbered episodes.
- `title` is Arabic, free text, and inconsistent (spacing, `||` separators, spelling of names such as `على موسي` / `علي موسى`).
- `youtube_url` is a `https://www.youtube.com/watch?v=...` link.

**`books`** (positional, no header row). Field meanings below are **inferred from the data**, not from a schema; verify before relying on them.

| Idx | Likely meaning | Observed values |
|---|---|---|
| 0 | Episode number the book was discussed in | int, 2–921 |
| 1 | Book title | string; ~132 rows are the placeholder `أسئلة المشاهدين` (viewer questions), 18 are `السؤال التفاعلي` |
| 2 | Author (sometimes with title/role text appended, e.g. `جمعا ودراسة ...`) | string or `null` (~55%) |
| 3 | Edition / series / extra note | mostly `null` (99.5%) |
| 4 | Subject (Arabic label, e.g. `الحديث`, `العقيدة`, `الفقه العام`) | string or `null` |
| 5 | Subject category code (`أخرى`, `فقه`, `حديث`, `عقيدة`, `تفسير`, …) | 5 distinct values; mirrors idx 4 |
| 6 | Publisher **or** work type (`مقاربة`) — mixed | string or `null` |
| 7 | Flag/count, almost always `1` | int or `null` (~1.5%) |

Caveats:
- Idx 6 is inconsistent: some rows hold a publisher (e.g. `اللؤلؤة - مصر`, `كنوز أشبيليا`), others a work-type word (`مقاربة`).
- Idx 2 sometimes contains the title's subtitle or a "تحقيق/جمعا ودراسة" phrase rather than a clean name.
- Many values are `null`; do not assume a field is always populated.

## Working on the data

- The file is large, so avoid printing it whole. Use `python3 -c` with `json.load` and slice what you need, or `jq`.
- Keep the file valid UTF-8 JSON. Write with `ensure_ascii=False` to preserve Arabic, and keep the top-level key order (`episodes`, then `books`).
- Do not reorder rows unless asked. Row order probably reflects discovery/episode order, and downstream consumers may depend on it.
- Preserve the existing `null` and placeholder conventions. Do not "clean" spelling or spacing in titles without an explicit request, since that changes many rows at once.
- If you add or change a field, document it in the table above.

## Git

- Develop on the branch assigned for the session; do not push to `main` without explicit permission.
- Commit messages should describe the data change (e.g. "Add episodes 925–930").
