# ST-Development

> A modular, roleplay systems built for RedM — designed on RSG RedM Framework.

---

## What is ST-Development?

It is a collection of interconnected, standalone modules built on top of the [RSGCore](https://github.com/Rexshack-RedM) framework for RedM (Red Dead Redemption 2 multiplayer). Each module is designed to be **minimally invasive** — minimal core file edits, clean event-driven architecture, and fully compatible with standard RSGCore installations.


## Architecture Philosophy

Every module in follows the same principles:

**1. Avoiding core edits**
Minimal RSGCore files are modified. Almost all integration happens through exports, events, and RSGCore's existing API.

**2. Modular and standalone**
Each `st-*` resource can be installed independently. Modules that depend on each other declare that dependency explicitly in `fxmanifest.lua` and degrade gracefully when optional dependencies are absent.

**3. Security first**
All gameplay-affecting logic lives server-side. Client events are validated. Player status, inventory, and money are never trusted from the client.

**4. RSGCore patterns**
Code style, naming conventions, and patterns follow RSGCore's own development guides — `local` scoping, short returns, `ipairs` over `table.insert`, server-side transactions only.

**5. Open source**
Most modules will be open source and free to use, fork, and adapt. If you run RSGCore and want any of these systems on your server, clone and install.

---

## Modules

### [st-housing] *(in development)*
> Player-built, persistent housing with cooperative construction

Players purchase building plans from Housing Agent NPCs, choose a location anywhere on the map, and physically construct their home by gathering and depositing crafting materials. Houses are persistent world objects — they stay in the world when the owner logs off.

**Features:**
- Ghost prop placement system with real-time rotation and height adjustment
- Validity checking — cannot place inside towns or too close to existing properties
- Cooperative building — owner shares keys with other players who can contribute materials
- Construction site container — ghost prop accepts material deposits, progress bar fills to 100%
- At 100% completion, prop becomes solid and storage/addons unlock
- Automatic vegetation removal in 10m radius around placed houses
- Property storage (capacity scales with house tier)
- Key management — give/revoke access to any player
- Ownership transfer between players
- Daily property tax with grace period and abandonment system (anti-hogging)
- Abandoned properties visible on map, repossessed after inactivity

**House tiers:**
- Small Cabin — basic shelter, low tax, limited storage (planned)
- Medium Cabin — comfortable homestead, moderate requirements (planned)
- Wooden House — larger sized house with several rooms.

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `ox_target`, `rsg-inventory`

**Optional:** `st-status` (gates land ownership for Immigrants until milestone reached) - not yet implemented

---
### [st-crafting] *(in development)*
> Workbench and blacksmith crafting for building materials and tools

A dedicated crafting system for processing raw materials into construction materials, tools, and equipment. Works independently of camp-based cooking.

**Crafting stations:**
- **Workbench** (at home or in town) — wood planks, rope, basic tools
- **Blacksmith** (town NPC) — iron nails, iron bars, horseshoes, glass panes
- **Tannery** — leather goods from hides
- **Sawmill** — bulk wood processing

**Crafting chain example:**
```
wood_log (gathered) → wood_plank (workbench) → used in st-housing construction
iron_ore (mined)    → iron_nail (blacksmith)  → used in st-housing construction
iron_ore (mined)    → iron_bar  (blacksmith)  → glass_pane (blacksmith) → st-housing windows
fiber    (harvested)→ rope      (by hand)     → used in st-housing construction
```

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `rsg-inventory`

---
### [st-lumberjack] *(in development)*

### [st-mining] *(planned)*

### [st-jobs] *(planned)*

### [st-farming] *(planned)*

### [st-ranching] *(planned)*

### [st-reputation] *(planned)*


---


## Installation

Each module will have its own installation instructions in its repository README. General steps:

1. Clone the desired module into your `resources/[custom]/` folder
2. Run the SQL file in your database
3. Add any required items to `rsg-core/shared/items.lua` as documented
4. Add `ensure st-[module]` to your `server.cfg` after RSGCore dependencies
5. Configure `shared/config.lua` to match your server

Some RSGCore files need to be modified for some modules.

---

## Built With

- [RSGCore](https://github.com/Rexshack-RedM) — RedM roleplay framework
- [ox_lib](https://github.com/overextended/ox_lib) — UI and utility library
- [ox_target](https://github.com/overextended/ox_target) — Entity targeting
- [oxmysql](https://github.com/overextended/oxmysql) — MySQL async library
- [rsg-inventory](https://github.com/Rexshack-RedM/rsg-inventory) — Inventory system

---

## Contributing

Pull requests and issues are welcome on any module repository. If you build something that extends or integrates with ST-Development, open an issue and we'll link it here.

---

## License

MIT — free to use, modify, and distribute. Credit appreciated but not required.

---

*Built for the RedM community. Made in Georgia 🇬🇪*
