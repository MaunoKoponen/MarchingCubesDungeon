# MarchingCubesDungeon

A browser-based dungeon mesh generator and game prototype using Marching Cubes, ported from an old Unity project.

## The Core Idea

A 2D symbolic tile map drives a 3D scalar field, which Marching Cubes converts into a mesh. The key trick is **terrainHardness** — by assigning very large scalar values to solid surfaces (floor = +300, pit bottom = +60000), the MC interpolation is pushed hard toward the air side of each voxel edge. The result is sharp 90° dungeon corners instead of the usual smooth terrain slopes.

```
tile symbols:  x=wall  _=empty  o=pit  a/b/c=height steps  A/B/C=floating ledges  w=walkway
```

## Files

| File | Description |
|------|-------------|
| `viewer.html` | Three.js viewer with triplanar procedural stone shader |
| `editor.html` | Split-view: live 3D preview + 2D canvas tile map editor |
| `game.html` | Real-time dungeon game with AI NPCs, A* pathfinding, and d20 combat |
| `mjkMArchingCubes.js` | Original Unity UnityScript source (reference only) |

---

## Viewer

Open `viewer.html` in a browser (serve via local HTTP server).

- **Drag** — orbit camera
- **Right drag** — pan
- **Scroll** — zoom
- **W** — toggle wireframe
- **R** — reset camera

Sliders adjust ambient/sun lighting, texture scale, zone blend width, and the three height-zone transition centers (Pit→Floor, Floor→Wall, Wall cap).

The shader uses triplanar mapping with three procedural stone textures blended by world-space Y height:
- **Pit** — dark damp stone (low Y)
- **Floor** — flagstone with Voronoi cracks (mid Y)
- **Wall** — brick courses (high Y / vertical faces)

---

## Editor

Open `editor.html` in a browser.

- **Left-drag** on 2D map — paint tiles
- **Right-drag** on 2D map — erase (set to empty)
- **Copy map** — copies the current map as a JS array to clipboard
- The 3D mesh rebuilds automatically ~120ms after any map edit

Tile palette: Empty, Wall, Pit, Step 1/2/3, Ledge A/B/C, Walkway.

---

## Game

Open `game.html` in a browser (requires a local HTTP server for GLTF asset loading).

A fully automatic combat demo — no player input needed. Watch the AI navigate, fight, and react.

### Controls

| Key | Action |
|-----|--------|
| Space | Pause / resume |
| D | Toggle debug overlay (nav nodes + AI states) |
| W | Toggle wireframe dungeon |
| R | Reset camera |

### Toolbar sliders

| Slider | Effect |
|--------|--------|
| Slope | Max height-step traversable by nav graph (0–3) |
| Sight | Enemy detection radius in world units |
| Speed | Global combat speed multiplier |
| Scale | Character model scale |

**Spawn** button resets and respawns all entities.

### AI & Combat

- **Party** (Wizard, Knight, Paladin, Elf) — `seeker` type: immediately pathfind to nearest enemy and engage
- **Goblins** — `wanderer` type: patrol randomly until they spot or hear a party member, then attack
- Pathfinding: A* on a nav graph built from the tile map; each character claims a separate adjacent attack node so they fan out instead of stacking
- Noise alert: combat wakes up all nearby allies within noise radius even without line-of-sight
- HP-based speed: wounded units move slower (35% speed at 0 HP → 100% at full HP)
- Combat: minimal d20 — `1d20 + attackBonus` vs AC, damage by dice spec (`1d8+3` etc.)
- Death: plays Death animation, then unit is removed from combat; survivors play Victory

### Stats

| Unit | HP | AC | Attack | Damage |
|------|----|----|--------|--------|
| Knight / Paladin | 16 | 16 | +5 | 1d8+3 |
| Elf | 8 | 13 | +5 | 1d8 |
| Wizard | 6 | 12 | +4 | 1d6+2 |
| Goblin | 7 | 11 | +4 | 1d6 |

---

## Parameters

```
SIZE = 32          (voxel grid: 32×32×32)
AXIS_MAX = 78      (world-space extent)
PIT_DEPTH = 4      (floor sits at voxel y=4, world y≈10)
terrainHardness = 6
```

## Origin

Salvaged from a Unity game prototype. The original UnityScript (`mjkMArchingCubes.js`) is included as reference. The JS port replicates the scalar field construction and Paul Bourke's Marching Cubes lookup tables verbatim.
