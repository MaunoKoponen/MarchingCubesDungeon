# MarchingCubesDungeon

A browser-based dungeon mesh generator using Marching Cubes, ported from an old Unity project.

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
| `mjkMArchingCubes.js` | Original Unity UnityScript source (reference only) |

## Viewer

Open `viewer.html` in a browser. Controls:

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

## Editor

Open `editor.html` in a browser.

- **Left-drag** on 2D map — paint tiles
- **Right-drag** on 2D map — erase (set to empty)
- **Copy map** — copies the current map as a JS array to clipboard
- The 3D mesh rebuilds automatically ~120ms after any map edit

Tile palette: Empty, Wall, Pit, Step 1/2/3, Ledge A/B/C, Walkway.

## Parameters

```
SIZE = 32          (voxel grid: 32×32×32)
AXIS_MAX = 78      (world-space extent)
PIT_DEPTH = 4      (floor sits at voxel y=4, world y≈10)
terrainHardness = 6
```

## Origin

Salvaged from a Unity game prototype. The original UnityScript (`mjkMArchingCubes.js`) is included as reference. The JS port replicates the scalar field construction and Paul Bourke's Marching Cubes lookup tables verbatim.
