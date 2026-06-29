# Bitbake Recipe Cartographer

An interactive, single-file viewer for BitBake/Yocto build-dependency graphs.
Drop a `task-depends.dot` (from `bitbake -g`) onto the canvas and explore which
recipes depend on a given recipe, how they chain, and which layers own them.

Everything runs client-side in your browser. **No file is uploaded anywhere** —
the `.dot` you drop is read locally and never leaves the page. The only external
request is loading the D3 library from a CDN.

## Use it

Open `recipe-cartographer.html` in a browser (or the hosted page), then:

1. Generate a graph in your build directory:
   ```
   bitbake -g <your-image-or-recipe>
   ```
   This writes `task-depends.dot` (plus `pn-buildlist` and `recipe-depends.dot`)
   to the current directory.
2. Drag `task-depends.dot` onto the canvas.
3. Type the recipe you want at the centre of the graph into **root recipe** and
   press Enter. Every recipe that can reach it (build-time `DEPENDS`) is shown.

## What it shows

- **Build-time dependency edges** (`DEPENDS`), not runtime `RDEPENDS`. An edge
  `A → B` means *A build-depends on B*.
- **Role** of each consumer relative to the root: direct (depends straight on
  the root), indirect (reaches it only through another recipe), or a sink
  (image / packagegroup).
- **Owning layer** (the `meta-*` the recipe lives in), read from the `.bb` path
  in the dot labels.
- **In-degree** (how many recipes depend on a node) as dot size and as a heat
  colour mode — big/hot nodes are hubs.
- **Leaves**: recipes nothing else depends on — the low-risk things to change
  first.

## Views & controls

- **Layout**: force-directed (explore), by distance (columns by hop-count from
  the root — reads as a porting/refactor order), or by layer (columns per
  owning layer).
- **Colour**: by role, by layer/team, or by in-degree heat.
- **Filter**: show/hide indirect consumers, images, packagegroups, or restrict
  to leaves only. Images and packagegroups are hidden by default to reduce
  clutter.
- **Click a node** to highlight its full path(s) to the root and see its direct
  dependencies.
- **Search** to highlight recipes by name. **Scroll** to zoom, **drag** to pan.
- **Theme**: follow system, light, or dark.

## Notes

- Images are detected by their `.bb` living under an `images/` directory and
  packagegroups by the `packagegroup-` name prefix — both standard BitBake
  conventions. A recipe that merely ends in `-image` is **not** treated as an
  image.
- Layer detection relies on the dot labels carrying each recipe's `.bb` path,
  which is how recent BitBake emits them. If a future format changes that, layer
  colours fall back to "unknown" while nodes and edges still work.

## Tech

Plain HTML/CSS/JS in one file. [D3 v7](https://d3js.org/) for the force
simulation and scales, loaded from a CDN. No build step, no backend.

## License

MIT
