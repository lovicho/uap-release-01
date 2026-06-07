# uap-release-01

A mirror of the war.gov "PURSUE" UFO/UAP release — **219 content files** (126 PDFs, 71 MP4 videos, 14 images, plus 8 placeholders), **3.83 GB** in Git LFS. Pulled from <https://www.war.gov/UFO/>, first on 2026-05-08 and kept in sync by a weekly watcher since.

This is a **mirror of public-domain US government documents**, hosted here as the canonical example dataset for the [`uap-release-analyzer`](https://github.com/ckpxgfnksd-max/uap-release-analyzer) skill. It lets anyone reproduce the eval scoreboard against the same input the skill was tuned on, without scraping war.gov themselves.

> **Last sync:** 2026-06-07. war.gov now exposes two tranches — the original **release_1** and a **release_02** (dated 05-22-26). All 136 downloadable medialink documents across both are mirrored. The latest run added 8 new DOW videos (PR092–PR099) as placeholders — see [What's pending](#whats-pending).

## Composition

| Agency | Files | Notes |
|---|---|---|
| DOW (Dept of War, formerly DoD) | 123 | Mission reports + range-fouler debriefs (PDF), 70 Unresolved UAP Report videos `dow-uap-prNN.mp4` (PR19–PR91, with gaps), and 8 PR092–PR099 placeholders (pending) |
| NARA (numeric record groups) | 44 | RG 18, 38, 59, 65, 255, 331, 341, 342 — historical, mostly scanned-only |
| FBI | 32 | 62-HQ-83894 case-file sections, FD-302 reports, sensor photos, 2024 composite sketch |
| NASA | 13 | Apollo / Skylab / Gemini transcripts and crew debriefings (incl. the `255_t_763_r1b_excerpt.mp4` excerpt) |
| DOE | 3 | Pantex image, Tuck correspondence, Pajarito astronomers |
| DOS | 2 | Embassy cables (Papua New Guinea 1985, Kazakhstan 1994) |
| CIA | 1 | Intelligence Information Report, USSR, 1973 |
| ODNI | 1 | USPER narrative, senior USIC official |
| **Total** | **219** | |

*(Agency buckets are by filename prefix and approximate at the margins.)*

### Format split

- **126 PDFs** (2.48 GB) — text-bearing and scanned-only (the latter need OCR for content)
- **71 MP4 videos** (1.33 GB) — DOW Unresolved UAP Reports (`dow-uap-prNN.mp4`, PR19–PR91) + one NASA excerpt (`255_t_763_r1b_excerpt.mp4`). Sourced from war.gov's DVIDS/CloudFront pipeline. Largest is 275 MB (`dow-uap-pr42.mp4`)
- **14 images** (8 PNG + 6 JPG) — FBI sensor frames and the 2024 composite sketch. Need vision analysis, not OCR
- **8 placeholders** (`*.viewer-only.txt`) — see below

## What's pending

release_02 introduced 8 new DOW Unresolved-Report videos, **PR092–PR099**, that are DVIDS-streamed with no per-file war.gov download URL (all medialink video paths return 404). They are tracked here as `dow-uap-prNN.mp4.viewer-only.txt` placeholders carrying each record's DVIDS Video ID and resolution instructions.

To resolve real bytes, map each record's DVIDS Video ID to its DOD asset id (via the war.gov lightbox modal or the DVIDS API), then fetch from the same CloudFront path that brought in PR19–PR91:

```
https://d34w7g4gy10iej.cloudfront.net/video/<group>/DOD_<id>/DOD_<id>.mp4
```

The full release_02 video set also exists inside the combined bundle `https://d34w7g4gy10iej.cloudfront.net/uap052226.zip` (5.64 GB), but its entries are named by internal DOD asset id (`video_2605_DOD_<id>_DOD_<id>.mp4`), so per-file mapping is still required before mirroring with faithful filenames.

## How to use

Clone with LFS:

```bash
git lfs install              # one-time setup if you don't have LFS
git clone https://github.com/ckpxgfnksd-max/uap-release-01.git ~/Documents/UFO/release_01
```

Then run the analyzer:

```bash
git clone https://github.com/ckpxgfnksd-max/uap-release-analyzer.git
python uap-release-analyzer/scripts/run_all.py ~/Documents/UFO/release_01
open ~/Documents/UFO/release_01/REPORT.md
```

To skip downloading the corpus immediately (clone metadata only, fetch files on demand):

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/ckpxgfnksd-max/uap-release-01.git
cd uap-release-01

git lfs pull --include "dow-uap-d27*"        # one specific PDF
git lfs pull --include "dow-uap-*.pdf"       # all DOW mission report PDFs
git lfs pull --include "dow-uap-pr*.mp4"     # all 70 DOW Unresolved-Report videos
git lfs pull --include "65_hs1*"             # the heavy FBI scanned sections
```

## Provenance

- **Source:** <https://www.war.gov/UFO/>
- **PDFs + images first pulled:** 2026-05-08
- **Videos (PR19–PR49) added:** 2026-05-09 — fetched directly from `d34w7g4gy10iej.cloudfront.net/video/<group>/DOD_<id>/DOD_<id>.mp4` after each record's DVIDS asset id was discovered via the war.gov lightbox modal. War.gov serves these via inline viewer + client-side HLS-to-MP4 assembly; the direct CloudFront URLs bypass that and return the pre-assembled MP4 in seconds.
- **Subsequent weekly watcher runs:** extended the DOW videos through PR91 and merged the **release_02** document tranche (`/medialink/ufo/052226/release_02/`).
- **2026-06-07:** detected release_02's 8 new DOW videos (PR092–PR099) — added as placeholders (no clean per-file URL resolved this run); flagged one document, `dow-uap-d20-mission-report-southern-united-states-2020.pdf`, that no longer resolves on war.gov (likely a rename) for review — see issue #3. Nothing is ever auto-deleted.
- **No re-redactions or transformations** have been applied. Files are as-released by war.gov.

For the scrape mechanics (DOM-only pagination, blob URLs, synthetic-click downloads), see [war_gov_quirks.md](https://github.com/ckpxgfnksd-max/uap-release-analyzer/blob/main/references/war_gov_quirks.md) in the analyzer repo.

## License / copyright

US federal government works are not eligible for copyright under [17 U.S.C. § 105](https://www.copyright.gov/title17/92chap1.html#105) and are in the public domain in the United States. Foreign copyright treatment may vary; check your jurisdiction if redistributing outside the US.

This repository carries no additional copyright claim by Chase Wang or contributors.

## Storage notes

This corpus uses Git LFS — **3.83 GB across 211 binary files** (126 PDFs · 2.48 GB, 71 videos · 1.33 GB, 14 images). The 8 `*.viewer-only.txt` placeholders are plain text and not in LFS. Several files exceed GitHub's 100 MB single-file cap — the largest are a 371 MB FBI scan section (`65_hs1-834228961_62-hq-83894_section_6.pdf`) and a 275 MB DOW UAP video (`dow-uap-pr42.mp4`) — which is why LFS is required.

If you don't need the heaviest scanned NARA/FBI files (they don't have text layers anyway and the skill flags them as "OCR-required" rather than analyzing them) or the larger videos, the partial-clone form above lets you fetch only the buckets you care about.

## Sync to upstream

Updates from war.gov are picked up automatically by the `war-gov-uap-watcher` scheduled task (weekly, Sunday 9 AM local). It crawls war.gov with a headless browser (Akamai 403s plain curl), diffs the live `uap-data.csv` against this mirror, auto-mirrors any new downloadable files via Git LFS, and leaves `*.viewer-only.txt` placeholders for inline-viewer records with no clean URL. Files that vanish from war.gov are flagged in an issue for review, never auto-deleted. If you spot a delta the watcher missed, open an issue here.
