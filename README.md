# universe-data

Star catalog tiles for **[Universe Atlas](https://universeatlas.org/)**
([chrisjz/universe](https://github.com/chrisjz/universe)) — served straight
from this repo via GitHub Pages at
`https://data.universeatlas.org/`.

## Contents

```
stars/
  manifest.json      chunk index (files, star counts, source attribution)
  chunk-*.bin        LOD 0 — ATHYG brights (mag ≤ 11), brightest first
  gaia-b<B>-p<P>.bin LOD 1+ — Gaia DR3 band B (11–11.5, 11.5–12, 12–12.5,
                     12.5–13), HEALPix level-0 sky tile P (0–11)
sdss/
  manifest.json      band index (files, galaxy counts, redshift ranges)
  band-<k>.bin       SDSS DR18 spectroscopic galaxies in redshift bands
                     (0.01–0.08, 0.08–0.15, 0.15–0.3, 0.3–0.5, 0.5–1.0),
                     12-byte records: f32 ra°, f32 dec°, f32 z
```

Each tile is a run of 16-byte little-endian star records:

```
f32 x, y, z   position, scene meters (sun frame, pre-orientation convention)
u8  r, g, b   color from B−V / BP−RP via blackbody temperature
u8  s         absolute magnitude, s = (absmag + 15) · 8
```

The atlas streams tiles brightest-first from `manifest.json`; the tile
scheme is hierarchical LOD (magnitude bands) × spatial (HEALPix level-0),
so a session only downloads what it renders. Regenerate with
[`scripts/generate-gaia-tiles.mjs`](https://github.com/chrisjz/universe/blob/main/scripts/generate-gaia-tiles.mjs)
in the main repo (chunked, resumable download from ESA's TAP archive).

The SDSS tiles come from SkyServer's SpecObj table (class GALAXY,
zWarning 0, 0.01 ≤ z ≤ 1.0; the app converts redshift to comoving
distance with its own ΛCDM) — regenerate with
[`scripts/generate-sdss.mjs`](https://github.com/chrisjz/universe/blob/main/scripts/generate-sdss.mjs).
Funding for SDSS: the Alfred P. Sloan Foundation and participating
institutions — [sdss.org](https://www.sdss.org/).

## Data credits & licenses

- **Gaia DR3** — This work has made use of data from the European Space
  Agency (ESA) mission [Gaia](https://www.cosmos.esa.int/gaia), processed by
  the Gaia Data Processing and Analysis Consortium
  ([DPAC](https://www.cosmos.esa.int/web/gaia/dpac/consortium)). Funding for
  the DPAC has been provided by national institutions, in particular the
  institutions participating in the Gaia Multilateral Agreement. Credit:
  **ESA/Gaia/DPAC**, [free use with attribution](https://www.cosmos.esa.int/web/gaia-users/credits).
  Cut: `11 ≤ G < 13`, `parallax_over_error > 5`; distances are `1/parallax`.
- **ATHYG v3.2** — [astronexus/ATHYG-Database](https://github.com/astronexus/ATHYG-Database)
  (Tycho-2 + Gaia DR3 merge), **CC BY-SA 4.0**.

The tile data in `stars/` is derived from both catalogs and is provided
under **CC BY-SA 4.0**.
