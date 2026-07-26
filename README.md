# learningmath-assets

Public asset repo for [learningmath.co.za](https://learningmath.co.za) — hosts every past exam
paper PDF, served to the site via the [jsDelivr CDN](https://www.jsdelivr.com/) rather than
Supabase Storage. Supabase only stores metadata (grade, year, paper type, category) and the
resulting jsDelivr URL — never the file itself.

## Folder structure

```
national/<grade>/<year>/                       e.g. national/grade-12/2023/
prelims/grade-12/<province>/<year>/            e.g. prelims/grade-12/gauteng/2023/
midyear/<grade>/<school>/<year>/               e.g. midyear/grade-11/st-johns/2023/
timetables/grade-12/<board>/<year>/            e.g. timetables/grade-12/dbe/2026/
```

- `national` — official DBE/IEB November final exam papers, Grade 8–12, 2016–2026.
- `prelims` — provincial preliminary ("prelim"/"trial") exams, Grade 12 only, by province.
- `midyear` — school-level June/midyear exams, Grade 8–12, by individual school.
- `timetables` — the official Grade 12 exam timetable PDF per board, per year.

## File naming convention

Drop PDFs directly into the matching leaf folder (each one currently holds a `.gitkeep`
placeholder — delete it once real files are added, or leave it, it's harmless). **No renaming
required** — the seed script (`tmc-learningmath/scripts/seed-past-papers.mjs`) understands the
real-world filenames straight from DBE/IEB sources:

```
Mathematics P1 May-June 2025 Eng.pdf          -> paper1
Mathematics P1 May-June 2025 MG Afr & Eng.pdf -> memo1   (MG = Marking Guideline)
Mathematics P2 Nov 2025 MG Afr & Eng.pdf      -> memo2
timetable.pdf                                  (inside timetables/... only)
```

It detects the paper number from `P1`/`P2`/`P3` and treats a filename as a memo if it contains
the `MG` token — anywhere else, it's treated as a paper. Language tags (`Eng`, `Afr & Eng`,
etc.) are ignored entirely. Our own shorthand convention (`maths-paper1.pdf` / `maths-memo1.pdf`)
still works too and takes priority if present.

## Serving via jsDelivr

Any file pushed to `main` is instantly available at:

```
https://cdn.jsdelivr.net/gh/CS-TG/learningmath-assets@main/<path-from-repo-root>
```

jsDelivr caches aggressively (~7 days) — if you replace a file with the same name, purge the
cache at https://www.jsdelivr.com/tools/purge or append `?v=2` etc. in a pinch.

## Workflow

1. Drop new PDFs into the appropriate leaf folder (matching the naming convention above).
2. `git add -A && git commit -m "add: <describe what you added>" && git push`
3. Run `node scripts/seed-past-papers.mjs` from `tmc-learningmath` to sync the new files'
   metadata into Supabase (`lm_past_papers`). Safe to re-run — it upserts by GitHub path.
