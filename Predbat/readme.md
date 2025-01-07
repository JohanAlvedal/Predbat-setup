---

# **Predbat Setup in Home Assistant**  

## **Overview**  
**[Predbat (Home Battery Prediction)](https://github.com/springfall2008/predbat)** is a tool designed to optimize the charging and discharging of home batteries. By leveraging real-time data such as electricity prices, solar production, and household energy consumption, Predbat automates battery management to reduce costs and maximize efficiency.  

This repository documents my setup and configuration for integrating Predbat with Home Assistant.  

---

## **Disclaimer**  
I am not an expert in programming, energy management, or automation. This setup is based on personal experience and experimentation. Use the code at your own risk—I do not take responsibility for issues or damages caused by implementing this setup. Test thoroughly in your environment before applying changes.  

---

## **Features of Predbat**  
- **Battery Optimization**: Automates charging during low-price periods and prioritizes discharging during high-price periods.  
- **Cost Reduction**: Reduces energy costs by strategically utilizing stored solar energy and optimizing grid usage.  
- **Flexible Integration**: Supports multiple inverters, including Huawei.  

---

## **My Setup**  

### 1. **Home Assistant**  
- **[Home Assistant](https://www.home-assistant.io/)** serves as the platform managing all automations and integrations.  

### 2. **[Nordpool Integration](https://github.com/custom-components/nordpool)**  
- Fetches real-time electricity prices, enabling Predbat to optimize battery usage.  

### 3. **Huawei Inverter and 10kW Battery**  
- A Huawei inverter paired with a 10kW battery provides the foundation for storing solar energy and managing energy flow in the home.  

### 4. **[Solcast Integration](https://github.com/dannerph/home_assistant_solcast_solar)**  
- Predicts solar energy production, helping Predbat optimize battery charging in anticipation of upcoming solar availability.  

---

## **How I Integrated Predbat**  

### **Step 1: Install Predbat**  
Follow the installation instructions provided in the official [Predbat GitHub Repository](https://github.com/springfall2008/predbat).  

### **Step 2: Configure Predbat**  
1. Set up electricity prices using the **Nordpool** or **Tibber** integrations.  
2. Add solar production forecasts via **Solcast** or similar tools.  
3. Configure battery sensors and energy usage data in Home Assistant.  

### **Step 3: Automate with Predbat**  
Create automations in Home Assistant to trigger battery charging or discharging based on Predbat’s predictions.  
---

## **Current Status**  
- **Working Features**:  
  - Automating battery charging during off-peak hours.  
  - Optimizing discharging during peak-price periods.  
  - Leveraging solar production forecasts to prioritize renewable energy usage.  

- **Ongoing Improvements**:  
  - Refining automations for greater stability and precision.  
  - Enhancing integration with Nordpool and Solcast for better predictions.  

---

## **Future Goals**  
- **Dynamic Adjustments**: Automate additional devices based on Predbat’s predictions.  
- **Advanced Dashboards**: Add real-time monitoring of battery performance in Home Assistant.  
- **Documentation**: Share detailed setup guides for easier replication by others.  

---

## **Resources**  
- **Predbat Repository**: [GitHub](https://github.com/springfall2008/predbat)  
- **Nordpool Integration**: [GitHub](https://github.com/custom-components/nordpool)  
- **Solcast Integration**: [GitHub](https://github.com/dannerph/home_assistant_solcast_solar)  
- **Home Assistant**: [Official Site](https://www.home-assistant.io/)  

---

## **Acknowledgments**  
Thanks to:  
- **[springfall2008](https://github.com/springfall2008)** for creating Predbat.  
- The **[Home Assistant Community](https://community.home-assistant.io/)** for their invaluable resources.  

---
