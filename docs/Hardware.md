# Hardware

## IoT Sensor Hardware Specifications

This document outlines the hardware components, specifications, and design considerations for the Flood Sentinel IoT sensor network.

## Sensor Requirements

### Functional Requirements
- Measure water level (0-5 meters with ±5 cm accuracy)
- Measure rainfall (0-500 mm/hr with ±2 mm accuracy)
- Measure soil moisture (0-100% with ±3% accuracy)
- Operate in outdoor monsoon/flood conditions
- Transmit data wirelessly every 5-15 minutes
- Battery-powered operation (6-12 months minimum)
- Low cost (<$200 per unit for mass production)
- Durable and maintainable by local technicians

### Environmental Requirements
- Operating temperature: -10°C to +60°C
- Humidity: 0-100% (condensation prone)
- Water resistance: IP67 minimum (submersion proof)
- Corrosion resistance (coastal/urban environments)
- Vibration resistant (mounting on structures)
- Lightning protection capability

---

## Core Sensor Modules

### 1. Water Level Measurement

#### Sensor Options

**Option A: Ultrasonic Sensor (Recommended for Phase 1)**
- **Model**: MaxBotix MB7389 HRXL-MaxSonar-WRS
- **Type**: Non-contact ultrasonic
- **Range**: 0-5 meters
- **Accuracy**: ±2-3 cm
- **Advantages**: No moving parts, minimal maintenance, weather-sealed
- **Disadvantages**: Affected by air temperature, foam/turbulence
- **Cost**: $100-150 (with cable)
- **Power**: 3.3-5V, ~3 mA average

**Option B: Capacitive Level Sensor**
- **Model**: Vegaflex 61 or similar
- **Range**: 0-6 meters
- **Accuracy**: ±1 cm
- **Advantages**: High accuracy, minimal maintenance
- **Disadvantage**: Higher cost (~$300-500)
- **Best for**: Phase 2+ deployments requiring higher accuracy

**Option C: Pressure-Based Sensor**
- **Submerged pressure transducer** (e.g., Honeywell HPX)
- **Range**: Varies by model (0-5m available)
- **Accuracy**: ±1-2 cm
- **Advantage**: Robust, no air temperature sensitivity
- **Disadvantage**: Requires recalibration if sensor depth changes
- **Cost**: $150-250

#### Design Considerations
- Non-contact (ultrasonic) preferred to avoid corrosion
- Protective shield/cone to prevent false readings from nearby objects
- Temperature compensation for ultrasonic sensors
- Backup redundant sensor option for critical locations

---

### 2. Rainfall Measurement

#### Sensor Options

**Option A: Tipping Bucket Rain Gauge (Recommended)**
- **Model**: Onset HOBO RG3, Hydrological Services TB4
- **Type**: Mechanical, self-emptying
- **Accuracy**: ±2% up to 100 mm/hr
- **Resolution**: 0.01 inches (0.254 mm) per tip
- **Advantages**: Simple, reliable, minimal power, accurate
- **Disadvantage**: Moving parts (maintenance), analog signal
- **Cost**: $150-300
- **Power**: Negligible (mechanical), digital trigger needed

**Option B: Optical Rain Sensor**
- **Model**: Vaisala WXT series or similar
- **Type**: Optical particle detection
- **Accuracy**: ±5% up to 200 mm/hr
- **Advantages**: No moving parts, instant response
- **Disadvantage**: Higher cost (~$500-1000)
- **Best for**: Phase 2+ with higher budget

**Option C: Piezo Impact Sensor**
- **Model**: Hydrological Services PISO or similar
- **Accuracy**: ±2-3%
- **Advantages**: No moving parts, direct digital output
- **Disadvantage**: Less proven in field
- **Cost**: $200-400

#### Design Considerations
- Install at height to avoid splashing
- Wind protection funnel (affects accuracy significantly)
- Keep sensor clean (biofouling can cause errors)
- Regular calibration (monthly during monsoon)

---

### 3. Soil Moisture Measurement

#### Sensor Options

**Option A: Capacitive Moisture Sensor (Recommended)**
- **Model**: DFRobot SEN0193 or Adafruit DHT series adaptation
- **Type**: Capacitive dielectric
- **Accuracy**: ±3-5%
- **Range**: 0-100% soil moisture
- **Advantages**: Low cost, low power, simple integration
- **Disadvantage**: Requires calibration, affected by soil composition
- **Cost**: $10-50
- **Power**: 3.3-5V, ~1-5 mA

**Option B: Tensiometer**
- **Type**: Vacuum-based water potential sensor
- **Advantages**: Direct measurement of plant-available water
- **Disadvantage**: High maintenance, fragile
- **Cost**: $200-500
- **Best for**: Research-focused deployments

**Option C: TDR (Time Domain Reflectometry) Sensor**
- **Model**: Decagon EC-5 or similar
- **Accuracy**: ±3%
- **Advantages**: Good accuracy, low power
- **Disadvantage**: More expensive (~$100-200)
- **Best for**: Phase 2+ deployments

#### Design Considerations
- Install at multiple depths (10 cm, 30 cm, 50 cm) to detect saturation
- Bury sensors at 45° angle to minimize air gaps
- Calibrate for local soil type (sand, clay, loam)
- Monthly maintenance to check for physical damage

---

### 4. Environmental Sensors (Complementary)

**Temperature & Humidity**
- **Sensor**: DHT22 or SHT31
- **Accuracy**: ±0.5°C, ±3% RH
- **Power**: 1-2 mA
- **Cost**: $10-30

**Atmospheric Pressure**
- **Sensor**: BMP280 or BME680
- **Accuracy**: ±1 hPa
- **Power**: 1 mA
- **Cost**: $5-15

**Wind Speed** (optional)
- **Sensor**: Anemometer cup type
- **Accuracy**: ±2%
- **Cost**: $50-150

---

## Microcontroller & Connectivity

### Microcontroller Options

**Option A: Arduino with Wireless Module (Phase 1)**
- **MCU**: Arduino MKR WiFi 1010 or Arduino MKR NB-IoT
- **Features**: Built-in WiFi/NB-IoT, good community support
- **Power**: ~30 mA active, 100 µA standby
- **Cost**: $50-100
- **Programming**: Arduino IDE, extensive libraries

**Option B: Raspberry Pi Pico with LoRaWAN HAT**
- **MCU**: RP2040 dual-core
- **Features**: Very low cost, flexible I/O
- **Power**: ~100 mA active, 100 µA standby
- **Cost**: $30-60 total
- **Programming**: MicroPython, C/C++

**Option C: STM32L4-based Development Board**
- **Features**: Ultra-low power, bare-metal efficiency
- **Power**: ~5 mA active, <1 µA standby
- **Cost**: $20-40
- **Programming**: More technical, higher barrier

### Connectivity Options

**WiFi**
- **Best for**: Urban areas with existing WiFi
- **Power**: ~80 mA transmitting
- **Range**: 100-200m indoor
- **Cost**: Integrated modules ($10-20)

**LoRaWAN**
- **Best for**: Remote areas, long range, battery life
- **Power**: ~50 mA transmitting, ~2 µA standby
- **Range**: 5-15 km line-of-sight
- **Cost**: Module ($20-50) + gateway ($200-500)
- **Latency**: High (minutes), good for periodic data

**NB-IoT / LTE-M**
- **Best for**: Urban areas without WiFi/LoRaWAN
- **Power**: ~200-400 mA during transmission
- **Range**: Wide (cellular)
- **Cost**: Module ($40-80) + SIM cost
- **Latency**: Low (seconds)

**GSM/GPRS**
- **Best for**: Legacy infrastructure areas
- **Power**: ~300-400 mA during transmission
- **Cost**: Module ($20-40) + SIM cost
- **Availability**: Declining but widespread

### Recommended Configuration (Phase 1)
- **MCU**: Arduino MKR WiFi 1010
- **Primary Connectivity**: WiFi (urban deployments)
- **Secondary**: NB-IoT HAT (fallback, areas without WiFi)
- **Total Cost**: $60-120 per unit

---

## Power Management

### Battery Options

**Option A: Lithium AA/AAA Batteries**
- **Capacity**: 2,000-3,000 mAh per cell
- **Voltage**: 1.5V (alkaline), 3.7V (Li-ion)
- **Life**: 6-12 months depending on duty cycle
- **Cost**: $1-5 per cell
- **Advantage**: Replaceable, widely available
- **Disadvantage**: Not rechargeable (unless Li-ion)

**Option B: Rechargeable Battery Pack**
- **Type**: Li-ion 18650 cells (3,000-3,500 mAh each)
- **Voltage**: 3.7V per cell, or 7.4V for 2S configuration
- **Life**: 300-500 charge cycles
- **Cost**: $10-30 per pack
- **Advantage**: Rechargeable, higher capacity
- **Disadvantage**: Requires charging infrastructure

**Option C: Solar + Battery Hybrid**
- **Solar Panel**: 5-10W monocrystalline
- **Charge Controller**: MPPT or PWM based
- **Battery**: Rechargeable Li-ion as above
- **Life**: Theoretically indefinite with proper sizing
- **Cost**: $100-200 per unit
- **Best for**: Long-term deployments

### Power Budget Calculation

**Typical sensor consumption (WiFi model)**:
- Microcontroller active: 30 mA × 10 sec/cycle = 3.3 µAh
- Water level sensor: 3 mA × 5 sec = 0.0042 mAh
- WiFi transmission: 80 mA × 3 sec = 0.067 mAh
- **Per reading (15 min interval)**: 0.074 mAh
- **Per day (96 readings)**: 7.1 mAh
- **Per month**: 213 mAh
- **Per year**: 2,556 mAh

**Battery recommendation**: 4,000 mAh lithium would last ~18 months at this consumption

**With solar + rechargeable**: Indefinite (1-2 year service life for battery)

---

## Enclosure & Mounting

### Enclosure Specifications
- **Rating**: IP67 (dust/waterproof), rated for submersion
- **Material**: Polycarbonate or ABS plastic (UV resistant)
- **Size**: 150×100×100 mm (typical)
- **Cable Glands**: Waterproof M20 or M25 size
- **Cost**: $30-80 per unit

### Sensor-Specific Enclosures
- **Water level**: Clear acrylic tube or open design (no enclosure)
- **Rain gauge**: Integrated (tipping bucket includes enclosure)
- **Soil moisture**: Outdoor-rated connector box
- **Electronics**: Main polycarbonate box

### Mounting Hardware
- Stainless steel or nylon brackets (corrosion resistant)
- Adjustable pole/staff gauge for water level (2-3 meter height)
- Ground stakes or concrete anchors for stability
- Cable management clips and conduit

### Installation Best Practices
- Mount at least 1.5 m above expected maximum water level
- Ensure 180° unobstructed view for rain gauge
- Avoid proximity to buildings/reflective surfaces (ultrasonic sensor)
- Provide shelter from direct wind (rain gauge accuracy)
- Ground the sensor enclosure to prevent lightning damage

---

## Bill of Materials (BOM) - Phase 1 Prototype

| Component | Quantity | Unit Cost | Total | Notes |
|-----------|----------|-----------|-------|-------|
| Arduino MKR WiFi 1010 | 1 | $40 | $40 | Microcontroller |
| MaxBotix MB7389 | 1 | $120 | $120 | Water level sensor |
| Tipping Bucket Rain Gauge | 1 | $200 | $200 | Rainfall sensor |
| Capacitive Soil Moisture | 1 | $30 | $30 | Soil moisture |
| DHT22 Temperature/Humidity | 1 | $15 | $15 | Environmental |
| Lithium Battery Pack (7.4V 3Ah) | 1 | $20 | $20 | Power |
| Solar Panel 5W | 1 | $30 | $30 | Optional recharge |
| Charge Controller | 1 | $15 | $15 | Solar controller |
| Polycarbonate Enclosure | 1 | $50 | $50 | Weatherproof box |
| Stainless Steel Mounting Hardware | 1 | $30 | $30 | Brackets, cables, etc. |
| PCB & Connectors | 1 | $20 | $20 | Custom board |
| **Total per Unit** | | | **$470** | Without solar: $410 |

**Notes**:
- Costs are estimates for single-unit prototype; bulk pricing (100+ units) would be 30-50% lower
- Add $50-100 for integration engineering and testing
- Gateway/receiver hardware additional (MQTT broker, WiFi router, or LoRaWAN gateway)

---

## Testing & Calibration

### Factory Testing
- Power on test (all components functional)
- Sensor range validation against known values
- Wireless connectivity test
- Battery voltage verification

### Field Calibration
- **Water level**: Check against reference gauge or survey
- **Rain gauge**: Verify tip count against known water volumes (250 mL, 500 mL)
- **Soil moisture**: Calibrate for local soil type (saturation = 100%, air-dry = 0%)
- **Temperature**: Cross-check against nearby weather station

### Maintenance Schedule
- Monthly: Visual inspection, clean rain gauge funnel
- Quarterly: Battery voltage check, data quality review
- Annually: Full recalibration, sensor replacement if needed

---

## Safety Considerations

### Electrical Safety
- Voltage <24V (safe from shock)
- Current limiting for solar charging
- Proper grounding for lightning protection

### Mechanical Safety
- Secure mounting to prevent falling during storms
- No sharp edges or protrusions
- Easy access for maintenance without climbing

### Environmental Impact
- Recyclable materials where possible
- Battery disposal plan (proper recycling)
- No toxic coatings or adhesives

---

## Future Enhancements

**Phase 2+**:
- Integrated multiple sensors on single PCB
- Real-time data quality assessment
- On-device edge computing for alerts
- Mesh networking capability
- Energy harvesting from vibration/thermal gradients

---

## Resources & Suppliers

### Recommended Suppliers
- **Electronics**: Arduino, Adafruit, Sparkfun, Digi-Key
- **Sensors**: Onset (weather), Vegetronix (moisture), MaxBotix (ultrasonic)
- **Enclosures**: Fibox, Phoenix Contact
- **Solar Components**: MPPT charge controllers from Victron, Epever

### Reference Documentation
- Arduino MKR WiFi 1010 Datasheet
- MaxBotix MB7389 User Manual
- Onset HOBO RG3 Specifications
- IP67 Waterproof Connector Standards

---

**Status**: Phase 0 - Hardware specification (prototyping begins Phase 1)  
**Last Updated**: July 2026

Have suggestions for hardware improvements? Open an issue or discussion!
