# Greenhouse Hydroponic System TL;DR

Date: 2026-05-09

## Purpose

Build a year-round greenhouse system for leafy greens using food-contact hydroponic hardware, conservative nutrient management, and monitoring-first automation. The starting point is mineral-nutrient deep water culture (DWC), not aquaponics or automatic chemical dosing.

## Physical System

Use either modular food-safe `#2 HDPE` 5-gallon bucket DWC modules or compact NSF food-service `#5 PP` / `#2 HDPE` reservoirs. Each plant site uses a drilled lid, net cup, starter plug, continuous aeration, air-line check valve, and light-blocked reservoir. Light-blocking is mandatory because translucent food-service containers will grow algae if nutrient solution sees light.

Use one full-size leafy green per 5-gallon bucket, or 4-6 small greens in a compact food-service reservoir. The bucket layout is easier to isolate, sanitize, and expand; the shared reservoir is simpler to manage but shares crop and water problems across all plants.

## Food-Safe Materials

Use only containers and wetted parts that are explicitly food-contact appropriate. Prefer `#2 HDPE`, `#5 PP`, NSF food-service containers, FDA-compliant pails, food-safe lids, silicone or food-safe tubing where applicable, and known-safe probe materials.

Avoid polycarbonate, unknown `#7`, PVC `#3`, polystyrene `#6`, recycled-content containers with unknown origin, old or smelly plastic, and anything that previously held non-food chemicals.

## Crops

Best first crops are lettuce, bok choy, kale, mustard greens, basil, cilantro, parsley, and similar leafy greens. Do not mix crops with very different EC needs in one shared reservoir during early operation. Heat-sensitive greens may bolt or turn bitter if the greenhouse and root zone get too warm.

## Water And Nutrients

Use potable municipal or filtered water. Test source water before locking in a nutrient recipe because alkalinity, hardness, chlorine, and chloramine can affect pH drift and plant response.

Targets:

| Parameter | Target |
| --- | --- |
| pH | 5.8-6.5 |
| EC for seedlings | 0.8-1.2 mS/cm |
| EC for established lettuce | 1.2-1.8 mS/cm |
| Water temperature | Keep below about 75 F |
| Air pump | Run 24/7 |

Start with a commercial one-part leafy-green nutrient. DIY mineral salts are the preferred hardening path later. If making concentrates, keep calcium separate from phosphate and sulfate concentrates with A/B stock discipline. Do not use compost tea, manure extracts, worm leachate, fish emulsion, or other biologically active homemade extracts in recirculating raw leafy-green water.

## Environment

Reservoir cooling is part of the base design. Shade, insulate, bury, or increase water volume if the nutrient solution repeatedly approaches 75 F. Use insect netting, airflow, sanitation, and humidity control to reduce pest and disease pressure.

The phrase "year-round" requires winter light planning in Washington. Measure or estimate DLI and add supplemental greenhouse lighting if winter production must be reliable.

## Cost And Build Time

Base hydroponic build estimates, excluding tax, shipping, greenhouse structure, heat, cooling, benches, and supplemental lighting:

| System | Estimated cost | Notes |
| --- | ---: | --- |
| 4-bucket food-safe HDPE DWC | $207-$268 | Modular first production setup |
| 8-bucket food-safe HDPE DWC | $282-$364 | Better cost per plant after trial |
| Compact food-service PP/HDPE reservoir | $162-$236 | Simpler shared reservoir |
| Hole saw and GFCI cord, if needed | Add $47-$60 | Common first-build tools |
| Premium meter upgrade | Add $85-$110 | Better EC and pH reliability |

Hands-on build time is about 2-3 hours. First plantable setup is usually the next day because water should mix, aerate, stabilize, and be rechecked before transplant.

## Maintenance

Estimated weekly maintenance after the system is stable:

| System | Time |
| --- | ---: |
| 4-bucket food-safe HDPE DWC | 1.5-3 hr/week |
| 8-bucket food-safe HDPE DWC | 2.5-5 hr/week |
| Compact food-service PP/HDPE reservoir | 1-2.5 hr/week |

Daily checks take 5-10 minutes: bubbles, water level, plant health, pests, algae, odor, and water temperature. Check pH and EC two to three times per week. Change or refresh nutrient solution every one to two weeks, or sooner if pH/EC becomes unstable, roots brown, algae grows, or water smells bad.

## Automation

Automate in layers. The first goal is failure detection, not hands-off growing.

| Layer | Scope | Estimated hardware cost |
| --- | --- | ---: |
| Basic alerts | Water temp, air temp/RH, leak sensor, power monitoring, enclosure, GFCI, wiring | $275-$450 with existing Home Assistant |
| New Home Assistant host | Adds local dashboard host | $435-$670 total |
| Guarded top-off | Small top-off reservoir, redundant float switches, pump, runtime limits | Add $100-$220 |
| pH/EC monitoring | Better probes, isolation, calibration and storage supplies | $500-$775, or $625-$750 kit path |

Hard gates: plants must survive Wi-Fi or Home Assistant failure, critical controls need local fallback, top-off needs redundant float switches plus runtime limits, and automatic pH/EC dosing is not part of the first build. Add automatic dosing only after stable logs, high-quality isolated probes, calibration discipline, tiny dose increments, mixing delays, and daily maximum limits.

## Roadmap

1. Build and stabilize a food-safe mineral DWC system.
2. Record pH, EC, water temperature, air temperature, humidity, top-off volume, pest pressure, and harvest results for at least two crop cycles.
3. Add monitoring and alerts before adding control.
4. Add winter lighting, airflow, humidity control, and reservoir cooling as needed for true year-round production.
5. Harden nutrients with measured mineral salts and A/B concentrate separation.
6. Consider aquaponics only as a separate experiment after confirming Washington WDFW fish transport, aquatic farm, and discharge requirements.

Aquaponics is not the first reliability upgrade. Trout are the more plausible Washington food-fish candidate but need cold, oxygen-rich water and backup aeration. Tilapia are a poor first fit because their warm-water preference conflicts with leafy-green root-zone limits and Washington regulatory checks are required.

## Safety

Use GFCI-protected power, drip loops, raised power strips, sheltered air pumps, check valves on every air line, labeled chemicals, gloves and eye protection for pH products and mineral salts, and sanitation between crops. Keep pH products, nutrient salts, and electrical gear away from children, pets, direct sun, greenhouse heat, and standing water. Wash greens before eating.
