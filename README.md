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

### [st-status](https://github.com/Stviri/st-status)
> Character status selection on first spawn

The first thing a new player does is choose who they are. Status is a permanent character attribute that defines starting conditions, economic modifiers, and long-term gameplay paths.

| Status | Starting Conditions | Advantages | Disadvantages |
|--------|-------------------|------------|---------------|
| **Immigrant** | No money, no items | Slower hunger drain, cheaper crafting (-20%) | Expensive shops (+20%), no reputation |
| **Local** | $50 cash + knife | Trusted reputation (+100), normal shop prices | Expensive crafting (+10%) |
| **Drifter** | Knife only | No job change cooldown, neutral reputation | Suspicious to NPCs, slightly pricier shops (+10%) |

**Features:**
- Status selection menu appears on first spawn with full pros/cons display
- Player is locked (frozen, controls disabled) during selection — cannot skip
- Server-side validation prevents reselection after first choice
- Inventory wipe and status-appropriate items applied on selection
- Stored in RSGCore player metadata as `playerstatus`
- Compatible with all RSGCore resources via `Player.Functions.GetMetaData('playerstatus')`

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `rsg-inventory`, `rsg-multicharacter`

---

### [st-housing] *(in development)*
> Player-built, persistent housing with cooperative construction

Players purchase building plans from Housing Agent NPCs, choose a location anywhere on the map, and physically construct their home by gathering and depositing crafting materials. Houses are persistent world objects — they stay in the world when the owner logs off.

**Features:**
- Ghost prop placement system with real-time rotation and height adjustment
- Validity checking — cannot place inside towns or too close to existing properties
- Cooperative building — owner shares keys with other players who can contribute materials
- Construction site container — ghost prop accepts material deposits, progress bar fills to 100%
- At 100% completion, prop becomes solid and storage/addons unlock
- Automatic vegetation removal in 30m radius around placed houses
- Property storage (capacity scales with house tier)
- Key management — give/revoke access to any player
- Ownership transfer between players
- Daily property tax with grace period and abandonment system (anti-hogging)
- Abandoned properties visible on map, repossessed after inactivity

**House tiers:**
- Small Cabin — basic shelter, low tax, limited storage
- Medium Cabin — comfortable homestead, moderate requirements
- Ranch House — large property, heavy material requirements, farming/ranching addon support

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `ox_target`, `rsg-inventory`

**Optional:** `st-status` (gates land ownership for Immigrants until milestone reached)

---

### [st-jobs] *(planned)*
> Status-aware job application system with cooldowns and reputation gates

RSGCore has no built-in job application system. `st-jobs` provides a town hall NPC interface where players apply for jobs, with requirements gated by status and reputation. Drifters bypass cooldowns as their core perk.

**Features:**
- Town hall NPCs in each major settlement where players browse and apply for jobs
- Jobs gated by `playerstatus` — certain jobs require Local status or specific reputation
- Job change cooldown (24 hours for Immigrant/Local, no cooldown for Drifter)
- Full job history tracked in dedicated `st_job_history` table — never touches RSGCore tables
- Uses `Player.Functions.SetJob()` to assign — zero core edits
- Reputation requirements per job (Lawman requires positive rep, some outlaw jobs require negative)

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `ox_target`

**Optional:** `st-status` (for cooldown and gate logic), `st-reputation` (for rep requirements)

---

### [st-farming] *(planned)*
> Plot-based crop farming tied to housing system

Players with a property can cultivate crop plots on their land. Farming is the primary food production system for the server — raw ingredients flow into the player economy from here.

**Features:**
- Crop plots purchasable as housing addons (requires completed `st-housing` property)
- Plant seeds → water → tend → harvest cycle
- Session-aware timing — crops progress on real time, not session time (2-3 hour sessions are viable)
- Solo farming is possible but slow — group effort multiplies yield
- Raw crop output feeds into crafting and cooking systems
- Seasonal crop availability (some crops only in certain months)
- Crop decay if untended too long

**Crops (planned):** Corn, wheat, potatoes, tobacco, cotton, herbs

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `ox_target`, `rsg-inventory`, `st-housing`

---

### [st-ranching] *(planned)*
> Livestock management tied to housing system

Players with a ranch house property can raise, tend, breed, and sell livestock. Ranching is the primary source of meat, milk, wool, and leather in the player economy.

**Features:**
- Animal pens purchasable as ranch house addons
- Livestock types: cattle, sheep, pigs, chickens
- Daily feeding, health management, breeding cycles
- Produce collection: milk (cattle), eggs (chickens), wool (sheep)
- Slaughter for meat and hide
- Animal market — buy starter livestock from NPC, sell surplus to players or NPC buyers
- Solo ranching is hard — designed for 2-3 player cooperation
- Animals can die from neglect

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `ox_target`, `rsg-inventory`, `st-housing`

---

### [st-reputation] *(planned)*
> Server-wide reputation system with faction tracking

RSGCore has a reputation field but no behavior attached to it. `st-reputation` defines what reputation means, how it changes, and what it affects.

**Features:**
- Global reputation score per character (tracked separately from RSGCore's `rep` field)
- Reputation events: completing jobs, criminal activity, helping NPCs, paying taxes, business dealings
- Faction reputation: separate scores for each major settlement (Valentine, Rhodes, Saint Denis, etc.)
- Reputation effects: shop price modifiers, NPC dialog changes, access to certain jobs and areas
- Outlaw status threshold — above a certain negative rep, bounties become active
- Public reputation display for roleplay purposes
- Hooks for other `st-*` modules to add/remove reputation via exports

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`

---

### [st-economy] *(planned)*
> Dynamic market system bridging player production and consumption

The economy layer that connects all production systems (farming, ranching, hunting, mining) with consumption (food, crafting, construction). Prices respond to supply and demand with NPC buyers providing a price floor.

**Features:**
- Player market — list items for sale at chosen price, other players buy
- NPC market — fallback buyers for all raw materials at fixed lower prices
- Price history tracking — visible market trends
- Town treasury — tax revenue funds infrastructure upgrades
- Transitional phases — as player economy matures, NPC dependency decreases
- Anti-monopoly mechanics — position limits per item category

**Dependencies:** `rsg-core`, `ox_lib`, `oxmysql`, `rsg-inventory`

---

### [st-crafting] *(planned)*
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

## Roadmap

```
Phase 1 — Identity Layer          [Complete]
  └── st-status

Phase 2 — Property Layer          [In Development]
  └── st-housing

Phase 3 — Production Layer        [Planned]
  ├── st-farming
  ├── st-ranching
  └── st-crafting

Phase 4 — Social Layer            [Planned]
  ├── st-jobs
  └── st-reputation

Phase 5 — Economy Layer           [Planned]
  └── st-economy
```

---

## Dependency Map

```
st-status
    └── feeds into → st-jobs (status gates)
    └── feeds into → st-housing (immigrant milestone gate)
    └── feeds into → st-reputation (starting rep values)

st-housing
    └── enables → st-farming (crop plots addon)
    └── enables → st-ranching (animal pens addon)

st-reputation
    └── feeds into → st-jobs (rep requirements)
    └── feeds into → st-economy (shop price modifiers)

st-crafting
    └── feeds into → st-housing (construction materials)
    └── feeds into → st-farming (tools)
    └── feeds into → st-ranching (tools)

st-farming + st-ranching + hunting (existing)
    └── feeds into → st-economy (supply)

st-economy
    └── feeds into → everything (demand and pricing)
```

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
