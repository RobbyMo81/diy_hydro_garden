# Greenhouse Solar Thermal Future Enhancement

Date: 2026-05-10

## Purpose

This document captures a possible future enhancement for adding solar-thermal heat buffering to the greenhouse. It is not part of the first hydroponic build.

The goal is to reduce nighttime temperature swings in a Western Washington greenhouse without warming the hydroponic nutrient reservoirs above leafy-green root-zone limits.

## Recommendation

Use water barrels as the primary thermal storage medium. Treat sand as secondary floor mass only.

For a small hydroponic greenhouse, water is safer, simpler, easier to inspect, easier to move, and much more effective per cubic foot than sand at normal greenhouse temperatures.

Do not use hydroponic reservoirs as the heat battery. Leafy greens need cool roots; keep nutrient solution below about 75 F when possible.

## Western Washington Solar Reality

NREL solar-resource data for Western Washington shows that winter sunlight is limited, especially direct beam sunlight.

Values below are daily averages in `kWh/m2/day`.

| Location | Dec DNI | Dec tilted flat-plate | July DNI | July tilted flat-plate |
| --- | ---: | ---: | ---: | ---: |
| Seattle | 1.21 | 1.65 | 6.66 | 5.98 |
| Olympia | 1.23 | 1.66 | 5.50 | 5.69 |
| Bellingham | 1.25 | 1.63 | 5.86 | 5.92 |

`DNI` matters for parabolic troughs because they mostly need direct beam sunlight. Flat black water or air collectors can use more diffuse cloudy light, which may make them a better fit for Western Washington winter.

## 4 ft x 8 ft Collector Check

A `4 ft x 8 ft` collector has:

```text
32 ft2 = 2.97 m2 aperture
```

Seattle December parabolic input:

```text
1.21 kWh/m2/day x 2.97 m2 = 3.59 kWh/day raw direct sun
```

Assuming a conservative DIY useful efficiency of 35-50%:

```text
Useful heat = 1.25-1.80 kWh/day
```

A 55-gallon water barrel needs about:

```text
0.134 kWh to rise 1 F
```

So a `4 ft x 8 ft` collector can raise one 55-gallon barrel by roughly:

```text
1.25-1.80 kWh / 0.134 = 9-13 F
```

That is an average December solar-day estimate, not a guarantee. Several cloudy days may provide little useful trough heat.

## Water vs Sand

University greenhouse guidance and Gemini skeptic verification agree that water stores roughly three times as much useful heat per unit volume as sand, crushed rock, or concrete at greenhouse temperature ranges.

One 55-gallon water barrel:

```text
~459 lb water
~459 BTU per F
```

If warmed by 10 F:

```text
~4,590 BTU stored
```

The same volume of dry sand stores roughly one-third as much useful heat and releases it more slowly unless it has embedded tubing or forced air paths.

### Water Advantages

- Highest practical heat storage per cubic foot.
- Self-mixes heat through convection.
- Easy to inspect.
- Easy to add incrementally.
- Easy to remove or relocate.
- Works passively as greenhouse thermal mass.

### Water Risks

- Leaks.
- Algae if light reaches water.
- Mosquitoes if open.
- Freeze expansion if filled completely.
- Added humidity if uncovered.

Controls:

- Use closed black or opaque HDPE barrels.
- Leave headspace for expansion.
- Keep lids sealed.
- Place on compacted, level support.
- Keep barrels separate from nutrient reservoirs.

### Sand Advantages

- Cannot leak.
- Cheap.
- Can be used under paths or as buried thermal mass.
- Useful for slow floor-temperature stabilization.

### Sand Risks

- Stores much less heat per cubic foot.
- Very heavy.
- Hard to remove later.
- Charges and discharges slowly.
- Can trap moisture.
- Forced-air sand beds can become mold or condensation problems if poorly drained.

## Best Greenhouse Architecture

Use solar thermal only as greenhouse air-temperature support:

```text
Solar collector
-> small pump or fan loop
-> closed water barrels or insulated storage tank
-> passive or fan-assisted nighttime heat release
-> no direct heating of hydroponic reservoirs
```

Place thermal mass:

- Along the north wall if possible.
- Where it receives winter sun.
- Away from DWC buckets.
- On a compacted base, not on a weak bench.
- With airflow around barrels for heat release.

## Parabolic Trough vs Flat Collector

A parabolic trough can make higher temperatures, but it is a poor first greenhouse heater in Western Washington because it depends on direct beam sunlight and can overheat quickly on clear days.

A lower-temperature black flat-plate collector or black poly-pipe collector may be a safer future enhancement because greenhouse heating needs moderate heat, not steam or domestic hot-water temperatures.

If a parabolic trough is tested, keep it:

- Non-potable.
- Low-pressure or drainback.
- Isolated from hydroponic reservoirs.
- Controlled by collector and storage temperature sensors.
- Equipped with a heat dump or pump shutoff.
- Operated only as an outdoor experimental loop until proven safe.

## Control Logic

Minimum useful control:

```text
IF collector temperature > storage temperature + 10 F
THEN turn circulation pump on

IF collector temperature <= storage temperature + 3 F
THEN turn circulation pump off

IF storage temperature > 110-120 F
THEN stop collector pump or dump heat

IF nutrient water > 72-75 F
THEN isolate reservoirs from heat source and increase cooling
```

Do not connect a DIY solar-thermal loop directly to domestic potable hot water without code-compliant plumbing, temperature and pressure relief, expansion control, scald protection, backflow protection, and qualified review.

## Practical Sizing

A single `4 ft x 8 ft` collector plus one 55-gallon barrel is a thermal buffer, not a full greenhouse heater.

For meaningful Western Washington winter buffering, plan closer to:

- 3-6 closed water barrels.
- Multiple low-temperature collectors.
- Greenhouse insulation and air sealing.
- Night curtains or internal row covers.
- Backup heat for hard freezes.
- Monitoring for air temperature, reservoir temperature, and humidity.

## Verdict

Water barrels first. Sand only as secondary floor mass.

For this hydroponic greenhouse, solar thermal should be treated as a later resilience feature that moderates air temperature. It should not be used to heat the DWC nutrient solution.

## Sources

- NREL Solar Resource Maps and Data: https://www.nrel.gov/gis/solar-resource-maps
- NREL Solar Resource Data API: https://developer.nrel.gov/docs/solar/solar-resource-v1/
- University of Connecticut, "Heat Storage for Greenhouses": https://ipm.cahnr.uconn.edu/heat-storage-for-greenhouses/
- UMass Amherst, "Heat Storage for Greenhouses": https://www.umass.edu/agriculture-food-environment/greenhouse-floriculture/fact-sheets/heat-storage-for-greenhouses
