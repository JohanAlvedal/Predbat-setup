These two scripts in Home Assistant dynamically set the battery's maximum charging and discharging power. Here's what they do:

### **1. Huawei Charging Power 4500W**
- **Purpose**: Sets the battery's maximum charging power to 4500W.  
- **Steps**:
  1. Checks if the current value of `number.battery_maximum_charging_power` is not already 4500W.  
     - This is done using a **template condition**:  
       ```jinja
       {{ states('number.battery_maximum_charging_power') | int(0) != 4500 }}
       ```
     If the value is already 4500W, the script does nothing.
  2. If the value is not 4500W, the script updates it to 4500W using the `number.set_value` service.

---

### **2. Huawei Discharging Power 4500W**
- **Purpose**: Sets the battery's maximum discharging power to 4500W.  
- **Steps**:
  1. Checks if the current value of `number.battery_maximum_discharging_power` is not already 4500W.  
     - This uses a similar **template condition**:  
       ```jinja
       {{ states('number.battery_maximum_discharging_power') | int(0) != 4500 }}
       ```
     If the value is already 4500W, the script does nothing.
  2. If the value is not 4500W, the script updates it to 4500W using the `number.set_value` service.

---

### **Summary**
Both scripts ensure that the battery's maximum charging or discharging power is correctly set to 4500W if it isn’t already, maintaining proper settings for operational needs.
