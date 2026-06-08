# BOARD DESIGN SPECIFICATION
## Demolition Derby — Phase 1 Physical Board

**Document Status:** Active — Phase 1 decisions locked as of June 8, 2026
**Engineers:** Dwain Henderson Jr. (board visualization, layout, transport) · David (software, relay firmware)

---

## Overview

Each engineer builds one identical 4×6 project board independently. The boards are designed to be portable, visually appealing, and functional as standalone demonstration units. When both engineers meet, the boards are placed side-by-side and wired together to operate as a unified system.

The board is the physical manifestation of the Demolition Derby program. It is not a prototype — it is the first production iteration. It should look like something you would want to show people.

---

## Physical Dimensions

| Attribute | Specification |
|-----------|--------------|
| Width | 4 feet (48 inches) |
| Length | 6 feet (72 inches) |
| Height (operating) | Waist height on card table or round table |
| Orientation | Flat / horizontal |
| Portability | Must fit in a vehicle; designed for transport to meet-up location |

---

## Structure Zones

Three structure pads are permanently pre-wired on the board. Each pad is sized for a specific domino structure and has a relay-fired charge point at its base.

| Zone | Size | Relay | Structure (Phase 1) | Notes |
|------|------|-------|-------------------|-------|
| **Large Pad** | ~18" × 18" | R1 | Large domino tower / building | Primary visual centerpiece |
| **Medium Pad** | ~12" × 12" | R2 | Medium domino structure | Secondary structure |
| **Small Pad** | ~8" × 8" | R3 | Small domino cluster | Accent structure |
| **Reserve** | — | R4 | Unassigned Phase 1 | Available for scenery effect or second small structure |

City scenery, roads, and infrastructure (toy vehicles, miniature trees, signage) are placed adjacent to the pads to create context and visual interest. Scenery is not wired — it is set dressing.

---

## Phase 1 Building Materials

**Decision (June 8, 2026):** Wooden dominoes only for Phase 1. No concrete, no plaster, no frangible resin. The goal is to prove the relay-trigger system works end-to-end. Structure complexity is intentionally minimal.

![201-piece Colorful Domino Building Blocks](docs/assets/dominos-colorful-201pc.png)

**Purchased:** 201-piece Colorful Domino Building Blocks (Temu, $15.00 per engineer)
[Product Link](https://www.temu.com/201-colorful-domino-building-blocks-a-fun-stacking-and-construction-toy--halloween-birthdays-and-christmas-gifts-educational--colors-may--g-605976816111107.html)

The colorful wooden dominoes serve a dual purpose: they are the building material and the visual indicator of the collapse event. When the relay fires, the structure falls — the dominoes tell the story.

---

## Relay Board Mounting

The relay board (4-channel, Phase 1) is permanently mounted on the board in a visible, styled location. The aesthetic goal is "instrument panel" — not hidden inside a box, but displayed as part of the design. Trace wires run exposed along the board surface in a clean, intentional pattern that is part of the visual design.

Wiring runs from the relay board to pre-wired pads at each structure zone. Each pad has a clearly labeled connection point so structures can be placed and removed without re-wiring.

**Relay board candidates (Phase 1):**
- ESP32-based relay module (4-channel, 5V coil, optocoupler isolated)
- T-Display S3 as the local controller / display
- USB-C power from portable battery bank for portability

---

## Transport Design

The board must be transportable by one person. Design considerations:

- Lightweight substrate (foam-core board, thin plywood, or PVC foam sheet) preferred over heavy plywood
- Fold-flat or two-piece design considered for Phase 2
- Carry handles or shoulder strap mount points recommended
- Structures (dominoes) are removed for transport and re-set on arrival
- Relay board and wiring remain permanently mounted

---

## Future Phase Additions (Not Phase 1)

The following items are planned for later phases and are noted here so the board design accounts for their eventual integration:

| Addition | Phase | Notes |
|----------|-------|-------|
| IP cameras (overhead + street-level) | Phase 3 | Mount points should be included in Phase 1 board design |
| Accelerometer / IMU sensors at each pad | Phase 3 | Sensor pad attachment points pre-wired |
| Expanded relay channels (up to 16) | Phase 2 | Relay board footprint should allow for expansion |
| Second building material iteration | Phase 2 | Concrete/plaster structures replace dominoes at one pad |
| Blender 3D reconstruction | Phase 4 | Driven by sensor data from Phase 3 |

---

## Board Aesthetic Goals

The board should look like a piece of equipment, not a school project. Reference aesthetics:

- **Instrument panel / control board** — relay board visible, wiring intentional
- **Diorama quality** — structures and scenery look deliberate and finished
- **Portable and rugged** — nothing loose, nothing that falls off in transit
- **Photogenic** — the board should look good in photos and video

---

## Notes & Open Questions

- [ ] Confirm substrate material (foam-core vs. thin plywood) — Dwain to decide
- [ ] Confirm relay board model — David to specify
- [ ] Determine charge point design at each pad (clip terminal? solder pad? banana jack?)
- [ ] Decide on scenery items to purchase for Phase 1 (roads, vehicles, trees)
- [ ] Confirm transport method (vehicle, case, bag)

---

*Last updated: June 8, 2026 — Dwain Henderson Jr.*
