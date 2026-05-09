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
| Basic alerts | Water temp, air temp/RH, leak sensor, smart plug power monitoring | $75-$200 |
| Home Assistant monitoring | Basic alerts plus dashboard/history | $150-$350 if no host exists |
| Climate/light control | Fans, lights, smart relays/plugs, local fallback | $200-$600 |
| Better pH/EC monitoring | Atlas-class pH and EC probes/circuits | $350-$600+ |
| Automatic pH/EC dosing | Better probes plus 2-4 peristaltic pumps, isolation, reservoirs | $700-$1,500+ |
| Aquaponics monitoring | Adds DO, fish tank monitoring, backup power/aeration | $500-$2,000+ |

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
- Shelly Plus Plug S: https://us.shelly.com/products/shelly-plus-plug-s-white
