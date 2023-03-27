# AnalogPuzzle
Arduino Code for Controlling Meters and LEDs Based on Slider Inputs
This code is designed for an Arduino board to control meters and LEDs based on slider inputs. The purpose of the code is to ensure that the meter values match the target values within a specified tolerance level.

# Pin Configuration
The following pins are used for meters, sliders, LEDs, and relay:
```
const byte meterPins[] = {11, 10, 9, 3};
const byte sliderPins[] = {A1, A2, A3, A4};
const byte ledPins[] = {7, 6, 5, 4};
const byte relayPin = 2;
```
# Target Values and Tolerance
The code defines the target values for meters and tolerance level as follows:
```
const int targetValues[] = {128, 128, 128, 128};
const int tolerance = 5;
```
# Input and Output Values
The code uses arrays for storing input and output values as follows:
```
int inputValues[4] = {};
int outputValues[4] = {};
```
# Solving Status
The code defines a flag for solving status as follows:
```
bool isSolved = false;
```
# Setup
In the setup() function, the code sets up the serial communication and pin modes:
```
Serial.begin(115200);
Serial.println(__FILE__);
Serial.println("Compiled: " __DATE__ "," __TIME__);

for (int i = 0; i < 4; i++) {
  pinMode(meterPins[i], OUTPUT);
  pinMode(ledPins[i], OUTPUT);
}
```
# Loop
In the loop() function, the code reads slider inputs and calculates output values for meters and LEDs:
```
for (int i = 0; i < 4; i++) {
  for (int x = 0; x < 2; x++) {
    analogRead(sliderPins[i]);
    delay(5);
  }
  inputValues[i] = analogRead(sliderPins[i]);
}

outputValues[0] = max(inputValues[0], inputValues[2]);
outputValues[1] = abs(inputValues[1] - inputValues[3]);
outputValues[2] = (inputValues[2] + inputValues[3]) / 2;
outputValues[3] = min(inputValues[2], inputValues[3]);
```
The code then maps and constrains the output values for meters and writes to pins:
```
for (int i = 0; i < 4; i++) {
  outputValues[i] = map(outputValues[i], 0, 1010, 0, 255);
  outputValues[i] = constrain(outputValues[i], 0, 255);

  analogWrite(meterPins[i], outputValues[i]);
  if (abs(outputValues[i] - targetValues[i]) > tolerance) {
    allmetersCorrect = false;
    digitalWrite(ledPins[i], LOW);
  } else {
    digitalWrite(ledPins[i], HIGH);
  }
}
```
The code updates the solving status based on all meters being within tolerance:
```
if (allmetersCorrect && !isSolved) {
  Serial.println("SOLVED!");
  isSolved = true;
} else if (isSolved && !allmetersCorrect) {
  Serial.println("Unsolved!");
  isSolved = false;
}
```
Finally, the code prints input and output values for debugging:
```
for (int i = 0; i < 4; i++) {
    Serial.print(inputValues[i]);
    Serial.print(",");
    Serial.print(outputValues[i]);
    Serial.print(",");
    if (i > 3) {
      Serial.print(",");
    }
  }
  Serial.println("");
  delay(50);
}
```

















