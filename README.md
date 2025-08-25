# Pink Engine — Ren'Py RPG Toolkit for Tilemaps and Visual Novels

[![Releases](https://img.shields.io/badge/download-Releases-blue?logo=github&style=for-the-badge)](https://github.com/Pvrado/pink-engine/releases)

♦️ Pink Engine

Badges
- [![Python](https://img.shields.io/badge/language-Python-3776AB?style=flat&logo=python)](https://www.python.org/)
- [![Renpy](https://img.shields.io/badge/engine-Ren%27Py-ff66aa?style=flat&logo=renpy)](https://www.renpy.org/)
- [![Tiled](https://img.shields.io/badge/tool-Tiled-2b9bf6?style=flat&logo=data:image/png;base64,iVBORw0KGgo=)](https://www.mapeditor.org/)
- Topics: game · pink-engine · python · renpy · renpy-engine · renpy-game · rpg · tiled · tilemap · visual-novel

Overview
Pink Engine is a modular toolkit that blends Ren'Py visual novel flow with tile-based RPG features. It gives Ren'Py projects a map layer, entity system, event triggers, and a reusable battle state machine. Use it to build hybrid games that mix dialog, exploration, and grid or pixel movement.

This README covers:
- what Pink Engine does
- how it organizes files
- how to install and run
- how to build maps and events with Tiled
- how to hook systems into Ren'Py scripts
- API examples and templates
- packaging and releases

Download the release package from the Releases page and run the installer or extract the archive:
https://github.com/Pvrado/pink-engine/releases

Key features
- Map layer for Ren'Py
  - Top-down and sidescroller map modules
  - Tile collisions and layered rendering
  - Animated tile support
- Tiled editor support
  - Read Tiled (.tmx) files directly
  - Auto-assign collision, events, and zones
- Entity and component system
  - Player and NPC actors
  - Inventory component, stats, and equipment slots
- Dialogue and VN integration
  - Call Ren'Py labels from map events
  - Pause map and run VN flow
- Battle state machine
  - Turn-based and active-time modes
  - Skills, targets, and status effects
- Tools and debuggers
  - Map visualizer
  - Event logger
  - Hot reload for development
- Python-first API
  - Use Python and Ren'Py script together
  - Extend with custom components and behaviors

Screenshots and demo assets
- Demo map poster:
  ![Demo Map](https://raw.githubusercontent.com/Pvrado/pink-engine/main/assets/screenshots/demo-map.png)
- NPC dialog sample:
  ![Dialog Sample](https://raw.githubusercontent.com/Pvrado/pink-engine/main/assets/screenshots/dialog-sample.png)
- Tiled sample map:
  ![Tiled Map](https://raw.githubusercontent.com/Pvrado/pink-engine/main/assets/screenshots/tiled-sample.png)

Why pick Pink Engine
- You get tile-based movement inside Ren'Py.
- You keep Ren'Py narrative tools.
- You reuse your Python skills.
- You keep your project small and clear.

Repository layout
- pink_engine/
  - core/             # core systems (map, entity, events)
  - io/               # Tiled loader and asset helpers
  - ui/               # Ren'Py screens and HUD
  - systems/          # battle, inventory, camera, pathfinding
  - examples/         # full example projects and scripts
  - tools/            # map visualizer, debug console
- assets/
  - tilesets/
  - sprites/
  - audio/
- docs/
  - guides/
  - api.md
- tests/
  - unit/
  - sample-projects/
- scripts/
  - build_release.py

Supported platforms
- Windows, macOS, Linux where Ren'Py runs.
- Python 3.8+ for standalone utilities and tools.
- Ren'Py 7.4+ for integration screens.

Requirements
- Ren'Py (7.4 or later).
- Python 3.8+ for tooling.
- Tiled (optional, for editing maps).
- Pillow (image helper).
- PyTMX-like loader (built in).

Quick start — demo project
1. Install Ren'Py and create a project.
2. Copy the pink_engine folder into game/.
3. Copy assets/ into game/.
4. Open Ren'Py launcher and start the project.
5. Use the sample labels to jump into the map demo.

Detailed install
A. Via release package (recommended)
1. Visit the Releases page: https://github.com/Pvrado/pink-engine/releases
2. Download the release archive or installer for your OS.
3. If the release contains an installer, run it. If it contains a zip, extract it.
4. Copy the extracted pink_engine folder to your Ren'Py project's game/ directory.
5. Start Ren'Py and launch the project.

B. Via git (for development)
1. Clone this repo:
   git clone https://github.com/Pvrado/pink-engine.git
2. Copy the pink_engine directory into your Ren'Py project's game/ folder.
3. Add the examples to your project folder or connect by import paths.

C. Dependencies
- If you use the map visualizer or tools, run:
  pip install -r requirements.txt
- Core systems have no external runtime deps beyond Ren'Py and Python.

Using the Releases link again
- Visit the Releases page to get stable builds and assets: https://github.com/Pvrado/pink-engine/releases
- If you see an asset with a name like pink-engine-vX.Y.zip, download that file and run or extract it to install the engine files into your project.

Getting started with Tiled
- Create a new orthogonal map in Tiled.
- Use tilesets compatible with the tile size your project uses (16x16, 32x32).
- Add a "collision" layer with tiles set to block movement.
- Add an object layer named "events" and place point objects.
  - For each event object add properties:
    - event_type (string): label, npc, chest, warp
    - label (string): renpy label to call
    - id (string): unique id for that event
- Export the map as .tmx and place it in assets/tiled/.

TMX loader conventions
- Layer names:
  - ground: base visual layer
  - above: layer drawn above actors
  - collision: tiles that block movement
  - events: object layer with triggers
- Object properties:
  - trigger_on: enter, interact, leave
  - action: call, give_item, start_battle
  - args: JSON string or comma list for action args

Map setup example
- Map file: maps/forest.tmx
- Tileset: tiles/forest_tiles.png
- Actors:
  - actor_start position set in the map by an object with property id=player_start
- To load map in-game:
  python
  from pink_engine.core.map import Map
  map = Map.load("assets/tiled/forest.tmx")
  map.spawn_player_at("player_start")
  renpy.call_in_new_context("map_scene", map=map)

Ren'Py integration
Pink Engine provides Ren'Py screens and helper functions. Use a screen to embed the map canvas and to forward input from Ren'Py to the engine.

Basic label that opens a map
label start:
    $ from pink_engine.renpy_integration import MapScreen, load_map
    $ my_map = load_map("assets/tiled/forest.tmx")
    show screen map_screen(my_map)
    return

Map screen example
screen map_screen(map_obj):
    frame:
        background None
        add map_obj.get_surface()
    key "up" action SetVariable("map_action", "move_up")
    key "down" action SetVariable("map_action", "move_down")
    key "space" action Function(map_obj.interact)

The provided screen templates handle:
- drawing the map to a Render
- scaling and letterbox
- HUD and menus
- input capture and mapping to actor actions

Entity system
- Actor class models a game actor.
- Components add features to actors.
  - PositionComponent: x, y, z
  - SpriteComponent: animation and frames
  - CollisionComponent: bounding box
  - InventoryComponent: items and capacity
  - CombatComponent: stats and skills

Actor example
python
from pink_engine.core.actor import Actor
from pink_engine.core.components import PositionComponent, SpriteComponent

player = Actor("player")
player.add(PositionComponent(32, 64))
player.add(SpriteComponent("player_sprites.png"))

Pathfinding
- Built-in A* over tile grid.
- Supports diagonal movement or orthogonal only.
- Uses cost maps and dynamic obstacles.
- API:
  path = map.find_path(start, goal, allow_diagonal=True)
  actor.follow_path(path)

Collision rules
- Tiles marked on collision layer block movement.
- Actors can have collision masks.
- Dynamic collision checks run each tick.

Event system
- Triggers from map objects call event handlers.
- Handlers can run Python code or Ren'Py labels.
- Common triggers:
  - call_label: run a Ren'Py label
  - spawn_enemy: create an enemy actor
  - start_battle: switch to battle state
- Define an event handler in Python:
python
def chest_opened(event, state):
    player = state.player
    player.inventory.add("gold", 50)
    renpy.call_in_new_context("chest_loot")

Battle system
Pink Engine ships a battle state machine that works inside Ren'Py. It supports:
- Turn-based rounds
- Action queue
- Skills with cooldowns and resource costs
- Targeting selection screen
- Status effects and buff stacking

Battle flow
- map triggers call start_battle with encounter id
- system loads combatants from encounter data
- combat state takes over rendering and input
- when battle ends, it returns control to Ren'Py labels or the map

Sample battle definition (YAML)
encounters/forest_wolf.yaml
name: Forest Wolves
type: encounter
enemies:
  - id: wolf
    count: 3
    ai: aggressive
rewards:
  xp: 50
  items:
    - id: hide
      qty: 1

Skill example (Python)
class Fireball(Skill):
    name = "Fireball"
    cost_mp = 10
    def execute(self, caster, target):
        damage = caster.stats.int * 2
        target.stats.hp -= damage
        if target.stats.hp <= 0:
            target.die()

Scripting API
- Core modules export simple functions and classes.
- Use python blocks in Ren'Py to access them.
- Example:
label use_heal:
    python:
        from pink_engine.systems import combat
        combat.apply_heal(player, 50)
    "You feel better."

UI and HUD
- The UI layer uses Ren'Py screens.
- You can override screens to match your art.
- The HUD exposes:
  - HP/MP bars
  - Mini-map
  - Current objective
  - Dialog overlay

Asset pipeline
- Art assets go under assets/ as PNGs or webp
- Sprites use sprite sheets arranged in rows
- Animations use a JSON file mapping frames
- Audio assets go to assets/audio/; Ren'Py handles playback

Example sprite sheet config
animations/player.json
walk_down: [0,1,2,1]
walk_left: [3,4,5,4]
walk_right: [6,7,8,7]
walk_up: [9,10,11,10]

Hot reload during development
- The engine watches map and script files in development mode.
- It reloads only changed modules.
- Use the debug console to force reload:
    python
    from pink_engine.tools import reload_engine
    reload_engine()

Debug tools
- Map visualizer displays collision, events, and paths.
- Event logger prints to console when triggers fire.
- Performance overlay shows FPS and tick time.

Command line tools
- maptool export-tmx assets/tiled/forest.tmx --optimize
- build_release.py creates a zip for distribution
- test_runner runs sample scenarios

Example project — walkthrough
This section guides you through creating a small demo that uses a map, an NPC, a chest, and a battle.

1. Create a Ren'Py project.
2. Add pink_engine folder under game/.
3. Add the demo assets to game/assets/.
4. Create a map in Tiled named demo_map.tmx. Add:
   - tile layer "ground"
   - tile layer "above"
   - collision layer "collision"
   - object layer "events"
5. In events, place an object at x:100, y:100 with properties:
   - id: npc_guard
   - event_type: npc
   - label: guard_talk
6. Add player_start object with id player_start.
7. Add an encounter object with id encounter_wolf and event_type start_battle.
8. In script.rpy:
label map_demo:
    python:
        from pink_engine.renpy_integration import load_map
        map_obj = load_map("assets/tiled/demo_map.tmx")
        map_obj.spawn_player_at("player_start")
    show screen map_screen(map_obj)
    return

9. Create Ren'Py labels:
label guard_talk:
    "Guard" "Stay out of the forest."
    return

label battle_return:
    "You won the fight."
    return

Modding and extensions
- Create new component classes for features.
- Use events to hook custom code.
- You can swap the battle state for your own.
- The engine exposes clear extension points.

Performance tips
- Use small tilesets for memory.
- Batch draw calls with sprite atlases.
- Use fewer animated tiles when possible.
- Disable debug overlays in release builds.

Testing
- Unit tests live under tests/unit.
- Run tests with pytest:
  pip install pytest
  pytest tests/unit
- Sample functional tests run the demo scenario to validate triggers.

Versioning and releases
- Releases follow semver: MAJOR.MINOR.PATCH.
- Release bundles include a sample project and the core folder ready to copy into Ren'Py projects.
- Find release builds here:
  https://github.com/Pvrado/pink-engine/releases
- If the release page lists a file like pink-engine-1.2.0.zip, download that file. Extract and copy the pink_engine folder into your Ren'Py game/ directory.

Roadmap
- v1.x: stable core map, battle, Tiled loader.
- v2.0: add real-time combat mode and editor hooks.
- v2.x: audio engine integration for ambient zones.
- Planned: a small visual map editor built into a Ren'Py screen.

Contribution guide
- Fork the repo.
- Create a feature branch in your fork.
- Follow the code style used across modules.
- Write unit tests for new features.
- Open a pull request with a clear description.
- Use small, focused commits.

Coding style
- Use Python 3 typing where useful.
- Keep modules small and focused.
- Favor composition and components.
- Document public API in docs/api.md

API reference (selected)
- pink_engine.core.map.Map
  - Map.load(path) -> Map
  - Map.spawn_player_at(id)
  - Map.find_path(start, goal, allow_diagonal=False)
  - Map.tick(dt)
- pink_engine.core.actor.Actor
  - Actor(id)
  - add(component)
  - remove(component_type)
  - get(component_type)
- pink_engine.systems.battle.Battle
  - Battle.start(encounter)
  - Battle.tick(dt)
  - Battle.end(result)

Sample code blocks
- Loading a map and calling a Ren'Py label when entering a zone:
python
from pink_engine.core.map import Map
from pink_engine.core.events import EventHandler

def on_enter_zone(event):
    renpy.call_in_new_context("zone_label")

m = Map.load("assets/tiled/forest.tmx")
m.register_event_handler("zone_1", on_enter_zone)

- Spawning an NPC with a simple AI:
python
from pink_engine.core.actor import Actor
from pink_engine.core.components import PositionComponent, SpriteComponent

npc = Actor("guard")
npc.add(PositionComponent(200, 150))
npc.add(SpriteComponent("guard_sprites.png"))
npc.ai = SimplePatrolAI(path=[(200,150),(220,150),(220,170),(200,170)])

Troubleshooting (common issues)
- If the map does not show:
  - Check that the map file path is correct.
  - Ensure the tileset images are in assets and paths match the TMX.
- If events do not fire:
  - Confirm object property names match loader conventions.
  - Open the event logger to inspect registered events.
- If the renderer looks off:
  - Verify tile size and screen scale settings in config.

Security and asset licensing
- Respect third-party asset licenses.
- Ship only assets you own or have rights to.
- Engine code itself is under the repository license.

Licensing
- The project code uses a permissive license. See LICENSE file in the repo.

Credits and third-party tools
- Ren'Py for the visual novel engine.
- Tiled for map editing.
- Pillow for image tasks.
- Example art from free asset packs included in demo (see assets/credits.txt).

Maintainer contact
- Open an issue on GitHub for bugs and feature requests.
- Use pull requests for code contributions.

Appendix A — Map TMX example
An example of a tiny TMX setup expressed as pseudo-TMX attributes for clarity:

- map:
  - tileset: "tiles/forest_tiles.png"
  - tilewidth: 32
  - tileheight: 32
- layers:
  - ground (tile layer)
  - above (tile layer)
  - collision (tile layer) -> set tiles where collision is true
- object layer: events
  - object id=1 x=96 y=128 name="player_start" properties: id=player_start
  - object id=2 x=160 y=96 name="npc_guard" properties: id=npc_guard,event_type=npc,label=guard_talk

Appendix B — Common event actions
- call_label: calls a Ren'Py label. Args: label name.
- start_battle: begins an encounter. Args: encounter id.
- give_item: add item to inventory. Args: item id, qty.
- warp: move player to a map coordinate or other map. Args: map path, x, y.

Appendix C — Sample project tree
game/
  - script.rpy
  - screens.rpy
  - pink_engine/
    - core/
    - renpy_integration/
    - systems/
  - assets/
    - tiled/
      - demo_map.tmx
    - tiles/
    - sprites/
  - examples/
    - demo_save.rpy

FAQ
Q: Will this work with custom Ren'Py GUIs?
A: Yes. The engine uses screens and APIs. You can replace screens.

Q: Can I use non-orthogonal maps?
A: The loader focuses on orthogonal and isometric. You can extend it for hex.

Q: How do I add custom AI?
A: Implement an AI class with a tick() method and attach it to an Actor.ai field.

Changelog
- See the Releases page for release notes and downloads:
  https://github.com/Pvrado/pink-engine/releases

Community and support
- Open issues on GitHub for bugs.
- Request features via issues and pull requests.
- Share example projects in the repository or via issues.

Project philosophy
- Keep game code small and explicit.
- Let Ren'Py handle narrative flow.
- Keep map logic in Python modules that you can test.

Design notes
- The engine splits concerns into map rendering, actor logic, and UI.
- It favors components for easy extension.
- It uses a tick-based loop for deterministic updates.

Developer tips
- Run the demo project while you iterate on maps.
- Use the visualizer to check collisions and paths.
- Keep tileset versions in assets and update TMX when you swap images.

Advanced: embedding custom shaders
- Ren'Py supports shader transforms on displayables.
- Apply a transform to the map surface for transitions or post-processing.

Advanced: networked play concept
- The core is not network-aware.
- You can build a thin sync layer that sends actor positions and events.
- Keep deterministic rules on the server to avoid drift.

Localization
- Use Ren'Py translation features for dialogs.
- Map labels and event properties can use keys that map to translations.

Testing scenarios
- Create test maps that stress the pathfinder.
- Automate replay of event sequences in tests.

Maintenance
- Keep dependencies pinned in requirements for tools.
- Add unit tests when adding features.
- Tag releases with notes and sample packs.

Credits
- Engine prototype and code by the Pink Engine team.
- Sample assets and demo art come from public domain and contributors. See credits file.

If you need a release build, download and run the file found on the Releases page. The release packages contain all core code and demo assets for fast setup:
https://github.com/Pvrado/pink-engine/releases

License file and contributor guidelines live in this repository.