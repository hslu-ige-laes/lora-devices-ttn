# avelon-wisely-carbonsense
<img src="https://github.com/hslu-ige-laes/lora-devices-ttn/raw/master/img/avelon-wisely-carbonsense_01.png" width="250" align="right"></img>
- Manufacturer: [Avelon](https://avelon.com/en/wisely/)
- Product: [Wisely Carbon Sense ](https://avelon.com/en/wisely/)
- [Ordering Link](https://avelon.com/en/buy-wisesly/)


## Description
- Indoor device with following sensors
  - <b>Temperature</b>, -10 ... +80 [°C], ± 0.1 °C between 20 ... 60 °C
  - <b>relative Humidity</b>, 0 ... 95[%rH], ± 1.5 %rH between 10...80 %rH
  - <b>CO<sub>2</sub></b>, 400 ... 5'000 [ppm], ± 30 ppm + 3 % of measurement value
  - <b>atmosheric pressure</b>, [hPa], ± 1 hPa
- Power Supply: 1 battery, 3.6 V, 3600 mAh, (A), Li-SOCl₂
  - Expected life time: 3 ... 5 years at roomtemperature
- Size: 80 × 80 × 27 mm
- Weight: 100 g

## Important notes
- The sensor comes in four different "flavours"
  - <b>Swisscom LPN</b> (means that the data goes to the AVELON server via Swisscom low power network, monthly charges apply)
  - <b>The Things Network</b> (means that the data goes to the AVELON-server via ttn, no monthly charges)
  - <b>Building Automation</b> (integration in the Avelon automation system, license fees system)
  - <span style="color:lightgreen"><b>Self-Managed</b> (that's what you wanna have!!! Data goes over the ttn network to the ttn server where you pick it up, no monthly charges or fees)</span>

## Installation and payload decoder


```javascript
function Decoder(bytes, port) {

  var decoded = {};

  // Offset
  OFFSET = bytes[bytes.length-1];
  
  // Data set length
  // Wisely Standard = 5; CarbonSense = 7; AllSense = 7
  DATASETLENGTH = 7;
  
  // number of data sets
  DATASETS = (bytes.length - 2 ) / 7;

  // Battery status
  var BAT = bytes[0]
  
  if((BAT > 0) && (BAT < 255)){
    var batVal = BAT / 254.0 * 100.0;
    decoded.batVal = Math.round(batVal * 10) / 10;
    decoded.batSta = "OK";
  }else if(BAT === 0){ // no battery inserted, external power supply
    decoded.batVal = 100.0;
    decoded.batSta = "OK, external power supply";
  } else{ // device could not acquire the voltage
    decoded.batVal = 0.0;
    decoded.batSta = "Error, could not acquire the voltage";
  }
  
  var measurement = "";
  var value = "";
  
  // atmospheric pressure in hPa
  measurement = "press";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + (bytes[i * DATASETLENGTH + 1] << 8 | bytes[i * DATASETLENGTH + 2]) / 10.0;
  }
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");
  
  // Temperature in °C
  measurement = "temp";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + (bytes[i * DATASETLENGTH + 3] << 8 | bytes[i * DATASETLENGTH + 4]) / 10.0;
  }
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");

  // Humidity in %rH
  measurement = "hum";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + bytes[i * DATASETLENGTH + 5] / 2.0;
  }
  value = Math.round(value / DATASETS * 100) / 100;
  eval("decoded." + measurement + " = " + value + ";");
    
  // Air quality CO2 in ppm
  measurement = "aqual";
  value = 0;
  for (i = 0; i < DATASETS; i++) {
    value = value + (bytes[i * DATASETLENGTH + 6] << 8 | bytes[i * DATASETLENGTH + 7]);
  }
  value = value / DATASETS;
  eval("decoded." + measurement + " = " + value + ";");

return decoded;
}
```
