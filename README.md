# Task-1-Microcontroller-Based-AC-Bulb-Control-Menu


# code for arduino ide
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Encoder.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);  // Initialize with 16 columns and 2 rows
Encoder encoder(2, 3);

int relayPins[] = {5, 6, 7};
int numRelays = sizeof(relayPins) / sizeof(relayPins[0]);

bool bulbStates[] = {false, false, false};
int currentRegion = 0;  // 0: OFF, 1: B1, 2: B2, 3: B3
int lastEncoderValue = 0;

void setup() {
  lcd.init();
  lcd.backlight();
  
  for (int i = 0; i < numRelays; i++) {
    pinMode(relayPins[i], OUTPUT);
    digitalWrite(relayPins[i], LOW);  // Initialize relays to OFF
  }
  
  updateLCD();
}

void loop() {
  int encoderValue = encoder.read();

  if (encoderValue != lastEncoderValue) {
    int change = encoderValue - lastEncoderValue;
    if (change > 0) {
      currentRegion = (currentRegion + 1) % 4;  // Cycle through 0 to 3
    } else if (change < 0) {
      currentRegion = (currentRegion + 3) % 4;  // Handle negative change
    }
    lastEncoderValue = encoderValue;
    updateBulbs();
    updateLCD();
  }
}

void updateBulbs() {
  for (int i = 0; i < numRelays; i++) {
    if (i == (currentRegion - 1)) {
      bulbStates[i] = true;  // Turn on selected bulb
      digitalWrite(relayPins[i], HIGH);
    } else {
      bulbStates[i] = false;  // Turn off other bulbs
      digitalWrite(relayPins[i], LOW);
    }
  }
}

void updateLCD() {
  lcd.clear();
  
  lcd.setCursor(0, 0);  // First row
  lcd.print(" B1    B2    B3");

  lcd.setCursor(0, 1);  // Second row
  for (int i = 0; i < 3; i++) {
    lcd.print(currentRegion == (i + 1) ? " ON " : " OFF ");
  }
  
  // Highlight the selected region label
  lcd.setCursor((currentRegion - 1) * 7, 0);
 // lcd.print(">");
}


#video demonstration
https://drive.google.com/drive/folders/1_C2miSrXcBcnZwU5pyBz_UjpE61fk7qF?usp=sharing
