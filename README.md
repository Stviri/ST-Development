# ST-Development

> A modular, roleplay systems built for RedM — designed on RSG RedM Framework.

---

## What is ST-Development?

It is a collection of interconnected, standalone modules built on top of the [RSGCore](https://github.com/Rexshack-RedM) framework for RedM (Red Dead Redemption 2 multiplayer).


## Architecture Philosophy

**Open source**
Most modules will be open source and free to use, fork, and adapt. If you run RSGCore and want any of these systems on your server, clone and install.

---

## Modules

### [st-housing](https://github.com/Stviri/st-housing)
> Player-built, persistent housing with cooperative construction

Players purchase building plans from Housing Agent NPCs, choose a location anywhere on the map, and physically construct their home by gathering and depositing crafting materials. Houses are persistent world objects — they stay in the world when the owner logs off.
---
### [st-crafting](https://github.com/Stviri/st-crafting)
> Workbench, blacksmith and gunsmith crafting systen for building materials, tools and guns.
---
### [st-lumberjack](https://github.com/Stviri/st-lumberjack)
> Lumberjack system for RSGCore, pre-defined Lumber Company location with Foreman NPC, tree cutting minigame, buying/selling and NPC Stock systems.
---
### [st-mining](https://github.com/Stviri/st-mining)
> Mining system for RSGCore and ST-Resources.
---
### [st-wagons](https://github.com/Stviri/st-wagons)
> Wagon purchase/management system for RSGCore and ST-Resources.

---


## Installation

Each module will have its own installation instructions in its repository README. General steps:

1. Clone the desired module into your `resources/[custom]/` folder
2. Run the SQL file in your database
3. Add any required items to `rsg-core/shared/items.lua` as documented
4. Add `ensure st-[module]` to your `server.cfg` after RSGCore dependencies
5. Configure `shared/config.lua` to match your server

Some RSGCore files may need to be modified for some modules.

---

## Built With

- [RSGCore](https://github.com/Rexshack-RedM) — RedM roleplay framework
- [ox_lib](https://github.com/overextended/ox_lib) — UI and utility library
- [ox_target](https://github.com/overextended/ox_target) — Entity targeting
- [oxmysql](https://github.com/overextended/oxmysql) — MySQL async library
- [rsg-inventory](https://github.com/Rexshack-RedM/rsg-inventory) — Inventory system

---

## License

MIT — free to use, modify, and distribute. Credit appreciated but not required.

---

*Built for the RedM community. Made in Georgia 🇬🇪*
