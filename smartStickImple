#include <SoftwareSerial.h>

#define TRIG_PIN 7
#define ECHO_PIN 6
#define BUZZER_PIN 8
#define LDR_PIN A0
#define LED_PIN 9
#define EMERGENCY_BUTTON_PIN 2 // Define the emergency button pin

SoftwareSerial BTSerial(10, 11); // RX, TX for Bluetooth communication

String gpsData;
bool emergencyTriggered = false;

void setup() {
  Serial.begin(9600); // Initialize serial communication at 9600 bps
  BTSerial.begin(9600); // Initialize Bluetooth communication at 9600 bps
  pinMode(TRIG_PIN, OUTPUT); // Set TRIG_PIN as an output
  pinMode(ECHO_PIN, INPUT);  // Set ECHO_PIN as an input
  pinMode(BUZZER_PIN, OUTPUT); // Set BUZZER_PIN as an output
  pinMode(LED_PIN, OUTPUT); // Set LED_PIN as an output
  pinMode(EMERGENCY_BUTTON_PIN, INPUT_PULLUP); // Set EMERGENCY_BUTTON_PIN as an input with internal pull-up resistor
}

void loop() {
  long duration, distance;

  // Handle distance measurement
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  duration = pulseIn(ECHO_PIN, HIGH);
  distance = duration * 0.034 / 2; // Speed of sound wave (0.034 cm/us) divided by 2 (round trip)

  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  BTSerial.print("Distance: ");
  BTSerial.print(distance);
  BTSerial.println(" cm");

  if (distance > 0 && distance <= 50) {
    int beepDelay = map(distance, 0, 50, 100, 1000); // Map the distance to a delay value
    digitalWrite(BUZZER_PIN, HIGH); // Turn the buzzer on
    delay(50); // Buzzer on duration
    digitalWrite(BUZZER_PIN, LOW); // Turn the buzzer off
    delay(beepDelay); // Wait for beepDelay duration
  } else {
    digitalWrite(BUZZER_PIN, LOW); // Turn the buzzer off
  }

  // Handle emergency button press
  if (digitalRead(EMERGENCY_BUTTON_PIN) == LOW) {
    emergencyTriggered = true;
    BTSerial.write("H"); // Indicate that the emergency button has been pressed
    Serial.println("EMERGENCY BUTTON PRESSED!");
    Serial.println("Button Pressed");
  }

  // Read GPS data from Bluetooth
  if (BTSerial.available()) {
    gpsData = BTSerial.readString();
    Serial.println(gpsData);

    // Send GPS data if emergency is triggered
    if (emergencyTriggered) {
      BTSerial.print("Emergency! Current Location: ");
      BTSerial.println(gpsData);
      Serial.print("Emergency! Current Location: ");
      Serial.println(gpsData);
      emergencyTriggered = false; // Reset emergency trigger after sending the message
    }
  }

  // Adjust LED brightness based on LDR reading
  int ldrValue = analogRead(LDR_PIN); // Read the LDR value
  int ledBrightness = map(ldrValue, 0, 1023, 255, 0); // Map the LDR value to LED brightness (reverse)
  analogWrite(LED_PIN, ledBrightness); // Set the LED brightness

  // Short delay before next measurement
  delay(50);
}
