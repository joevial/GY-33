Recommended use:

Ground S1 to enable direct i2c access to TSC34725 sensor - this still allows access to the STM32 via UART to control the LED.

Wire both CT/DR as TX/RX for the UART connection, and SDA/SCL for the i2c connection.  Connect both.

Use whatever library you want for the TCS sensor, and use the following to control LED brightness over UART:

````
void gy33SendCommand(uint8_t cmd) {
  uint8_t sum = (0xA5 + cmd) & 0xFF;
  gy33Serial.write(0xA5);
  gy33Serial.write(cmd);
  gy33Serial.write(sum);
  gy33Serial.flush();
  Serial.print(F("[GY33] Sent: A5 "));
  Serial.print(cmd, HEX);
  Serial.print(F(" "));
  Serial.println(sum, HEX);
}

// level 0 = dimmest (0x6A), level 11 = brightest (0x60)
void gy33SetLED(uint8_t level) {
  if (level > 11) level = 11;
  uint8_t hw = 0x6A - level;
  if (hw < 0x60) hw = 0x60;
  gy33SendCommand(hw);
}
````
That way you get both LED control *and* direct access to the sensor!
