Summary of Automations
1. Predbat Charge:
The **"Predbat Charge"** automation governs forced battery charging based on time intervals, charge limits, and specific conditions. Here's a breakdown:

---

### **Triggers:**
- **Battery Charging Active:** Triggered when **`binary_sensor.predbat_charging`** turns **"on"**.
- **Charge Limit Changes:** Triggered when **`predbat.best_charge_limit`** changes state.

---

### **Conditions:**
- Ensures that **`binary_sensor.predbat_charging`** is **"on"** before proceeding.

---

### **Actions:**
The automation handles two distinct time periods:

#### **1. Between 06:00 and 23:30:**
- **Conditions:**
  - **`sensor.battery_state_of_capacity`** must be between 0 and 97.
  - Active triggers include **`Predbat_charging_on`** or **state changes**.

- **If `binary_sensor.predbat_car_charging_slot` is "on":**
  - Initiates forced charging via `huawei_solar.forcible_charge_soc` with a `target_soc` based on **`predbat.best_charge_limit`** (minimum 12), at **4500W** power.
  - Executes the **`script.huawei_charging_power_2500w`** after a 5-second delay.

- **Else:**
  - Forces charging at **5000W** with the same logic for `target_soc`.
  - Executes **`script.emhass_charging_power_2500`** after a delay.

- **Final Step:**  
  - Ensures discharging power is set to **0W** via **`script.huawei_discharging_power_0w`**.

---

#### **2. Between 23:30 and 06:00:**
- **Conditions:**
  - **`sensor.battery_state_of_capacity`** must be between 0 and 96.
  - Active triggers include **`Predbat_charging_on`** or **state changes**.

- **If `binary_sensor.predbat_car_charging_slot` is "on":**
  - Forces charging at **3500W** with a dynamically determined `target_soc`.
  - Executes **`script.emhass_charging_power_2500`** after a 5-second delay.

- **Else:**
  - Forces charging at **5000W** with the same logic for `target_soc`.
  - Executes **`script.huawei_charging_power_5000w`** after a delay.

- **Final Step:**  
  - Ensures discharging power is set to **0W** via **`script.huawei_discharging_power_0w`**.

---

### **Mode:**
- **Restart mode:** Ensures the automation restarts when triggered, stopping any current execution.

---

### **Purpose:**
This automation maintains efficient and optimized battery charging:
- Adapts charging power and `target_soc` based on time, triggers, and battery state.
- Prioritizes car charging slots during specific conditions.
- Aligns with operational hours and prevents overcharging with defined capacity limits.

2. Predbat Discharge:
The automation **"Predbat Discharge"** manages forced battery discharging for optimal export when specific conditions are met. Here's a summary of its functionality:

### **Triggers:**
- Activated when:
  1. **`binary_sensor.predbat_exporting`** turns **"on"**.
  2. The **`predbat.status`** changes to **"Exporting"**.
  3. A time pattern is matched (every 11 minutes).

### **Conditions:**
- Proceeds only if:
  - **`binary_sensor.predbat_exporting`** is **"on"** (exporting is active).
  - **`binary_sensor.predbat_charging`** is **"off"** (charging is inactive).

### **Actions:**
- **If `predbat.best_export_limit` is below 12:**  
  - Forces discharging at **5000W** for 120 seconds via `huawei_solar.forcible_discharge`.  
  - Runs a script to set discharging power to **5000W** after a 5-second delay.

- **Else (`predbat.best_export_limit` is 12 or above):**  
  - Sets a target state of charge (`target_soc`) dynamically based on the value of `predbat.best_export_limit` (minimum 12) via `huawei_solar.forcible_discharge_soc`.  
  - Forces discharging at **5000W**.  
  - Runs the script to set discharging power to **5000W** after a 5-second delay.

### **Mode:**
- **Parallel mode:** Allows up to 10 instances of the automation to run simultaneously.

### **Purpose:**
This automation ensures the battery discharges optimally to export power when exporting is active and charging is inactive. It adjusts the discharge logic based on the `predbat.best_export_limit` to maintain efficient energy export while meeting specific thresholds.

3. Predbat STOP, Freeze, Hold, Demand:
The automation **"Predbat STOP Freeze Hold Demand"** manages various charging and discharging states for a battery inverter based on the value of the `predbat.status` entity. Here's a summary of its functionality:

### **Triggers:**
- Activated when `predbat.status` changes to any of the following states:
  - **Hold charging**, **Freeze charging**, **Hold exporting**, **Freeze exporting**, **Hold for car**, or **Demand**.

### **Conditions:**
- Executes only if the current state of `predbat.status` matches one of the above states.

### **Actions:**
- **Hold for Car:**  
  - Stops discharging by running the script (`huawei_discharging_power_0w`).

- **Freeze exporting & Hold exporting:**  
  - Stops forced charging via the inverter.  
  - Switches between different charging and discharging modes with delays.

- **Freeze charging & Hold charging:**  
  - Stops forced charging via the inverter.  
  - Adjusts charging power based on:
    - Season (charges at 0W during winter, autumn, and spring; otherwise, charges at 4500W).  
    - Solar input power (`sensor.inverter_input_power` below 1500W).  
  - Stops discharging.

- **Demand:**  
  - Adapts charging/discharging depending on the time of day:
    - **23:00-06:00:** Stops all charging and discharging.  
    - **Otherwise:** Checks battery capacity and solar energy availability to adjust between 0W and 5000W discharging, and 4500W charging.

### **Mode:**
- **Parallel mode:** Allows up to 10 instances of the automation to run simultaneously.

### **Purpose:**
The automation dynamically controls charging and discharging of the battery based on predefined states, time of day, season, and energy availability. It optimizes solar energy usage and protects the battery's lifespan.

Adjusts power levels (0W or 2500W) based on triggers and conditions like time or SOC.
Improvement: Refactor complex "choose" sequences for better readability and maintainability.

Key Adjustments Needed:
Reduce repeated delays and script executions for performance.
Improve readability with clearer logic and variable reuse.
Ensure triggers and actions don't conflict under overlapping conditions.


##################

