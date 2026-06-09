# Predbat Setup in Home Assistant

## Huawei Solar, Nordpool, Solcast, go-eCharger and Home Assistant Automations

## Overview

This repository documents my personal Home Assistant setup for using **[Predbat](https://github.com/springfall2008/predbat)** with a Huawei inverter, Huawei battery, Nordpool electricity prices, Solcast solar forecasts and a go-eCharger EV charger.

**Predbat** is a Home Assistant/AppDaemon-based tool that predicts and optimizes home battery charging and discharging based on electricity prices, solar forecasts, household load and other energy data.

The goal of this setup is to:

* charge the home battery when electricity is cheap,
* discharge or export when electricity prices are high,
* use solar production more intelligently,
* prevent the EV charger from draining the home battery,
* reduce unnecessary battery cycling,
* protect the main fuse by limiting battery charging during high house load,
* and provide a documented example for other Huawei Solar users.

This is my own working setup and should be treated as an example, not a universal plug-and-play solution.

---

## Disclaimer

I am not an expert in programming, energy management, Huawei Solar, Predbat or Home Assistant.

This setup is based on my own testing, troubleshooting and experimentation. It works in my installation, but it has **not been fully reviewed or tested across all Huawei inverter, Huawei battery, Home Assistant or Predbat setups**.

Use this configuration at your own risk.

Before enabling active control:

1. verify all entity IDs in Home Assistant,
2. start Predbat in monitor/read-only mode,
3. test all service calls manually,
4. use low power limits during initial testing,
5. monitor the inverter, battery and Home Assistant logs carefully.

I take no responsibility for issues, incorrect behaviour, energy cost, battery wear, service call errors or damage caused by using this setup.

---

## My System

### Home Assistant

Home Assistant is used as the central platform for:

* energy monitoring,
* sensor aggregation,
* automation logic,
* MQTT control,
* Lovelace dashboards,
* and Predbat/AppDaemon integration.

### Predbat

Predbat is used for battery planning and optimization.

Key functions used:

* charge/discharge planning,
* export planning,
* import/export price optimization,
* car charging planning,
* Manual API overrides,
* battery reserve handling,
* and plan/status sensors for dashboards.

### Huawei Solar

The setup uses a Huawei inverter and a Huawei battery through the Home Assistant Huawei Solar integration.

In my setup:

* battery SOC is read from Huawei battery sensors,
* battery power is inverted for Predbat,
* max charge/discharge power is controlled through Huawei number entities,
* forcible charge/discharge services are used for active control,
* `stop_forcible_charge` is used as the stop command for both charge and discharge modes.

The setup uses Predbat inverter type:

```yaml
inverter_type: "HU"
```

### Battery

My battery is a Huawei battery with approximately 10 kWh usable capacity.

I currently use:

```yaml
reserve:
  - 12

battery_min_soc:
  - 12
```

This means Predbat should plan around 12% as the minimum battery reserve.

### Nordpool

Nordpool electricity prices are used for import and export price optimization.

Predbat receives import and export price data through Home Assistant sensors.

### Solcast

Solcast is used for solar production forecasts.

Predbat uses the solar forecast to decide when to charge, discharge, export or preserve battery energy.

### go-eCharger

A go-eCharger is used for EV charging.

Predbat decides when the car should charge through:

```yaml
binary_sensor.predbat_car_charging_slot
```

A Home Assistant automation sends MQTT commands to the go-eCharger to enable or disable charging depending on Predbat’s car charging slot.

---

## What This Repository Contains

This repository contains my current working examples for:

* Predbat `apps.yaml`,
* Huawei Solar service templates,
* Home Assistant automations,
* go-eCharger control,
* Predbat Manual API overrides,
* P1 meter based charging limitation,
* Lovelace dashboard cards,
* and supporting Home Assistant helpers/sensors.

---

## Huawei Solar Control

The Huawei integration is controlled using Predbat service templates.

Example:

```yaml
charge_start_service:
  service: huawei_solar.forcible_charge_soc
  device_id: YOUR_HUAWEI_DEVICE_ID
  target_soc: "{target_soc}"
  power: "{power}"

charge_stop_service:
  service: huawei_solar.stop_forcible_charge
  device_id: YOUR_HUAWEI_DEVICE_ID

discharge_start_service:
  service: huawei_solar.forcible_discharge_soc
  device_id: YOUR_HUAWEI_DEVICE_ID
  target_soc: "{target_soc}"
  power: "{power}"

discharge_stop_service:
  service: huawei_solar.stop_forcible_charge
  device_id: YOUR_HUAWEI_DEVICE_ID
```

In my installation, the Huawei `device_id` must be included directly in each service block. Using `!secret` or a separate `device_id` list did not work reliably in my Predbat configuration.

---

## Battery Power Sign Convention

In my Huawei setup, the raw Huawei battery power sensor has the opposite sign compared to what Predbat expects.

Therefore I use:

```yaml
battery_power:
  - sensor.batteries_charge_discharge_power

battery_power_invert:
  - true
```

With this configuration, Predbat shows:

```text
positive battery_power = battery discharging
negative battery_power = battery charging
```

This has been verified in my installation.

---

## Demand, Charge and Export Behaviour

The basic Huawei behaviour tested so far is:

| Predbat status | Expected behaviour                                 |
| -------------- | -------------------------------------------------- |
| Demand         | Normal operation. Battery may support the house.   |
| Charging       | Predbat starts Huawei forcible charge.             |
| Exporting      | Predbat starts Huawei forcible discharge/export.   |
| Stop           | Predbat calls `huawei_solar.stop_forcible_charge`. |

This basic control path is currently the most important part of the setup.

---

## Freeze and Hold Modes

Predbat also supports freeze and hold-style behaviour, but I currently treat this as experimental for Huawei.

Potential Huawei mapping:

| Predbat mode         | Possible Huawei action                           |
| -------------------- | ------------------------------------------------ |
| Hold / Freeze charge | Set maximum discharging power to `0 W`           |
| Freeze export        | Set maximum charging power to `0 W`              |
| Demand               | Restore charge/discharge limits to normal values |

For now, I have chosen to keep freeze modes disabled until the basic Huawei control has been tested over time.

Recommended starting point:

```text
Set Charge Freeze = OFF
Set Export Freeze = OFF
Set Freeze Export during Demand = OFF
Set discharge During Charge = OFF
```

Once the basic setup is stable, freeze/hold behaviour can be tested carefully.

---

## go-eCharger Control

The go-eCharger is controlled from Predbat using:

```yaml
binary_sensor.predbat_car_charging_slot
```

When Predbat wants the car to charge, the sensor turns on.
When the car should not charge, the sensor turns off.

Example MQTT automation:

```yaml
alias: Predbat Car Charging Slot go-e
description: Startar eller stoppar go-eCharger beroende på Predbats laddslot.
mode: single

triggers:
  - trigger: state
    entity_id: binary_sensor.predbat_car_charging_slot

conditions: []

actions:
  - variables:
      goe_charger_id: "265216"
      goe_topic: "go-eCharger/{{ goe_charger_id }}/frc/set"

  - choose:
      - conditions:
          - condition: state
            entity_id: binary_sensor.predbat_car_charging_slot
            state: "on"
        sequence:
          - action: mqtt.publish
            data:
              topic: "{{ goe_topic }}"
              payload: "2"
              qos: 0
              retain: false

      - conditions:
          - condition: state
            entity_id: binary_sensor.predbat_car_charging_slot
            state: "off"
        sequence:
          - action: mqtt.publish
            data:
              topic: "{{ goe_topic }}"
              payload: "1"
              qos: 0
              retain: false
```

Change this value to match your own go-eCharger ID:

```yaml
goe_charger_id: "265216"
```

---

## Preventing the Car from Draining the Home Battery

For EV charging, these Predbat settings are important in my setup:

```text
Allow car to charge from battery = OFF
Car charging hold = ON
Car energy is reported in load data = ON
Car Charging Plan Smart = ON
Octopus Intelligent Charging = OFF
```

This allows Predbat to plan EV charging while trying to avoid using the home battery to charge the car.

---

## P1 Meter Based Charge Limiting

I use P1 meter data to reduce Huawei battery charging during high house load.

This is useful when cooking, charging the car, running heating loads or when one phase approaches the main fuse limit.

My P1 sensors include:

```yaml
sensor.p1_meter_current_phase_1
sensor.p1_meter_current_phase_2
sensor.p1_meter_current_phase_3
sensor.p1_meter_current
sensor.p1_meter_power
```

The logic is:

```text
Normal daytime battery charge limit: 4000 W
High house load or high phase current: reduce battery charging to 1000 W
Evening/night: remove the daytime limit
Discharge is not limited by this automation
```

This is handled through the Predbat Manual API, for example:

```yaml
option: "inverter_limit_charge(0)=4000"
```

and:

```yaml
option: "inverter_limit_charge(0)=1000"
```

Only the battery charge limit is changed.
The discharge limit is left untouched so the home battery can still support the house.

---

## Predbat Manual API

Predbat’s Manual API is used for temporary overrides, such as limiting charge power during high load.

Example:

```yaml
action: select.select_option
target:
  entity_id: select.predbat_manual_api
data:
  option: "inverter_limit_charge(0)=4000"
```

To remove a specific override:

```yaml
action: select.select_option
target:
  entity_id: select.predbat_manual_api
data:
  option: "[inverter_limit_charge(0)=4000]"
```

Avoid using:

```yaml
option: "off"
```

unless you intentionally want to clear all Manual API overrides.

---

## Dashboard

I use Lovelace cards to monitor:

* Predbat status,
* Huawei battery SOC,
* Predbat battery power,
* Huawei max charge power,
* Huawei max discharge power,
* car charging slot,
* active Manual API override,
* and the Predbat plan summary.

The Predbat plan summary is available from:

```yaml
predbat.plan_html
```

Example markdown card:

```yaml
type: markdown
title: Predbat analysis
content: |
  {{ state_attr('predbat.plan_html', 'text') }}
```

The full HTML table can also be displayed, but it can be very large:

```yaml
type: markdown
title: Predbat plan
content: |
  {{ state_attr('predbat.plan_html', 'html') }}
```

---

## Current Status

### Working / Tested in My Setup

* Predbat reads Huawei SOC correctly.
* Predbat reads Huawei battery power correctly.
* `battery_power_invert: true` gives the correct Predbat sign convention.
* Huawei forcible charge works manually.
* Huawei forcible discharge works manually.
* Predbat stop services now send the correct `device_id`.
* Predbat can enter export mode and control Huawei discharge.
* go-eCharger can be controlled from Predbat car charging slot.
* P1 meter based charge limiting works through Manual API.
* Predbat dashboard card shows useful status and plan text.

### Still Being Tested

* Long-term stability.
* Behaviour across different Huawei inverter models.
* Freeze and hold modes.
* Whether Huawei-specific documentation should be proposed upstream.
* How well the setup behaves with different battery capacities and Home Assistant entity names.

---

## Recommended Starting Approach

For anyone trying something similar:

1. Install Predbat using the official instructions.
2. Verify all Huawei Solar entities in Home Assistant.
3. Start with Predbat in monitor/read-only mode.
4. Test Huawei service calls manually:

   * `forcible_charge_soc`
   * `forcible_discharge_soc`
   * `stop_forcible_charge`
5. Confirm the battery power sign.
6. Start with low charge/discharge power limits.
7. Only enable active control once the plan and service calls look correct.
8. Leave freeze modes disabled until basic control is stable.

---

## Resources

* [Predbat GitHub Repository](https://github.com/springfall2008/predbat)
* [Predbat Documentation](https://springfall2008.github.io/batpred/)
* [Home Assistant](https://www.home-assistant.io/)
* [Nordpool Integration](https://github.com/custom-components/nordpool)
* [Solcast Integration](https://github.com/dannerph/home_assistant_solcast_solar)
* [Huawei Solar Home Assistant Integration](https://github.com/wlcrs/huawei_solar)
* [go-eCharger](https://github.com/goecharger/go-eCharger-API-v2)

---

## Acknowledgements

Thanks to:

* [springfall2008](https://github.com/springfall2008) for creating Predbat,
* the Predbat community,
* the Home Assistant community,
* and everyone sharing examples, fixes and energy automation ideas.

This repository exists to document my own setup and hopefully help other Huawei Solar users get started with Predbat.
