# Delhi SIR 2026 - ASDD Deletion List

Static search over the **ASDD list** published by the CEO Delhi: voters whose
enumeration forms were not received during SIR 2026, and whose names are
therefore **not** in the draft roll. Claims in Form 6: **31/08/2026 - 30/09/2026**.

**13 districts, 70 assemblies, 4,756,722 records, 14,947 booths.**

## Layout

- `index.html` - takes a **district name and an assembly name** and continues to
  that assembly's page. It contains **no links at all**: navigation happens on a
  button press, and the button stays disabled until both are chosen.
- `<district>/AC<NNN>_<NAME>.html` - one **self-contained** page per assembly,
  with **two tiles**. Records are embedded as gzip+base64, so a page needs no
  server and no other files. Works over HTTPS and from a local `file://` copy.

Total about 98 MB; largest single page 3.4 MB. `index.html` is ~27 KB.

## Tile 1 - search for a person

Four boxes, all optional - fill any one or more, and results must match **all**
of the ones you filled:

| Box | Minimum | Matching |
|---|---|---|
| Elector Name | 3 letters | substring, then fuzzy >= 80% |
| Guardian Name | 3 letters | substring, then fuzzy >= 80% |
| EPIC Number | 5 characters | substring |
| Booth / Part No(s) | - | exact, optional filter; accepts a list or range |

**Each box is matched only against its own column.** "KISHORE" under Elector
Name returns records whose *elector* name is Kishore, and will not return
records where only the guardian is Kishore. The two are genuinely independent.

Fuzzy matching is a banded Levenshtein with cutoff, scored against the whole
string *and* each individual word, taking the best. Exact hits rank above
fuzzy, and each row is badged `exact` or `similar 87%`.

**Type in English only** - the published rolls are English-only, so the UI is
bilingual (English / Hindi) but input cannot be in Devanagari.

## Tile 2 - show a whole booth

Pick a booth from the dropdown (which lists every part number with its booth
name) and get every deleted elector in that part. Nothing is truncated - the
largest booth in Delhi is 1,159 rows.

Every listing is printed with the **number deleted and the split by reason for
deletion** - count and percentage per reason. This tile drops the Match column,
since there is no query to match against.

Results in both tiles show booth/part number and name, S.No, EPIC, elector and
guardian name, age, gender, deletion reason, and Old Part No / Old Serial - the
last being what the BLO/BLA meeting minutes are keyed to.

## Mobile

Verified at 320 / 360 / 414 px: tiles stack to one column, the four search
boxes stack, the language toggle moves above the title, and **the page never
scrolls sideways** - the wide results table scrolls inside its own container
instead. Inputs are 16px so iOS does not zoom the page when a field is focused,
and the summary chips go two-up to keep the table near the top of the screen.

## Publishing on GitHub Pages

1. Commit this whole folder to a repository.
2. Settings -> Pages -> Source: `main` branch, root (or `/docs` if nested).
3. Share `https://<user>.github.io/<repo>/`.

`.nojekyll` is included so Pages serves the files as-is, and all links are
relative, so a repo subpath works. Well inside the 1 GB Pages limit.

Decoding uses `DecompressionStream` (Chrome 80+, Firefox 113+, Safari 16.4+).
Older browsers get a bilingual "please update" message rather than results.
