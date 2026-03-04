# GalaxyTakeover

A space 4X strategy game inspired by Space Empires IV.

## Download

**Latest version: v2026.03.04-0330**

Go to the [Releases](https://github.com/AustinHeilman/GalaxyTakeover-releases/releases) page and download the installer from the latest tag.

### Requirements

- Windows 10/11 (64-bit)
- [.NET 10 Desktop Runtime x64](https://dotnet.microsoft.com/download/dotnet/10.0)

## Change Notes

GalaxyTakeover - Change Notes
==============================

[March 3 2026]

Command Bar Overhaul
--------------------
- Shared "cmdbar" style class ensures uniform button heights (28px) across all
command bars (ship, colony, and any future bars)
- SplitButtons (Build Queue, Cargo, Colonize) no longer render taller than
regular buttons
- Both ShipCommandBar and ConstructionCommandBar use the same shared styles

Data-Driven Component Commands
------------------------------
- Components can now define UI commands in their JSON "commands" array
- Commands automatically appear on the command bar and context menu when a unit
has the component installed
- Grouped commands: use "group" + "isPrimary" to create SplitButton dropdowns
(e.g., Colonize button with Colonize Nearest sub-item)
- The primary command IS the group parent — clicking the main SplitButton fires
it directly. Non-primary commands appear only in the dropdown.
- Multiple components/mods can add sub-items to the same group
- Command activation fires ui.command.{id}.activated events for script handlers
- Removed hardcoded Colonize button — now driven by colony module component JSON

Colonize Nearest (Script-Driven)
--------------------------------
- ColonizationHandler script now handles the "Colonize Nearest" command via
BFS through explored systems to find the nearest compatible planet
- Script sets target data on the event, VM reads it back to issue the order
- Fully data-driven: defined in component JSON, handled by mod script

Context Menu Improvements
-------------------------
- Context menu commands now appear in the same order as the command bar:
Move, Warp, Attack | Resupply, Repair | Cargo | dynamic commands |
Clear Orders | Details
- Added Repair to context menu (was missing)
- Dynamic/component commands appear after a separator, matching command bar layout
- Grouped commands show as submenu (e.g., Colonize > Colonize Nearest)

Orders Button
-------------
- Orders indicator is now a clickable button with a flyout showing current
orders summary (was a non-interactive display element)

Bug Fixes
---------
- Fixed: UnitCategory definitions not copied to game DefinitionProvider
(caused Move/Warp buttons to not appear on ships)
- Fixed: Blueprint TotalSize was 0kT (missing calculation in CreateBlueprintUnchecked)
- Fixed: Duplicate Colonize button (hardcoded + data-driven both showing)
- Fixed: Command bar and context menu button colors now match for same commands

Documentation
-------------
- Updated docs/modding/json-definitions.md with component commands schema
- Updated docs/modding/api/commands.md with component-defined command examples
- Updated docs/modding/event-types.md with ui.command.{id}.activated events
- Updated docs/design/unified-command-framework.md with current implementation

[February 25 2026]

Colonize Nearest
----------------
- Colonize button is now a split button with a "Colonize Nearest" dropdown option
- Automatically finds the nearest compatible uncolonized planet across the galaxy
and issues move + colonize orders (similar to how Resupply finds nearest depot)
- Considers all terrain types the ship can colonize (rock, ice, ocean, gas)
- Now only searches solar systems the player has already explored
- Skips planets that another colony ship is already en route to colonize

Galaxy Map
----------
- Fixed galaxy map not centering on home system when starting via Quick Start

Blueprint List
--------------
- Blueprints within each category are now sorted by chassis type, then by name
- Creating, copying, or upgrading a blueprint now selects the new design in the
list (previously the old design stayed highlighted even though the detail panel
showed the new one)

Ship Commands
-------------
- Resupply and Repair buttons are now always enabled when a ship is selected
(previously grayed out when fully supplied or undamaged — but you might want
to send ships to a yard for retrofit)

Empire Elimination
------------------
- Empires that lose all colonies are now properly eliminated
- Remaining ships are scuttled (no colonies = no resupply)
- All empires with diplomatic contact receive a notification
- Player empires get a defeat message if eliminated
- Eliminated empires are skipped in AI, production, research, and maintenance
- Fires empire.defeated event for mod/script hooks
- IsEliminated flag persists in save files

Selection Panel
---------------
- Ships in the object list now show their chassis type (Destroyer, Corvette,
Light Cruiser, etc.) instead of generic "Ship"

Combat Fixes
------------
- Bombardment now consumes weapon supplies (was free to bombard)
- Ships with insufficient supplies skip those weapons during bombardment

Research
--------
- Reordered research sub-items within areas for better progression clarity

[February 24 2026]

Resource Rebalance
------------------
- Reduced mineral dominance in the economy (~90% of spending was minerals)
- Facilities rebalanced: Spaceport is now organic-heavy, Resupply Depot is
radioactive-heavy, Shipyards use all three resources
- Weapons, shields, and engines flipped to ~30% mineral / 70% radioactive
(was ~70/30). Total costs unchanged, only the resource mix changed.

AI Economy Fixes
----------------
- Fixed mineral bankruptcy: all colony role blueprints now start with a
mineral extractor before specialty facilities
- Mineral crisis mode: when minerals are scarce, AI defers expensive
facilities (shipyard, resupply depot) and keeps building extractors
- Colonization throttle: AI pauses colony ship production when too many
colonies lack spaceports and minerals are low
- Resource converters: AI builds converters when minerals drop below 20%
storage and other resources are surplus

Bug Fixes
---------
- Fixed: Unit destruction cleanup (empire lists, blueprint counts, groups)
now handled consistently via OnDestroy() instead of scattered code
- Fixed: Phase shield research prerequisites corrected
- Fixed: Colony chassis detection uses RequiredTraitCount properly

[February 23 2026]

Fighters & Troops
-----------------
- Full fighter component suite: weapons, armor, shields, sensors, ECM,
engines (afterburner), and cockpits for fighter-scale ships
- Troop components: ground weapons, small-scale armor, shields, sensors,
and combat equipment for boarding and invasion units
- Three carrier chassis (Light, Medium, Heavy) with fighter bay requirements

Mines
-----
- Mine deployment and minesweeper components are now functional
- Mines trigger when enemy ships enter their cell
- Deploy mines from ships with mine layer components

Armor System Expansion
----------------------
- New armor types: Emissive Armor, Scattering Armor, Stealth Armor
- Armor now provides damage-type-specific resistances in combat
- Rebalanced armor tiers across all levels

Cargo Load & Unload
--------------------
- Cargo button is now a split button with three options:
- Transfer: Opens the full cargo transfer dialog (same as before)
- Load Cargo: Quickly fill your ship from a colony stockpile
- Unload Cargo: Drop all cargo at a colony
- Pick which cargo types to load/unload (fighters, drones, population, etc.)
- Ships can retrieve deployed units (fighters, drones, satellites, mines) back
into cargo from the system view context menu
- Ships can deploy units from cargo into the system

Black Holes
-----------
- Ships displaced by black hole gravity now automatically re-pathfind to
their destination instead of getting stuck
- This re-pathing works for any displacement (stellar manipulation, etc.)
- Black hole destruction now sends a log message to the unit's owner

AI Improvements
---------------
- AI ships low on supplies now resupply before entering combat
- Improved AI fleet management, research priorities, and ship design choices
- AI personalities tuned per race (e.g., Jeskari aggression)

Performance
-----------
- System view no longer redraws after every individual event during turn
processing — one final redraw at the end of the turn instead
- Noticeably smoother turn processing in busy solar systems

Ship Design
-----------
- Chassis now define required component traits (e.g., carriers must have
50%+ mass in fighter bays) instead of rigid slot counts
- Gives more flexibility in ship design while keeping class identity

Bug Fixes
---------
- Fixed: Population cargo on colony ships now shows the correct race portrait
instead of a question mark icon
- Fixed: Component category display and sort order improvements

[February 19 2026]

Special Solar Systems
---------------------
Four new hazardous system types can now appear on the galaxy map, shown as
diamond shapes instead of the usual circles:

- Dead Systems: Cold, dim stellar cores surrounded by asteroid belts. No
habitable planets, but good for mining. Up to 3 per galaxy.

- Pulsar Systems: Neutron stars with rotating radiation beams. Ships and
colonies caught in the beams take 25 damage per turn, and colonies lose
5% population. The beams rotate each turn so safe positions change.
Up to 2 per galaxy.

- Magnetar Systems: Neutron stars that build up magnetic charge over time.
When charge reaches critical levels, a devastating surge fires — dealing
up to 60 damage with falloff by distance and wiping out up to 20% of
colony population in range. Watch for the growing pink glow as a warning.
At most 1 per galaxy.

- Nebula Systems: Dense, colorful gas clouds that severely reduce scanner
range (~15% of normal). Great for ambushes, dangerous to navigate blind.
Six color variants. Up to 3 per galaxy.

Cloaking
--------
- New Cloaking Device components (Tiers I-III, requires Cloaking research)
- Toggle cloak on/off with the K key when a cloaked ship is selected
- Cloaked ships consume supplies each turn to maintain the cloak
- Ships automatically decloak when firing weapons in combat
- New Anti-Cloak Sensors (Tiers I-III) let you detect cloaked enemies
- Scanner Jamming: units with ECM can block enemy scanner sweeps in their cell

Stellar Manipulation
--------------------
A full suite of single-use ship components for reshaping the galaxy:
- Destroy or create storms, planets (up to Huge size), and warp points
- Create a new star in a starless system, or destroy one
- Collapse a star into a black hole (destroys everything, including your ship)
- Collapse a black hole back to normal space
- Convert a star system into a nebula, or dissipate an existing nebula
- All stellar manipulation components are consumed on use
- Every empire that has explored the affected system gets a notification

Transport Ships
---------------
- Three new transport chassis: Small, Medium, and Large
- Designed for cargo and troop bays — not combat capable
- Each race now has unique transport ship art for all three sizes

New Components
--------------
- Multiplex Tracking (II-V): Engage 2-5 different targets per combat round
instead of just one. After destroying a target, remaining weapons retarget
automatically. A major upgrade for heavily-armed warships.
- Solar Collectors (I-III): Generate 5/10/20 supplies per turn from starlight.
Output scales with the number of stars in the system.
- Quantum Reactors (I-III): Generate 100/250/500 supplies per turn anywhere,
even in deep space. Essential for long-range operations.
- Supply Storage expanded to 5 tiers (was 3) for finer control over capacity

Combat Improvements
-------------------
- Cloaked ships now decloak when firing, adding a tactical decision layer
- Multiplex tracking lets ships retarget after a kill in the same round
- Planetary shields absorb bombardment damage before it hits facilities
- Transport ships deploy troops during the troop landing combat phase
- Chassis size affects evasion: corvettes get +40% natural evasion,
large ships and bases are easier to hit

Star & Galaxy Generation
-------------------------
- Blue Giant and Red Giant star types added to the generation pool
- Stars now have spectral class, corona colors, and luminosity data
- Special systems (dead, pulsar, magnetar, nebula) show as diamonds on
the galaxy map
- Nebula systems display rich, layered gas cloud visuals filling the system
- Pulsar beams, magnetar surges, and magnetar charge glow are all visible
as dynamic effects in the system view

Emergency Save
--------------
- The game now attempts an automatic emergency save if it crashes,
so you won't lose your progress to an unexpected error

[February 17 2026]

Navigation Toolbar
------------------
- New Ship/Fleet/Colony cycling buttons on the toolbar (SE4-style < > arrows)
- Quickly find idle ships and fleets that need orders
- Cycle through all your colonies with one click
- Navigates to the system and selects the object automatically

New Components: ECM & Target Sensors
-------------------------------------
- Ships now have natural ECM (electronic countermeasures) based on chassis type
- New ECM and Target Sensor components available for ship designs
- Research unlocks progressively better sensor and countermeasure tech

Chassis Rename
--------------
- "Hull" terminology replaced with "Chassis" throughout the game for clarity

Research Improvements
---------------------
- Research tree now shows what each tech unlocks (components, facilities, capabilities)
- Easier to plan your research path when you can see the rewards

Colony Context Menus
--------------------
- Right-click colonies for quick access to common actions

Turn Processing
---------------
- Added a progress bar when processing turns so you can see what's happening
- End-of-turn processing is noticeably faster

UI Consistency
--------------
- "Open Construction Queue" renamed to "Build Queue" to match ship spaceyards
- Cargo and Build Queue buttons now use matching icons on both colony and ship bars

[February 14-16 2026]

New Race: Quilon
----------------
- Added the Quilon race with unique traits, capabilities, and planet modifiers

Combat & Movement Fixes
------------------------
- Fixed: Ships could fail to trigger combat when they should
- Fixed: Attack orders on nearest target work correctly now
- Fixed: Ships moving simultaneously no longer cause position conflicts
- Fixed: Rare bug where colonization could fail silently
- Fixed: Resupply and repair buttons work properly again

Diplomacy
---------
- AI empires now have treaty request cooldowns (no more constant spam)

[February 13 2026]

Intelligence System (Work in Progress)
--------------------------------------
- New Intelligence panel for espionage operations
- AI empires can now conduct intelligence ops
- Intelligence fixes and UI work

Colonization Fix
----------------
- Colonization research no longer incorrectly tied to atmosphere unlocks
- Researching atmospheres and colonization are properly separate now

[February 10-12 2026]

AI Overhaul
-----------
- AI empires now research and upgrade to larger, more powerful ship designs over time
- AI properly considers enemy territory for border defense (not just unexplored systems)
- AI spending is smarter: military budgets scale with empire size and actual warship costs
- AI colony ships carry appropriate population (10% of colony or 1 million, whichever is less)
- Fixed AI empires getting stuck and not issuing orders
- Fixed fleets displaying incorrectly on the galaxy map

Race-Specific Ship Names
-------------------------
- Each race now has its own thematic ship naming pool (~2,450 names total)
- Ships get lore-appropriate names instead of generic designations

Retrofit Dialog
---------------
- Ship retrofit dialog now works - click a blueprint to upgrade your ship
- Spaceyards can repair damaged ships

Smaller Save Files
------------------
- Save games are smaller: temporary data no longer bloated save files

Info Windows
------------
- Floating info windows (Research, Blueprints, etc.) now stay within the main game window

[February 8-9 2026]

Ship Upgrades
-------------
- Improved blueprint upgrade system for replacing older components

Scrap, Retrofit & Analyze
--------------------------
- Ships at colonies with spaceyards can be scrapped, retrofitted, or analyzed
- Recover resources from obsolete ships

Planet Generation
-----------------
- Gas giants and other planet types can now be larger sizes (not just gas = big)
- Moon population limits adjusted to be more reasonable
- Fixed population bars not displaying correctly

Balance
-------
- Resupply Depots and Space Ports no longer require research to build
- Resupply storage moved to correct research category

[January 28 - February 2 2026]

Tutorial System
---------------
- New interactive tutorial to help new players learn the game
- Guided walkthrough of core mechanics

Diplomacy Improvements
----------------------
- Improved diplomacy interactions and AI treaty behavior

Research Hints
--------------
- Tech tree shows hints about what technologies lead to

Ship Details
------------
- Selection panel now shows who owns a ship when viewing foreign vessels

Empire Colors
-------------
- Updated empire color palette for better visual distinction between players

Homeworld & Generation
----------------------
- Fixed homeworld placement issues
- Black holes are now rarer (were spawning too frequently)

Performance
-----------
- Grid-based lookup system for faster solar system object searches
- Script caching for faster mod loading

Bug Fixes
---------
- Fixed: Research tree errors that could break the game
- Fixed: Orbital bombardment not working correctly
- Fixed: Save/load could lose data in certain scenarios
- Fixed: Ship orders, production, and retrofit logic bugs
- Fixed: Missing image assets no longer cause errors

[January 20 2026]

Modding: Visual Event Handlers
------------------------------
- Scripts can now reference ViewModels types for UI-related events
- SystemObjectNode and other visual types available in scripts
- Enables mods to hook into visual events like black hole visual effects

Bug Fixes
---------
- Fixed: Save game loading now properly initializes all game services
(Previously could cause errors when loading saved games)
- Fixed: Script compilation error for handlers using SystemObjectNode

Under the Hood
--------------
- Large class refactoring for better maintainability
- Fleet management extracted to dedicated FleetService
- Galaxy generation extracted to GalaxyGenerationService
- Selection state management extracted to SelectionService
- Added comprehensive unit tests for new services

[January 12 2026]

Ship Supplies System
--------------------
- Ships now consume supplies when moving through space (grid movement)
- Engines provide supply capacity (500 units per engine)
- Ships without supplies are limited to 1 movement point per turn
- Selection panel shows supply levels with "no fuel!" indicator when empty
- Movement display shows turns to destination when ship has queued orders

Resupply & Repair Commands
--------------------------
- New "Resupply" button on ship command bar - navigates to nearest friendly colony
- New "Repair" button - navigates to nearest colony with a space yard facility
- Commands calculate shortest warp path and issue movement orders automatically

Ship Details Panel
------------------
- Selection panel now shows detailed ship stats: class, size, health, supplies, shields
- Sub-tabs for Detail, Components (with health bars), and Cargo
- Shows effective movement speed and turns to destination when orders are queued

End Turn Timer
--------------
- Long-press End Turn button to access real-time timer options
- Quick click still ends turn immediately
- Timer speeds: 5 sec, 30 sec, 60 sec, 90 sec, 5 min, 10 min
- "Disabled" option turns off automatic turn advancement
- Countdown timer displays directly on End Turn button when active
- Button turns red when timer is in last 10 seconds (warning)

Facility Services (Resupply & Repair)
-------------------------------------
- Ships now automatically resupply when entering a colony with a Resupply Depot
- Ships now automatically repair when entering a colony with a Space Yard
- New event: colony.unit.enters - fires when units enter a colony's grid position
- Efficient event handling - only fires when units actually reach colony locations

Bug Fixes
---------
- Fixed: AI empire colonization messages no longer appear in player's message log
- Fixed: Message log now properly filters messages by empire from initial load
- Fixed: Resupply depots now work - ships are resupplied when they reach the colony

Context Menu Improvements
-------------------------
- Context menus now display icons matching the command bar style
- Fixed: Right-clicking on planets, stars, warp points, and storms now shows the correct
context menu (was incorrectly showing "Center View / Reset Zoom" in some cases)
- Larger click targets on celestial objects - easier to right-click on small icons

Galaxy Generation Fix
---------------------
- Fixed: Homeworld no longer always spawns on the edge of the galaxy during quick start
(was always picking systems farthest from center)

[January 5 2026]

Space Hazards & Navigation
--------------------------
- Black holes, nebulae, and other hazards now display warning indicators on the map
- Ship pathfinding automatically routes around dangerous celestial objects
- Hazard properties are data-driven - modders can define custom hazards with danger zones

Galaxy Exploration Fixes
------------------------
- Fixed: Other empires now properly stay hidden until you actually observe them
(previously, just discovering a system could reveal empire locations)
- Fixed: Quickstart now generates a unique galaxy each time instead of the same one

[January 1-4 2026]

Ship Speed Rebalance (SE4-Style)
--------------------------------
- Ship speed now calculated from engine count and ship mass, matching Space Empires 4
- Engine slot counts adjusted across all hull types for strategic variety
- Quickstart blueprints updated to reflect new engine balance

Diplomacy Panel
---------------
- Race portraits and flags now display when viewing other empires
- See who you're negotiating with at a glance

Galaxy Map Improvements
-----------------------
- Enemy ships now visible on the galaxy map when you can observe them
- Enemy colonies appear on the strategic map when observed
- Map updates in real-time when ships warp to new systems
- View ship movement paths when looking at systems along a planned route

Combat System (New!)
--------------------
- Tactical combat on a 71x71 grid with positioning and movement
- Point defense systems protect against missiles and fighters
- Seeker/missile weapons launch guided projectiles
- Colony bombardment from orbit
- Ground combat - troops can land and capture colonies
- Combat integrates into turn processing automatically

Bug Fixes
---------
- Fixed: Ships now properly show on galaxy map after warping
- Fixed: No more duplicate key errors when colonies share grid positions
- Fixed: Colonization failure messages only go to the affected empire now

[December 29 2025]

Menu Bar Keyboard Shortcuts
---------------------------
- All menu bar panels now have keyboard shortcuts:
- B: Blueprints
- R: Research
- I: Intelligence
- D: Diplomacy
- L: Log (Message Log)
- E: Empire Overview
- S: Ships & Fleets
- Q: Construction Queue (when colonized planet selected)
- Escape: Close active panel
- Tooltips updated to show "(Keyboard: X)" format for discoverability
- Shortcuts are disabled when typing in text input fields

Message Log Window
------------------
- New Log button on toolbar (keyboard shortcut: L) opens the message log
- Tab filters: All, Communications, Construction, Colonization, Diplomacy, Research, Intelligence, Combat, Events
- "This Turn" / "All Turns" toggle to view current turn messages or full history
- Construction completions (facilities and ships) now appear in the log instead of popups
- Colonization results appear in the log
- Click a message to see full details in the right panel
- "Goto" button navigates to the related system and selects the object (ship, colony, etc.)
- "Mark All Read" clears unread indicators
- "Open at turn start" checkbox (on by default) - automatically opens the log when a new turn starts with messages
- Messages tagged with the turn number they occurred

Ship Command Bar
----------------
- New unified command bar below the toolbar for issuing ship/fleet orders
- Shows ship name with Move, Warp, Colonize, and Attack command buttons
- Click a command button, then click the map to execute (Move to location, Warp through point, Colonize planet)
- Custom targeting cursors appear when in command mode (blue for move, green for colonize, purple for warp)
- "Clear Orders" button to cancel pending movement and warp orders
- "Orders" indicator with tooltip showing pending orders summary
- Cancel button and instruction text when in targeting mode

Colony Command Bar
------------------
- Same row shows colony info when a colonized planet is selected
- Displays facility slots, production rates, and queue item count
- "Open Construction Queue" button for quick access

Colonization Target Dialog
--------------------------
- When colonizing a location with multiple targets (planet + moons), a selection dialog appears
- Shows which targets are compatible with your colony ship's terrain capabilities
- Works from both command bar button and right-click context menu

Floating Panel: Escape Key to Close
-----------------------------------
- Press Escape to close any open floating panel (Research, Blueprints, Diplomacy, etc.)
- Works on both standalone windows and AvalonDock floating panels
- Centralized keyboard handling in MainWindow for all floating windows

Floating Panel: Position Memory
-------------------------------
- Panels remember their position after being closed
- First open: centered on main window
- Subsequent opens: restored to last position
- Move a panel, close it, reopen - it returns where you left it

[December 27 2025]

Research & Resources: Bug Fixes
-------------------------------
- Fixed: Research facilities now properly generate research points each turn
- Fixed: Resource bar updates immediately when income changes
- Fixed: Research window refreshes when you build or scrap facilities
- All resource displays now update in real-time

Construction Queue: Item Selection
----------------------------------
- Click queue items to select them (radio button indicator shows selection)
- Use Move Up/Down/Pause buttons on the selected item
- Small X button on each row to remove items from queue
- Selection persists after moving or pausing items

Facilities Management: View & Scrap
-----------------------------------
- Right-click planet header in construction queue to view existing facilities
- Right-click colonized planet in system view → "Facilities" menu option
- View all built facilities with icon, name, category, and health
- Scrap facilities to recover 50% of build resources (minerals, organics, radioactives)
- Confirmation dialog shows resource return before scrapping

Construction Queue: UI Improvements
-----------------------------------
- Added "Build Category" label above category buttons (Ships/Facilities/Units/Upgrades)
- Planet header tooltip indicates right-click for facilities

[December 26 2025]

Research System: SE4 Alignment
------------------------------
- Added 3 new research areas based on Space Empires 4:
- Engine Overloading Weapons (requires Propulsion 4)
- Gravitational Weapons (requires Astrophysics 2)
- Stellar Harnessing (requires Astrophysics 1)
- Updated component research requirements to match SE4:
- Ionic Disperser: Engine Overloading Weapons 1-5
- Ripper Beam: High-Energy Discharge Weapons 1-4
- Incinerator Beam: High-Energy Discharge Weapons 5-7
- Graviton Beam: Gravitational Weapons 1-5
- Solar Collector: Stellar Harnessing 1

Mod Support Improvements
------------------------
- Mods can now organize content across multiple files
- Fixed issues with research prerequisites not loading correctly

[December 24 2025 8:00pm pacific]

Ship Designer: Component Info Popup
-----------------------------------
- Right-click any component (available or installed) to show a detailed popup
- Displays icon, name, category, description, cost, size, and abilities
- Click anywhere, press Escape, or click away to close the popup
- Icons display with fallback to purple question mark for missing assets

Ship Designer: Bulk Add/Remove
------------------------------
- Hold Shift while clicking to add/remove up to 5 components at once
- Respects capacity limits (stops when ship is full)

Ship Designer: UI Defaults
--------------------------
- Stack View is now enabled by default
- Fixed "Only Latest" filter for size-variant components (Cargo/Drone/Fighter Bays)
These use small/medium/large instead of numeric levels and weren't filtering properly

Component Assets
----------------
Added 15 new SVG icons for components:
- Core: bridge, crew, life support
- Weapons: point defense, shield depleter, ionic disperser, ripper, repulser
- Utility: cargo, fighter bay, drone bay, mine layer, repair bay, solar collector, boarding party

Data Cleanup
------------
- Sorted research_areas.json entries alphabetically by ID

[December 24 2025 3:00pm pacific]

Multi-Star System Support
-------------------------
- Systems can now have 1-5 stars (binary, trinary, quaternary, quinary)
- Stars are positioned intelligently in the 3x3 center grid
- Planets are kept outside the 3x3 center to avoid star collisions
- Ring search algorithm finds valid positions for planets near occupied spaces

Planet Generation Fixes
-----------------------
- Fixed planet-star collision: planets no longer spawn on same grid position as stars
- Fixed gas planets generating without atmosphere despite RequiresAtmosphere=true
- Improved collision avoidance using expanding ring search

Modding: Event Phases
---------------------
- Mod scripts can now hook into events at Pre/During/Post phases
- Enables mods to validate, modify, or react to game events
- See docs/modding/scripting.md for details

[December 23 2025]

Loading Screen
--------------
- New splash screen while the game loads

Under the Hood
--------------
- Script engine improvements for better mod support

[Earlier December 2025]

Cargo Transfer
--------------
- New cargo button for transferring goods between ships and colonies
- Keyboard shortcut: T

Movement Keyboard Shortcuts
---------------------------
- M: Move command
- W: Warp command
- C: Colonize command
- Enter: End Turn

AI Opponents
------------
- Non-playable empires added to opponent pool for more variety
- Improved AI colonization - prioritizes compatible planets and home system

Population Fix
--------------
- Fixed critical bug where homeworld population could crash due to incorrect
max population calculations from planet size