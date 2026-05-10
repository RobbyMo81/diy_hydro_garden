# Greenhouse Automation Path

Date: 2026-05-09

## Recommendation

Automate in layers. Start with monitoring and alerts, then automate low-risk controls, then add top-off safeguards. Do not automate pH or EC dosing in the first build.

The correct first automation goal is not "make it hands-off." The goal is to catch failures early: air pump failure, water temperature drift, reservoir dry-down, light schedule failure, leaks, and greenhouse humidity problems.

Recommended order:

1. Manual baseline and logs.
2. Monitoring and alerts.
3. Low-risk control: lights, fans, and temperature equipment.
4. Guarded water top-off.
5. Optional pH/EC dosing only after stable manual operation and probe calibration discipline.
6. Aquaponics monitoring only after the hydroponic system is stable.

## Gemini Skeptic Verification

Gemini was used as the skeptic verifier for this automation path. Its major corrections were:

- Home Assistant should not be the only survival controller. Critical controls need local fallback logic on the microcontroller.
- Cheap "waterproof" probes may not be food-safe or durable in nutrient solution.
- Washington greenhouse condensation can destroy exposed electronics and humidity sensors.
- Smart plugs should not directly switch high-load heaters, chillers, or compressor loads unless the device rating explicitly supports the load.
- Water top-off needs redundant float switches and a hard runtime limit.
- pH/EC automation can overdose if probes drift, electrical noise affects readings, or mixing delay is ignored.
- pH and EC probes in the same reservoir can interfere without electrical isolation.
- Probe cleaning, storage, and calibration are part of the automation system, not optional maintenance.

Those corrections are included below.

## Automation Philosophy

Use automation to reduce missed failures, not to remove human oversight.

Hard rules:

- The plants must survive if Wi-Fi, Home Assistant, or the internet goes down.
- Critical actions need local control logic on the device.
- Every pump that can add water or chemicals needs a maximum runtime limit.
- Any automatic dosing must have daily maximum limits.
- Alerts are not enough for life-support items such as aeration; use redundancy.
- Manual pH and EC verification remains required even with sensors.

## Phase 0: Manual Baseline

Run the system manually for at least two crop cycles before automatic dosing.

Track:

- pH
- EC
- Water temperature
- Air temperature
- Relative humidity
- Water top-off volume
- Nutrient additions
- Harvest date
- Root condition
- Pest and disease observations

Purpose:

- Learn normal daily drift.
- Identify whether pH rises, falls, or stays stable.
- Identify water consumption per plant.
- Establish what automation should actually solve.

## Phase 1: Monitoring And Alerts

This is the highest-value first automation layer.

Recommended sensors:

| Sensor | Purpose | Notes |
| --- | --- | --- |
| Water temperature | Root-zone heat alert | Use food-safe probe, thermowell, or known-safe wetted materials. |
| Air temperature and RH | Greenhouse climate and mold risk | Use protected, conformal-coated, or enclosure-mounted sensors. |
| Leak sensor | Detect spills, cracked fittings, or overflow | Place below reservoirs and near pumps. |
| Power monitoring | Verify air pump, lights, and fans are actually drawing power | Useful for failure alerts. |
| Camera | Remote plant and water-level sanity check | Do not rely on camera alone. |
| Optional PAR/DLI sensor | Winter light management | PAR is the useful plant-light metric, not lux. |

Useful alerts:

- Air pump power draw is zero or abnormal.
- Water temperature above 75 F.
- Greenhouse RH above target for extended periods.
- Water level low.
- Leak detected.
- Light circuit failed to turn on.
- Sensor offline.

## Phase 2: Low-Risk Controls

Good first controls:

- Lighting schedule.
- Circulation fan.
- Exhaust fan.
- Intake fan.
- Small reservoir heater controlled by a local thermostat.
- Chiller or cooling pump controlled by a purpose-rated controller.

Use Home Assistant for dashboards, logging, and high-level schedules. Use ESPHome or dedicated controllers for local fallback.

Example local fallback requirements:

- If water temperature is too high, turn on cooling output locally.
- If air temperature is too high, turn on exhaust fan locally.
- If Home Assistant is unavailable, keep the last safe light schedule or use a local timer.
- If a sensor is offline, fail to a safe state and alert.

Do not use consumer smart plugs as the only switching device for large heaters, chillers, or compressor loads. For larger loads, use a properly rated controller, contactor, relay, or electrician-installed circuit.

## Phase 3: Guarded Water Top-Off

Automatic top-off is useful, but it can flood the greenhouse or dilute nutrients if built poorly.

Minimum safeguards:

- Use a small top-off reservoir, not unlimited direct mains water.
- Use two float switches: normal low-level trigger and high-level cutoff.
- Add a separate overflow path or catch tray if practical.
- Add a hard maximum pump runtime in local controller logic.
- Add a daily maximum top-off volume.
- Use an air gap to prevent siphon and backflow.
- Alert on every top-off event.

Do not connect a solenoid directly to household water without overflow protection, backflow prevention, and local code review.

## Phase 4: Optional pH And EC Automation

Delay this phase. Automatic pH/EC dosing is where small DIY systems can damage crops fastest.

Minimum prerequisites:

- At least 30 days of stable sensor readings.
- Manual pH/EC readings agree with sensors.
- Weekly probe inspection and cleaning.
- Biweekly calibration during the early automation period.
- Electrical isolation for pH/EC circuits.
- Separate A/B nutrient concentrates if using mineral salts.
- Separate pH up and pH down with interlocks.

Required dosing safeguards:

- Peristaltic pumps only.
- Dose in tiny increments.
- Wait 10-20 minutes after a dose before reading again.
- Set daily maximum dose limits.
- Lock out pH up if pH down has dosed recently, and vice versa.
- Lock out nutrient dosing if water level is low.
- Lock out dosing if sensor readings jump unrealistically.
- Alert on every dose.
- Keep dosing containers small enough that a full-container failure cannot destroy the system.

Recommended stance:

For this project, use automated pH/EC monitoring first and keep pH/EC correction manual until the greenhouse has proven stable.

## Phase 5: Aquaponics Automation

Aquaponics requires additional monitoring and redundancy because fish can die quickly when oxygen, ammonia, nitrite, or temperature move out of range.

Add:

- Dissolved oxygen monitoring or frequent manual DO checks.
- Ammonia and nitrite manual tests.
- Fish tank temperature.
- Backup air pump.
- Battery-backed air pump or inverter.
- Water-flow sensor for circulation.
- Alarm for power failure.
- Camera aimed at fish tank surface behavior.

Do not rely only on nitrate readings. Fish safety depends on ammonia, nitrite, oxygen, temperature, pH, and backup aeration.

## Suggested Architecture

Use a local-first architecture:

| Layer | Role |
| --- | --- |
| ESP32 or similar microcontroller | Local sensor reading and emergency control. |
| ESPHome or MQTT | Device communication. |
| Home Assistant | Dashboard, history, alerts, and orchestration. |
| Smart plugs or relays | Low-load switching and power monitoring. |
| Dedicated controllers/contactors | Higher-load heaters, chillers, pumps, and fans. |
| Manual handheld meters | Calibration and verification. |

Home Assistant is useful, but it should not be required for aeration, temperature survival, or flood prevention.

## Hardware Options

Representative options:

| Item | Role | Rough cost |
| --- | --- | ---: |
| ESP32 development board | Local controller | $5-$15 |
| Raspberry Pi or mini PC | Home Assistant host | $35-$150+ |
| Shelly Plus Plug S or similar power-monitoring plug | Low-load monitoring/control | About $20 each |
| Waterproof temperature probe or thermowell | Water temperature | $5-$30 |
| SHT31/SHT35-class air temp/RH sensor | Greenhouse air monitoring | $10-$30 |
| Leak sensor | Spill detection | $5-$25 |
| Atlas Scientific pH kit | Better pH monitoring | About $160-$175 |
| Atlas Scientific conductivity kit | Better EC monitoring | About $200-$260 |
| Atlas Scientific RTD temperature kit | Higher-quality water temp | About $30-$70 depending kit |
| Atlas Scientific EZO peristaltic pump | Controlled dosing | About $95-$115+ each |
| Atlas Scientific Wi-Fi hydroponics kit | Integrated monitoring starting point | About $570 |
| NEMA 4X / IP66 enclosure | Electronics protection | varies |

Budget sensors are acceptable for monitoring and alerts. Use better probes and calibration solutions before any dosing decisions.

## Automation BOM With Verified Links

Prices were checked from vendor pages on 2026-05-09. They do not include tax, shipping, spare parts, or electrician labor. The skeptical cost ranges below include enclosure, wiring, GFCI, power-supply, calibration, and mounting overhead that is easy to under-budget.

### Tier 1: Basic Monitoring And Alerts

Use this first. It watches the system and alerts you, but it does not add water or chemicals.

| Item | Qty | Est. cost | Link | Notes |
| --- | ---: | ---: | --- | --- |
| ESP32 controller | 1 | $15-$21 | [Adafruit ESP32 Dev Board](https://www.adafruit.com/product/3269) / [Adafruit ESP32 Feather V2](https://www.adafruit.com/product/5438) | Local controller for sensors and fallback logic. |
| Water temperature probe | 1 | $10-$20 | [Adafruit DS18B20](https://www.adafruit.com/product/381) / [Adafruit PTFE DS18B20](https://www.adafruit.com/product/642) | Avoid long-term direct contact if wetted metal is not SS316 or isolated in a thermowell. |
| Air temperature/RH sensor | 1 | $14 | [Adafruit SHT31-D](https://www.adafruit.com/product/2857) | Put in a protected, ventilated enclosure; exposed boards fail in condensation. |
| Leak sensor | 1-2 | $2-$29 each | [Adafruit water sensor](https://www.adafruit.com/product/4965) / [Seeed Grove Water Sensor](https://www.seeedstudio.com/Grove-Water-Sensor.html) / [Seeed IP66 Leak Detector](https://www.seeedstudio.com/Water-Leak-Detector-p-4620.html) | Resistive PCB sensors are cheap but corrode; prefer protected capacitive/industrial sensors for long-term use. |
| Power-monitoring smart plug | 2 | $20-$23 each | [Shelly Plug US Gen4](https://us.shelly.com/collections/smart-plugs) / [Home Depot Shelly Plus Plug US](https://www.homedepot.com/p/327539186) | Use for low-load monitoring. Do not directly switch large heaters/chillers unless rated for that exact load. |
| NEMA 4X / IP66 enclosure | 1 | $28-$66 | [Home Depot small NEMA 4X polycarbonate](https://www.homedepot.com/b/Electrical-Electrical-Boxes-Conduit-Fittings-Weatherproof-Boxes/Polycarbonate/NEMA-4X/N-5yc1vZca29Z1z0ubbvZ1z1pim6) / [Home Depot VEVOR 12x12x6 IP66](https://www.homedepot.com/p/320651988) | Protects electronics from splash and condensation. |
| Cable glands, DIN rail, terminal blocks, ferrules, wire, strain reliefs | 1 set | $100-$150 | Search locally for DIN rail terminal block kit, cable glands, ferrules, UL-rated wire | Gemini flagged this as commonly under-budgeted. |
| GFCI protection | 1 | $17-$40+ | [Aquaponics Source GFCI adapter](https://www.theaquaponicsource.com/shop/grow-lights-supplies/gfci-outlet-adapter/) / local electrical supplier | Use a proper GFCI outlet/circuit for permanent greenhouse power. |
| DC power supplies | 1-2 | $30-$75 | Local electronics/electrical supplier | Use quality 5V and 12V/24V supplies, not random phone chargers. |
| Home Assistant host, if not already available | 1 | $159-$220 | [Home Assistant Green](https://www.home-assistant.io/green/) / [Seeed Home Assistant Green](https://www.seeedstudio.com/Home-Assistant-Green-p-5792.html) / [Adafruit Raspberry Pi 5](https://www.adafruit.com/product/5812) | Optional if you already run Home Assistant. |

Estimated subtotal:

| Scenario | Estimated cost |
| --- | ---: |
| Basic monitoring, existing Home Assistant host | $275-$450 |
| Basic monitoring, new Home Assistant host | $435-$670 |

### Tier 2: Guarded Automatic Top-Off

Add this only after Tier 1 is stable. Use a small top-off reservoir, not unlimited direct household water.

| Item | Qty | Est. cost | Link | Notes |
| --- | ---: | ---: | --- | --- |
| Float switches | 2 | $25-$84 | [Level Sense float switch](https://leakalarms.com/level-sense/level-sense-15-feet-water-level-float-switch-with-mounting-bracket/) / [SMD PP/Nylon float switch](https://www.fluidswitch.com/product/fh08-plastic-horizontal-float-switch-copy/) | Use dual floats: normal trigger and high-level cutoff. |
| Top-off pump | 1 | $25-$31 | [Adafruit peristaltic pump](https://www.adafruit.com/product/1150) / [SparkFun 12V transfer pump](https://www.sparkfun.com/products/10455) | Peristaltic is safer against siphon; transfer pump moves water faster. |
| Relay/driver, power supply, tubing, fittings | 1 set | $30-$75 | Local electronics/hydroponics supplier | Add max runtime in local controller logic. |
| Small top-off reservoir | 1 | $10-$30 | Local food-safe bucket/container supplier | Small reservoir limits damage if a control fails. |

Estimated add-on subtotal: $100-$220.

Required safeguards:

- Dual float switches.
- Maximum pump runtime.
- Maximum daily top-off volume.
- Air gap to prevent siphon and backflow.
- Alert on every top-off event.

### Tier 3: pH/EC Monitoring

This tier monitors chemistry. It still does not dose chemicals automatically.

| Item | Qty | Est. cost | Link | Notes |
| --- | ---: | ---: | --- | --- |
| Atlas Wi-Fi Hydroponics Kit | 1 | $624.99 | [Atlas Wi-Fi Hydroponics Kit](https://atlas-scientific.com/kits/wi-fi-hydroponics-kit/) | Recommended first pro-monitoring path because it includes pH, EC, temperature, calibration supplies, and isolated slots. |
| Atlas pH Kit | 1 | $159.99 | [Atlas pH Kit](https://atlas-scientific.com/kits/ph-kit/) | Alternative component path. Includes isolated carrier board. |
| Atlas Conductivity K 1.0 Kit | 1 | $229.99 | [Atlas conductivity products](https://atlas-scientific.com/conductivity) | Pick probe range appropriate to nutrient EC. |
| Atlas temperature kit | 1 | $29.99-$61.99 | [Atlas hydroponics products](https://atlas-scientific.com/hydroponics/) | Temperature compensation improves pH/EC interpretation. |
| Calibration and storage solutions | 1 set | $80-$120 initial allowance | [Atlas pH calibration](https://atlas-scientific.com/hydroponics/) / [Atlas conductivity calibration](https://atlas-scientific.com/conductivity) | Include pH 4/7/10, EC standards, storage solution, and probe cleaner. |
| Electrical isolation, if not included | as needed | about $30+ per circuit | [Atlas isolated carrier context](https://atlas-scientific.com/kits/ph-kit/) | Required when pH/EC probes share a reservoir. |

Estimated subtotal:

| Path | Estimated cost |
| --- | ---: |
| Atlas Wi-Fi Hydroponics Kit path | $625-$750 including extra calibration/maintenance supplies |
| Component path | $500-$775 depending enclosure, isolation, and calibration choices |

### Tier 4: Automatic pH/EC Dosing Pilot

This is not recommended for the first build. Pilot on one non-critical reservoir only.

| Item | Qty | Est. cost | Link | Notes |
| --- | ---: | ---: | --- | --- |
| Atlas EZO-PMP Basic Kit | 2-4 | $114.99 each | [Atlas EZO-PMP Basic Kit](https://atlas-scientific.com/kits/ezo-pmp-kit/) | Use peristaltic dosing only. |
| Small dosing containers | 2-4 | $20-$60 total | Local lab/hydroponics supplier | Small containers limit overdose volume. |
| Tubing, check valves, mounts, labels | 1 set | $30-$100 | Local hydroponics/aquarium supplier | Label pH up, pH down, Stock A, and Stock B clearly. |

Estimated add-on subtotal: $280-$620, plus Tier 3 monitoring.

Do not buy dosing pumps until monitoring has been stable for at least one full crop cycle.

### Tier 5: Aquaponics Monitoring Add-On

Use only if the project later adds fish. This is not needed for mineral DWC.

| Item | Qty | Est. cost | Link | Notes |
| --- | ---: | ---: | --- | --- |
| Atlas Wi-Fi Aquaponics Kit | 1 | $1,199.99 | [Atlas Wi-Fi Aquaponics Kit](https://atlas-scientific.com/product/wi-fi-aquaponics-kit/) | Includes pH, DO, temperature, EC, CO2, and humidity. |
| Dissolved oxygen kit, if adding separately | 1 | $338-$355 | [Atlas EZO Complete DO Kit](https://atlas-scientific.com/kits/ezo-complete-do-kit/) / [Atlas DO Kit](https://atlas-scientific.com/kits/dissolved-oxygen-kit/) | DO is life-support data for fish. |
| Backup air pump | 1-2 | $25-$100 each | [hygger battery backup air pump](https://www.hygger-online.com/product/hygger-powergo-aquarium-air-pump/) / [Aquatop backup air pump](https://www.aquaticconnect.com/products/aquatop-breza-battery-powered-aquarium-air-pump) | Fish need backup aeration during power failures. |
| Ammonia/nitrite/nitrate manual test kit | 1 | varies | Local aquarium supplier | Manual tests still required. |

Estimated add-on subtotal: $450-$1,400+ depending whether you use a full aquaponics kit.

### Optional PAR/DLI Measurement

Useful if winter production is a real goal.

| Item | Est. cost | Link | Notes |
| --- | ---: | --- | --- |
| Apogee SQ-420X USB quantum sensor | $266.77 | [Apogee SQ-420X](https://www.apogeeinstruments.com/sq-420x-smart-quantum-sensor-usb-output/) | Good economical PAR/DLI logging option. |
| Apogee SQ-500 full-spectrum quantum sensor | $416.12 | [Apogee SQ-500](https://www.apogeeinstruments.com/sq-500-full-spectrum-quantum-sensor/) | Better for LED-heavy measurement. |

Do not use lux or foot-candle meters for plant-light decisions.

## BOM Audit And Safety Warnings

Gemini's skeptic review approved the tiered BOM only with these constraints:

- Wetted probes must be SS316 stainless, plastic, epoxy, glass, or isolated in a thermowell. Standard SS304 probes can pit in nutrient solution.
- pH and EC circuits must be electrically isolated when probes share a reservoir.
- Include calibration and storage fluids in the budget. Budget about $100/year for probe calibration, cleaning, storage solution, and replacements.
- Avoid bare resistive leak sensors for permanent greenhouse use; they are acceptable for cheap early alerts but corrode and false-trigger in humidity.
- Put electronics in NEMA 4X/IP66-style enclosures and add condensation management such as a breather vent or desiccant.
- Keep AC switching physically separated from low-voltage sensor wiring.
- Do not put heat-generating smart plugs in a sealed enclosure with the ESP32.
- Use GFCI-protected power and permanent code-compliant wiring for serious greenhouse use.
- Budget $100-$150 for wiring, DIN rail, ferrules, glands, terminal blocks, labels, and mounting hardware.

## Electrical Standards

Greenhouses combine water, humidity, condensation, and electricity. Treat the automation system as wet-location-adjacent even when it is under cover.

Minimum standards:

- Use GFCI-protected power.
- Keep power strips and controllers off the floor.
- Use drip loops on all cords.
- Use outdoor/wet-location-rated enclosures where condensation or splash is possible.
- Keep low-voltage sensor wiring separated from AC wiring.
- Use strain reliefs where cables enter enclosures.
- Label every plug and circuit.
- Do not overload smart plugs.
- Use properly rated relays/contactors for inductive or high-current loads.

For permanent greenhouse wiring, use a qualified electrician and follow local code.

## Sensor Calibration And Maintenance

Automation maintenance is added maintenance, not free labor.

| Task | Frequency |
| --- | --- |
| Visual check sensors, cables, and enclosures | Weekly |
| Clean pH and EC probes | Weekly or as needed |
| Compare automated pH to handheld/drop test | Weekly |
| Compare automated EC to handheld meter | Weekly |
| Calibrate pH probe | Every 2-4 weeks at first; adjust after drift history is known |
| Calibrate EC probe | Monthly or per manufacturer guidance |
| Replace pH probe storage solution | As needed |
| Test leak sensors | Monthly |
| Test float switch cutoff | Monthly |
| Test power-failure alert | Monthly |
| Test backup aeration | Monthly |

pH probes must not dry out. Store them in proper storage solution, commonly KCl-based solution, according to the probe manufacturer.

## Automation Failure Modes

| Failure | Impact | Mitigation |
| --- | --- | --- |
| Wi-Fi or Home Assistant down | Lost dashboard/control | Local ESPHome fallback logic and local timers. |
| Air pump fails | Root oxygen crash | Power monitoring, backup pump, alarm. |
| Top-off float sticks | Flood or nutrient dilution | Dual floats, max runtime, small top-off reservoir. |
| pH probe drifts | Bad dosing decisions | Manual verification and dose limits. |
| EC probe noise | False nutrient readings | Electrical isolation, stable wiring, manual verification. |
| Pump relay sticks on | Overdose or overflow | Small reservoirs, max runtime, physical cutoff. |
| Condensation enters electronics | Shorts and false readings | IP/NEMA enclosure, drip loops, conformal coating. |
| Light timer fails | Poor winter growth | Power monitoring and DLI logging. |
| Heater/chiller fails | Root disease or slow growth | Temperature alerts and redundant control. |

## Cost Tiers

| Tier | Scope | Estimated cost |
| --- | --- | ---: |
| Basic alerts | Water temp, air temp/RH, leak sensor, smart plug power monitoring, enclosure, GFCI, power, wiring | $275-$450 if Home Assistant already exists |
| Home Assistant monitoring | Basic alerts plus new Home Assistant host | $435-$670 |
| Guarded top-off | Dual floats, pump, top-off reservoir, tubing, relay/driver, runtime limits | Add $100-$220 |
| Climate/light control | Fans, lights, smart relays/plugs, local fallback; excludes major heater/chiller hardware | Add $200-$600 |
| Better pH/EC monitoring | Atlas-class pH and EC probes/circuits, calibration/storage fluids, isolation | $500-$775 or $625-$750 with Atlas Wi-Fi Hydroponics Kit |
| Automatic pH/EC dosing | Better probes plus 2-4 peristaltic pumps, isolation, reservoirs, lockouts | $900-$1,700+ total |
| Aquaponics monitoring | Adds DO, fish tank monitoring, backup power/aeration, manual ammonia/nitrite tests | Add $450-$1,400+ |

The best return for this project is the basic alerts plus climate/light control tier. Automatic dosing has a much higher failure cost.

## Implementation Roadmap

### Stage 1: Monitoring

- Install water temperature sensor.
- Install air temp/RH sensor in protected enclosure.
- Add leak sensor.
- Add power monitoring to air pump and lights.
- Add Home Assistant dashboard and alerts.
- Keep all corrections manual.

### Stage 2: Greenhouse Environment

- Automate fan schedules and temperature thresholds.
- Automate lighting schedule.
- Add DLI tracking if winter production matters.
- Add high-humidity alerts.

### Stage 3: Water Resilience

- Add guarded automatic top-off from a small reservoir.
- Add dual float switches.
- Add runtime limits and top-off event logging.

### Stage 4: Nutrient Instrumentation

- Add pH and EC probes for monitoring.
- Add electrical isolation.
- Build calibration routine.
- Keep dosing manual until trust is earned.

### Stage 5: Dosing Pilot

- Pilot dosing on one non-critical reservoir only.
- Use small dosing bottles.
- Dose tiny increments with long mixing delays.
- Keep daily maximum limits low.
- Review logs before expanding.

## Verdict

Automate monitoring first. Automate fans, lights, and alerts second. Add guarded top-off third. Delay automatic pH/EC dosing until the system has stable logs, high-quality probes, isolation, calibration discipline, and local failsafes.

For a small food-producing DWC greenhouse, the most valuable automation is failure detection, not chemical dosing.

## Sources

- Cornell Controlled Environment Agriculture, Hydroponic Lettuce Handbook: https://studylib.net/doc/18690167/hydroponic-lettuce-handbook---cornell-controlled-environment
- Cornell Controlled Environment Agriculture, Greens resources: https://cea.cals.cornell.edu/crops/greens/
- University of Minnesota Extension, Small-scale hydroponics: https://extension.umn.edu/how/small-scale-hydroponics
- Oklahoma State University Extension, Electrical Conductivity and pH Guide for Hydroponics: https://extension.okstate.edu/fact-sheets/electrical-conductivity-and-ph-guide-for-hydroponics
- Purdue, Guide to Home Hydroponics for Leafy Greens: https://www.purdue.edu/hla/sites/master-gardener/wp-content/uploads/sites/9/2022/10/Guide-To-Home-Hydroponics-For-Leafy-Greens-Ronzoni-and-Mattson-2020.pdf
- Home Assistant ESPHome integration: https://www.home-assistant.io/integrations/esphome/
- Home Assistant MQTT documentation: https://www.home-assistant.io/docs/mqtt/
- ESPHome MQTT component: https://esphome.io/components/mqtt
- Atlas Scientific hydroponics products: https://atlas-scientific.com/hydroponics/
- Atlas Scientific conductivity circuit: https://atlas-scientific.com/embedded-solutions/ezo-conductivity-circuit/
- Atlas Scientific peristaltic pumps: https://atlas-scientific.com/peristaltic/
- Atlas Scientific Wi-Fi Hydroponics Kit: https://atlas-scientific.com/kits/wi-fi-hydroponics-kit/
- Atlas Scientific Wi-Fi Aquaponics Kit: https://atlas-scientific.com/product/wi-fi-aquaponics-kit/
- Atlas Scientific Dissolved Oxygen Kit: https://atlas-scientific.com/kits/dissolved-oxygen-kit/
- Adafruit ESP32 products: https://www.adafruit.com/product/3269
- Adafruit DS18B20 water temperature sensor: https://www.adafruit.com/product/381
- Adafruit SHT31-D temperature/RH sensor: https://www.adafruit.com/product/2857
- Home Assistant Green: https://www.home-assistant.io/green/
- Shelly smart plugs: https://us.shelly.com/collections/smart-plugs
- Home Depot NEMA 4X / IP66 enclosures: https://www.homedepot.com/b/Electrical-Electrical-Boxes-Conduit-Fittings-Weatherproof-Boxes/Polycarbonate/NEMA-4X/N-5yc1vZca29Z1z0ubbvZ1z1pim6
- Apogee quantum sensors: https://www.apogeeinstruments.com/quantum/
