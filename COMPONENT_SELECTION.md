# SMD component and placement strategy

## Scope and constraints

This project already contains a populated PCB with both front-side and back-side parts. The current board outline is approx. 69 x 90 mm and there are 138 footprint instances in the PCB file; the placement is not a simple single-side pass. Reworking the layout into a strict one-side SMD strategy is therefore a board-level redesign, not a small component swap.

The change set below keeps the design safe and makes the project ready for a proper relocation/routing pass without breaking the existing net connectivity or forcing an unsafe component flip.

## Recommended component strategy

- Prefer SMD equivalents for passive and low-voltage parts where the electrical rating and footprint are compatible.
- When the same value is available in multiple voltage ratings, keep the highest voltage rating that still fits the target package and BOM consolidation goal.
- Prefer one family and one package per value if it reduces BOM count and still keeps the layout consistent.
- For current-sense shunts and power elements, keep the package on one side of the power stage and route the high-current path using wide copper and short traces.

## Placement rules for this board

1. Keep the power stage together on one side of the board and reserve the control section for logic and signal conditioning.
2. Keep the Bluetooth/RF section physically separated from the switching power stage and keep the ground under the module as the vendor datasheet requires.
3. Place MCU/control circuits away from the switching nodes and with a clean analog/common return path.
4. Keep high-current loops short and use the `HighCurrent` netclass for the main power path.
5. Keep the opposite side mostly as a ground/reference layer plus required transitions; do not use it as a random second placement area unless the routing and thermal review show it is safe.
6. Use the same package family within a zone to minimize footprint variation and improve assembly consistency.

## Updated board rules

The project rules have been tightened to support a realistic autoroute and power-stage layout:

- Default clearance: 0.2 mm
- Default track width: 0.2 mm
- High-current clearance: 0.5 mm
- High-current track width: 1.5-2.0 mm
- Keep a small but non-zero silk clearance to avoid overlay issues
- Keep via and hole constraints compatible with the chosen SMD footprint set

These values are more realistic for a compact power driver board than the default 0.1 mm / 0.2 mm rule set that does not match the project’s switching/current requirement.

## 3D model / footprint guidance

- Use the manufacturer or KiCad library footprint whenever the exact symbol and package are already available.
- If the exact library part is not present in the repository, use a generic compatible footprint rather than leaving a broken or non-portable absolute path reference.
- Do not keep local absolute paths to `KiCad/mouser` or other machine-specific directories in the PCB footprint model field.
- For missing 3D models, use a standard generic model for the package family and keep the project portable.

## Safe execution plan

This board should not be physically flipped or re-routed in a single blind update. A correct single-side optimization requires:

1. Recheck the schematic against the PCB for each net and symbol reference.
2. Group the power stage on the main copper side and place the logic blocks in the designated control zone.
3. Re-route the switching and current-sense nets with the updated HighCurrent rules.
4. Validate spacing, copper clearance, and thermal relief before finalizing the assembly drawings.

This preserves electrical correctness while keeping the design aligned with the requested SMD and single-side strategy.
