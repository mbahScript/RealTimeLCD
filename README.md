# STM32 RTC LCD Display

## Project Overview
This project demonstrates how to use the **internal Real-Time Clock (RTC)** of an STM32F7 microcontroller to display the **current time and date** on a **16×2 I²C LCD**. By using the **PCF8574 I²C backpack**, the project reduces GPIO usage to just **SDA and SCL**, making the setup compact and easy to integrate.

**Key features include:**  
- Initializing the STM32 **internal RTC**  
- Displaying **real-time time (HH:MM:SS)** and **date (DD-MM-YYYY)** on a 16×2 LCD  
- Using **RTC backup registers** to retain time after resets  
- Optional **alarm setup** using RTC Alarm A  

> This project is ideal for beginners exploring **STM32 HAL, I²C, and RTC modules**, and serves as a foundation for **embedded clocks, timers, and real-time displays**.

---

## Features
- Real-time **time and date display** on a 16×2 LCD  
- I²C communication using STM32 HAL (`hi2c1`)  
- **RTC backup register** to preserve time on resets  
- Configurable **RTC Alarm A**  
- Minimal pin usage (**SDA + SCL only**)  

---

## Hardware Requirements

| Component                     | Specification / Pin | Notes                       |
|-------------------------------|------------------|----------------------------|
| STM32F7xx development board   | e.g., Nucleo F767 | —                          |
| 16×2 LCD with I²C backpack    | PCF8574           | —                          |
| Jumper wires                  | —                 | —                          |
| LCD Power Supply              | 5V                | LCD requires 5V             |

---

## Wiring

| LCD Pin | STM32 Pin    | Notes               |
|---------|--------------|-------------------|
| GND     | GND          | Ground             |
| VCC     | 5V           | LCD requires 5V    |
| SDA     | PB7 (I2C1)   | I²C data line      |
| SCL     | PB6 (I2C1)   | I²C clock line     |

> **Tip:** Adjust the onboard contrast potentiometer to make text visible.

---

## Code Overview

### RTC Initialization & Backup Check
```c
if(HAL_RTCEx_BKUPRead(&hrtc, RTC_BKP_DR1) != 0x32F2)
{
    set_time(); // Set initial time if backup register is empty
}
```

### Time & Date Retrieval
```c
RTC_DateTypeDef gDate;
RTC_TimeTypeDef gTime;

HAL_RTC_GetTime(&hrtc, &gTime, RTC_FORMAT_BIN);
HAL_RTC_GetDate(&hrtc, &gDate, RTC_FORMAT_BIN);

sprintf(time,"%02d:%02d:%02d", gTime.Hours, gTime.Minutes, gTime.Seconds);
sprintf(date,"%02d-%02d-%d", gDate.Date, gDate.Month, 2026 + gDate.Year);
```

### Display on LCD
```c
lcd_send_cmd(0x80); // Line 1
lcd_send_string(time);
lcd_send_cmd(0xC0); // Line 2
lcd_send_string(date);
```

### Alarm Setup (Optional)
```c
set_alarm(); // Uses RTC Alarm A
```

### Main Loop
```c
while (1)
{
    get_time();
    display_time();
    HAL_Delay(500); // Update every 500ms
}
```

### Example Display Output
```c
Line 1: 20:44:50
Line 2: 27-01-2026
```

---

## References

This project was implemented using concepts explained on the **[ControllersTech YouTube channel](https://www.youtube.com/@ControllersTech)**.

*Used as a learning reference for STM32 RTC configuration, I²C LCD interfacing, and HAL usage.*

---

## Author
Developed as part of hands-on learning in **Embedded Systems and STM32 Microcontroller Programming**.
