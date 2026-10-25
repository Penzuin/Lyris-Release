# Content files

The game reads these once at start-up, so new items and changes to blocks
need no recompile: edit, save, restart the game. The build copies this
folder next to the exe; the game looks in `./data` first, then beside the
exe, then in the folder named by the `LYRIS_DATA` environment variable.
Mistakes are printed to the console (file and line) and skipped; they never
stop the game.

Both files are JSON with two conveniences: `//` and `/* */` comments, and a
trailing comma before `]` or `}`.

Checking your work without playing: `VoxelStarter --itemsheet out.ppm`
draws every loose item's icon on one sheet and exits.

## items.json

```json
{ "items": [ { "key": "flint_axe", "durability": 80 }, { "key": "new_thing", ... } ] }
```

An entry with a **built-in key** changes only the fields it gives. Built-in
keys: `flint`, `stick`, `plant_fibre`, `flint_axe_head`,
`flint_blade`, `flint_shovel_head`, `flint_pick_head`, `flint_axe`,
`flint_knife`, `flint_shovel`, `flint_pickaxe`, `axe` (the Woodcutter's
Axe, iron tier), and the forged tools `copper_`, `bronze_`, `iron_` and
`steel_` + `axe`, `pickaxe`, `shovel` (no `iron_axe`: that is `axe`). An entry with a **new key**
adds an item. Blocks are items too (their key is the block id), but they
are changed in blocks.json, not here.

| field | value | default for a new item |
|---|---|---|
| `key` | a-z, 0-9 and `_`; saves and `/give` use it, so don't rename it once used | (required) |
| `name` | shown to the player | the key |
| `note` | one line about it | none |
| `kind` | `material`, `part` or `tool` | `material` |
| `stack` | 1-999 in one slot (tools are always 1) | 64 |
| `durability` | uses before it breaks, 0 = never | 0 |
| `tool` | `axe`, `knife`, `shovel`, `pickaxe`, `hammer`, `hoe`, `spear` | none |
| `tier` | 0-5 or `hand`, `flint` (= `stone`), `copper`, `bronze`, `iron`, `steel` | 0 |
| `speed` | how many times faster it works its own blocks (axe: wood; knife: leaves and plants; shovel: earth, sand, snow; pickaxe and hammer: stone, crystal, ice) | 1 |
| `hold` | `block` (held out), `small` (in the palm), `tool` (gripped by the handle) | `small`, `tool` for tools |
| `places` | a block id: using it places that block (seeds, say) | nothing |
| `model` | a built-in model by name: the built-in keys above, `lump`, or `crafted:<key>` for the crafting framework's version of stick, plant_fibre, flint, flint_axe_head, flint_blade, flint_axe, flint_knife | `lump` if no cuboids |
| `cuboids` | a list of boxes, see below | none |
| `colour` | `"#rrggbb"` or `[r, g, b]`: its swatch | grey |
| `dropScale` | its size lying on the ground | 0.42 |

Giving an item a `tool` makes it a tool (one to a slot, gripped).

### Cuboid models

```json
"cuboids": [
    { "from": [-4, -2, -1], "to": [4, 2, 1], "colour": "#3a3550" },
    { "from": [-1, 2, -1], "to": [3, 5, 1], "colour": "#5a5478", "turn": 25 }
]
```

Boxes in sixteenths of a block, in the **handle frame**: centred on 0, a
block runs -8..8. For tools, y runs up the handle (the hand grips near
y -5), the working end sits at the top and points toward +x, and the flat
faces (cheeks) face z. `turn` rotates a box about z round its own centre,
in degrees. Keep to the house palette: warm woods, cool stone, no black.

## blocks.json

```json
{ "blocks": { "gravel": { "drops": [ { "item": "flint", "chance": 0.12, "instead": true } ] } } }
```

Keys are block ids (the `--blocks` table, or what `/give` takes). Fields:

| field | value |
|---|---|
| `name` | shown to the player (its item follows) |
| `hardness` | seconds to break by hand; 0 = one punch, -1 = unbreakable |
| `light` | light it gives, 0-15 |
| `glow` | how much it glows in the shader, 0-4 |
| `dropSelf` | `false`: it no longer drops its own item |
| `minTier` | drops nothing (or its `lowDrop`) unless broken with a tool that suits it, of this tier or better |
| `breakTier` | can't be broken at all without a tool that suits it, of this tier or better (rock: a pickaxe); 0 = bare hands can |
| `lowDrop` | an item key it gives when broken below `minTier` (diamond kimberlite: `kimberlite`); `""` = nothing |
| `drops` | a list that **replaces** the block's built-in extra drops |

A drop: `item` (a key), `chance` 0-1 (default 1), `min` and `max` (default
1), `instead` (`true`: when it drops, the block's own item doesn't), and
optionally `tool` with `toolChance` and `toolMax` for a different chance
and most with that kind of tool in hand.

Built-in harvest tiers (`--blocks` prints them): rock cubes and slabs need a
pickaxe to break, flint for stone, sandstone, limestone, slate, travertine
and kimberlite (permafrost: a flint shovel), copper for granite, basalt and gneiss, bronze for
gabbro, iron for obsidian; garnet kimberlite drops whole only for bronze and
diamond kimberlite for iron (else `kimberlite`); ice and packed ice drop only
for a pickaxe.

Built-in extra drops: flint from gravel, gravel slabs and shingle (12%,
instead) and limestone (4%); plant fibre from tall grass, reeds, cattails,
rushes, bulrushes, sedge, dune grass and cottongrass (30%, a knife always,
up to two); sticks from twigs and thin branches (always) and leaves (6%).

New blocks still need code (the block list is compiled in, and every block
has painted textures): ask in a session.
