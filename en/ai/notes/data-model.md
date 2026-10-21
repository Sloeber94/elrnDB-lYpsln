# Data model

## Source database (`source/`, local only)

- `database/elearn_1..7.dat` are Access (Jet) databases holding the same manual in IT, EN, DE, ES, FR,
  NL and PT. Only English (`elearn_2.dat`) is used; it is exported to `source/data/elearn_2.json`.
- Key tables:
  - `ELEMENT`: 4,026 topics. The tree is built from `PARENT_ID`, and siblings are ordered by `ORDERS`.
  - `XML`: 8,616 content blocks, ordered by `ORDERS`.
  - `SECTION`: the 6 root sections, ordered by `SECTION.ORDERS`.
  - Applicability: `VALIDITY` (7 engines), `PRODUCTION` (2 periods: up to 12/05, from 01/06) and
    `CODEP` (20 equipment options). They are linked per element and per block
    (`*_VALIDITY`, `*_PRODUCTION`, `*_CODEP`); the `ALL_*` flags mean "applies to all".
- Images: `source/Image/<id>.image` files are JPEGs, or gzipped Adobe Illustrator SVGs for the wiring
  and connector drawings. The diagrams have no text layer; their labels are drawn shapes.

## Wire colours

The colour key comes from the manual's own page *Electrical equipment › 1 Electrical functions ›
Additional information › Cable color code › Key*. It is read from the database at build time
(`model.colour_table`).

| Code | Colour | Code | Colour | Code | Colour |
|---|---|---|---|---|---|
| N | black | B | white | A | light blue |
| M | brown | G | yellow | R | red |
| V | green | H | grey | S | pink |
| C | orange | Z | violet | L | blue |

- A wire colour code is one or two letters. The first letter is the main colour and the second is the
  stripe, so HR = grey with red stripe.
- `SCH` in a colour column means a shielded cable.
- Connector bodies are given by Italian colour name (NERO, BIANCO, GRIGIO, …). They are decoded too:
  `NERO (black)`.
- Any table column headed "Colour" is decoded (`content.py`). This covers pinout tables (column 2) and
  connector-body tables (column 1).

## Published data (`docs/<brand>/<model>/<language>/data`)

- `tree.json`: `{model, disclaimer, engines[], periods[], options[[code, name, count]], nodes[]}`.
  Each node is `[id, parentIndex, code, title, engineMask, periodMask, optionMask,
  [[engineMask, periodMask] per distinct block], contentSize]`, listed in document order, so parents
  come before their children.
- `tree.json` also has `popular`: for Procedures, Electrical equipment and Test, the 10 most-referenced topics
  (counted as how many other topics link to them; variants count together).
- `t/<id>.json`: `[[engineMask, periodMask, optionMask, html], ...]`, one entry per content block.
- `search.json`: plain text per node index, loaded on the first content search.
- `manifest.json`: brand, model and language (from the database's MARK, MODEL and LANGUAGE tables),
  plus the title, engine count and topic count shown on the start page.
- Bitmasks:
  - An engine or period mask covering every bit means "all".
  - The option mask lists the equipment the topic requires; all of it must be present (AND).

## Wiring is fully described in text

Every pinout row gives one wire end: pin, section, colour, and the connector and pin at the other end.
Measured on the Alfa 159; the other manuals use the same pinout tables.
- For all 4,398 wire ends, the other end's connector has its own pinout page.
- 94% are listed back from that other end. The rest are mostly continuation rows of spliced pins.
- Splices are shown as "via splice", with several destinations under one pin.

The diagrams add only:
- the routing inside junction units and control units
- component internals, such as switch type or which bulb is on which pin

Fuse ratings are not diagram-only: every model's fuse and relay index (`ai/lookup/fuses.html`) lists them
from the manual's own tables.
