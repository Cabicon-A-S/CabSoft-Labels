# CabSoft-Labels

The label library of CabSoft V6: one folder per CabMark label, with the label's size, its print settings and its artwork. CabSoft V6 builds these labels into the program and reads them in the folder layout used here, so nothing is converted on the way.

This repository holds 126 labels in six families:

| Family       | Labels | Artwork                                      |
| ------------ | -----: | -------------------------------------------- |
| CabMark POL  |     44 | four SVG layers                              |
| CabMark CPL  |     24 | four SVG layers                              |
| CabMark ZHS  |     22 | four SVG layers                              |
| CabMark CMW  |     16 | four SVG layers                              |
| CabMark ZHCO |     11 | none: endless material, `template.json` only |
| CabMark CMP  |      9 | four SVG layers                              |

## Folder layout

```
<Family>/Labels/<Label name>/
    template.json
    Label.svg
    Fill.svg
    Print limitation.svg
    Transparent.svg
```

- The first folder is the family, the second the subcategory (`Labels` for every label today). The program takes both from the path.
- A label's id is its path, for example `CabMark ZHS/Labels/CabMark ZHS 3,2x12,5`. Saved documents refer to labels by it, so renaming or moving a folder makes it a different label.
- A folder is a label when it holds a `template.json`. Any other file in a label folder fails the program's library checks.
- A folder named `[Legacy]` is never loaded.

## The files

- `template.json`: the label, in five groups of settings.
  - `identity`: `itemno`, `description` (the name the New Label window shows), `status` (`wip` or `done`) and, with `done`, `calibrated-against`: the printer, its print offsets, the panel heat and the date the label was verified on. `done` holds for that printer only.
  - `geometry`, in millimetres: `label-width`, `label-height`, `liner-width`, `margin-left`, `margin-right`, `rowspacing`, `colspacing`, `columns`. The margins, the labels and the column gaps add up to `liner-width`, and `label-height + rowspacing` is the real row pitch of the material.
  - `print`: `sensor-type` (`gap`, `continuous`, `upper-marker`, `lower-marker`), `print-speed` in mm/s, `print-darkness` (-20 to +20, added to the printer's panel setting).
  - `feed`: `backfeed-enabled`, `backfeed-speed`, `tear-off-enabled`, `rotate-180`, and optionally `waiting-position` and `form-feed-count`.
  - `cutter`: `cutter-mode` (0 never, 1 after the job, 2 every `cutter-interval` labels) and `cutter-interval`.
- `Label.svg`: the label's outline, drawn under the user's design. In its first path, the first closed part is the outer edge and any further closed parts are holes.
- `Fill.svg`: the label's fill colour.
- `Print limitation.svg`: the area the printer cannot reach, shown when the user turns on the print limitations. The name is spelled as the program reads it. Older folders used the misspelling `Print limination.svg`; the program still accepts it in a user's own labels, but every label here uses the correct name.
- `Transparent.svg`: the label's see-through parts.

Files are kept byte for byte with LF line ends. `.gitattributes` turns line-end conversion off, because CabSoft V6 compares its copy with this repository's tree.

Print offsets are not set per label. They belong to the printer profile in the program (EOS5/300: x 0.0 mm, y -0.70 mm, measured on 2026-09-26).

## Adding or changing a label

1. Copy the folder of a similar label to `<Family>/Labels/<Label name>/` and edit `template.json`: the description, the geometry, and the print and feed settings. Set `status` to `wip` until the label has been printed and checked.
2. Replace the four SVG files with the label's artwork, at the label's size in millimetres. An endless material may have none.
3. On the real printer, print a rectangle that covers the whole label for a few rows. Adjust `margin-left` (sideways) and the row pitch until it sits on the die-cut edges. Then set `status` to `done` and fill in `calibrated-against`.
4. Commit and push to this repository.

## How CabSoft V6 takes the labels

The CabSoft V6 build never downloads anything. Its repository keeps a copy of these folders and a pin: this repository's URL, a commit and that commit's tree id. A check in CabSoft V6 fails when the copy differs from the pinned tree by a single byte.

To bring a change into CabSoft V6: push it here, set the pin in CabSoft V6 to the new commit, run its label sync script to refresh the copy, and run its tests. Those tests check every label against the rules the program enforces and expect the family counts above, so a new label also needs the counts updated there.
