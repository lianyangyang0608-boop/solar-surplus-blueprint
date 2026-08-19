# ☀️ PV Surplus Heat Pump Controller
### Heat water with excess solar energy using Home Assistant

Hey everyone!

I built a Home Assistant automation that uses excess solar energy to heat my water instead of exporting it back to the grid.

The idea is simple: when my PV system produces more power than the house needs, Home Assistant automatically turns on the heat pump water heater and stores that energy as hot water.

## 🎯 The idea

I have a PV system, and on sunny days I was exporting a lot of power back to the grid at a pretty low rate. Meanwhile my heat pump water heater was running on a fixed evening schedule, buying electricity at peak rates. It didn't make sense.

So I built this: whenever there's enough solar surplus, the heat pump turns on and heats the water for free. Hot water stores well, so it's basically banking that excess solar energy.

<div align="center">
<img src="./images/Scene diagram.png" width="500">
</div>

## ⚡ How it works

The logic is simple but effective:

- **Grid power < -900W for 60s** → Turn the heat pump ON (you're exporting more than 900W)
- **Grid power > -10W for 60s** → Turn the heat pump OFF (surplus is basically gone)
- An `input_boolean` tracks the current state, so it never toggles twice by accident

The 890W gap between thresholds + 60s delay means no rapid cycling, even when clouds pass over.

<div style="display:flex; justify-content:center; gap:16px; flex-wrap:wrap; margin: 12px 0;">
  <img src="./images/step-on-1.png" width="200">
  <img src="./images/step-off-1.png" width="200">
</div>

## 🔥 Key Features

- **Grid Power Trigger** — Uses your existing grid power sensor, negative = exporting
- **Dual Thresholds** — Separate ON/OFF values create hysteresis and prevent oscillation
- **State Memory** — input_boolean tracks whether the pump is currently running, avoids duplicate toggles
- **Anti-Cycling Delay** — Configurable duration before any action, filters out short power fluctuations
- **Fully Configurable** — All thresholds, timing, and entities are user-selectable

## 📊 How it performs in practice

Here's a typical sunny day at my place:

You can see the heat pump kicks in right when export ramps up, and shuts off as soon as surplus drops. No grid power used for heating that day.
<div align="center">
 <img src="./images/diagram.png" width="500">
</div>

## 📋 What you need

1. A **grid power sensor** — any smart meter or inverter that reports total active power (negative = exporting)
2. A **switch entity** — whatever controls your heat pump (finger robot, smart relay, etc.)
3. An **input_boolean helper** — for state tracking, create one in Settings → Devices & Services → Helpers → Create Helper → Toggle

## ⚙️ Configuration options

| Option | Description | Default |
|--------|-------------|---------|
| Grid Power Sensor | Your grid power entity | — |
| Heat Pump Switch | Switch that toggles the pump | — |
| State Tracker | input_boolean for state memory | — |
| Turn On Threshold | Grid power below this = turn on | -900W |
| Turn Off Threshold | Grid power above this = turn off | -10W |
| Trigger Duration | Seconds the condition must hold | 60s |

<div align="center">
  <img src="./images/config-ui.png" width="500">
</div>


## 🚀 Setup guide

1. **Create the helper** — Go to Settings → Devices & Services → Helpers → Create Helper → Toggle. Name it something like "Heat Pump PV State".
2. **Import the blueprint** — Click the Import button below, or paste the GitHub raw URL into Settings → Automations & Scenes → Blueprints → Import Blueprint.
3. **Create automation** — Find "PV Surplus Heat Pump Controller" in your blueprints list, click "Create Automation".
4. **Fill in the fields** — Select your grid power sensor, heat pump switch, and the input_boolean you just created.
5. **Tune thresholds** — Start with the defaults (-900W / -10W / 60s), adjust based on your heat pump's power draw and how stable your solar output is.
6. **Save and test** — Wait for a sunny day, or manually trigger by adjusting thresholds temporarily.


- **Size the ON threshold to your pump.** The ON threshold should be set slightly higher than your heat pump's typical power consumption to avoid importing electricity from the grid. For example, if your heat pump uses around 700W, an ON threshold of -650W to -800W is a reasonable starting point.
- **Don't set the OFF threshold too low.** If you set it to -500W, the pump will shut off while you're still exporting 500W — wasteful. -10W means it runs until the very last watt of surplus is gone.
- **Raise your water heater setpoint.** I set mine to 60°C so one heating session lasts through the evening.
- **Time-of-use users:** If your off-peak rate is cheaper than your export rate, add a time condition so this only runs during peak-price hours.

## 📥 Import
[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fraw.githubusercontent.com%2Flianyangyang0608-boop%2Fsolar-surplus-blueprint%2Frefs%2Fheads%2Fmain%2Fsolar_surplus_switch_control.yaml)

**GitHub:** https://raw.githubusercontent.com/lianyangyang0608-boop/solar-surplus-blueprint/refs/heads/main/solar_surplus_switch_control.yaml

## 📝 Changelog

**v1.0** — Initial release
- Grid power trigger with dual thresholds
- input_boolean state tracking
- Configurable anti-cycling delay
- Full UI configuration

---

Been running this for a few weeks now and it's solid. If anyone has ideas for improvements (water temp limit, time restrictions, battery SOC awareness) I'd love to hear them — still iterating on it myself.
