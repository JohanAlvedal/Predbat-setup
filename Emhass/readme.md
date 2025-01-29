# **My EMHASS Setup in Home Assistant**  

## **Overview**  
**[EMHASS (Energy Management for Home Assistant)](https://github.com/davidusb-geek/emhass)** is an open-source tool designed to optimize energy consumption in your smart home. By leveraging real-time data such as electricity prices, solar production, and household energy usage, EMHASS creates schedules to reduce costs and improve efficiency.  

This repository documents my setup and configuration for integrating EMHASS with Home Assistant, along with **Solcast**, **Tibber**, and other tools for enhanced energy management.  

---

## **Disclaimer**  
I am not an expert in programming, energy management, or automation. This setup is based on my personal experience and experimentation. I cannot guarantee that it will work for everyone, and I do not take any responsibility for issues or damages caused by using this configuration or code.  
Please use it at your own risk and test thoroughly in your environment before applying changes.  

---

## **Features of EMHASS**  
- **Energy Optimization**: Automatically schedules appliances based on electricity prices, solar production, and energy consumption.  
- **Cost Savings**: Reduces energy bills by prioritizing off-peak hours and renewable energy use.  
- **Open-Source & Flexible**: Fully customizable to fit your unique energy setup.  

---

## **My Setup**  

### 1. **Home Assistant**  
- Home Assistant serves as the core platform, managing all automations, integrations, and device controls.  

### 2. **[Nordpool Integration](https://github.com/custom-components/nordpool)**  
- Provides real-time electricity prices, enabling EMHASS to optimize usage during off-peak hours.  

### 3. **[Tibber Integration](https://www.home-assistant.io/integrations/tibber/)**  
- Fetches detailed electricity price data and consumption statistics directly from Tibber for accurate cost analysis.  

### 4. **[Solcast Integration](https://github.com/dannerph/home_assistant_solcast_solar)**  
- Uses Solcast’s solar forecasting API to predict solar panel production, enabling more efficient scheduling and battery management.  

### 5. **Solar Panels & Battery**  
- A Huawei inverter paired with a 10kW battery to store excess solar energy, further enhancing the optimization potential of EMHASS.  

---

## **How I Integrated EMHASS**  

### **Step 1: Install EMHASS**  
Follow the installation instructions in the official [EMHASS GitHub Repository](https://github.com/davidusb-geek/emhass).  

### **Step 2: Configure Integrations**  
1. **Electricity Prices**: Use **Nordpool** or **Tibber** to provide real-time pricing data.  
2. **Solar Production**: Use **Solcast** to forecast solar energy generation.  
3. **Sensors**: Add your household energy consumption and production sensors in Home Assistant.  

### **Step 3: Automate with EMHASS**  
1. Define optimization goals in EMHASS (e.g., minimizing costs, maximizing renewable energy use).  
2. Create Home Assistant automations to act on EMHASS-generated schedules.  

---

## **Current Status**  
- **Working Features**:  
  - Scheduling appliances like dishwashers and washing machines during off-peak hours.  
  - Using Solcast forecasts to optimize battery charging with solar energy.  
  - Leveraging Tibber data for accurate cost analysis and predictions.  

- **Ongoing Improvements**:  
  - Fine-tuning the integration between EMHASS and Tibber for real-time optimizations.  
  - Enhancing dashboard visuals in Home Assistant for better energy insights.  

---

## **Future Goals**  
- **Full Integration with Tibber and Solcast**: Seamlessly use their data for predictive energy management.  
- **Dynamic Automations**: Expand automations to include more devices and scenarios.  
- **Documentation & Sharing**: Publish detailed setup guides to help others replicate and improve upon this setup.  

---

## **Resources**  
- **EMHASS**: [GitHub Repository](https://github.com/davidusb-geek/emhass)  
- **Tibber Integration**: [Tibber Documentation](https://www.home-assistant.io/integrations/tibber/)  
- **Solcast Integration**: [Solcast GitHub](https://github.com/dannerph/home_assistant_solcast_solar)  
- **Nordpool Integration**: [Nordpool GitHub](https://github.com/custom-components/nordpool)  
- **Home Assistant**: [Official Site](https://www.home-assistant.io/)  

---

## **Contributing**  
Feel free to open an issue or submit a pull request if you have ideas or improvements for this setup. Collaboration is welcome!  

---

## **Acknowledgments**  
Thanks to:  
- **[davidusb-geek](https://github.com/davidusb-geek)** for EMHASS.  
- **Tibber** for energy data and insights.  
- **Solcast** for solar forecasting.  
- The **Home Assistant Community** for their incredible support and resources.  

---
