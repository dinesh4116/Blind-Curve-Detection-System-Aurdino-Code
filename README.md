 # Solar Powered Blind Curve Warning System
// Board: Arduino UNO
// Sensors: 2 x HC-SR04 ultrasonic sensors
// Alerts: 2 x red LEDs and 2 x buzzers
//
// Sensor A detects a vehicle approaching from Side A and warns Side B.
// Sensor B detects a vehicle approaching from Side B and warns Side A.
const byte TRIG_A = 2;
const byte ECHO_A = 3;
const byte TRIG_B = 4;
const byte ECHO_B = 5;
const byte LED_A = 6; // Warning for vehicles on Side A
const byte BUZZER_A = 7;
const byte LED_B = 8; // Warning for vehicles on Side B
const byte BUZZER_B = 9;
const float DETECT_DISTANCE_CM = 150.0; // Adjust after testing
const unsigned long SENSOR_TIMEOUT_US = 25000UL;
float readDistanceCM(byte trigPin, byte echoPin) {
digitalWrite(trigPin, LOW);
delayMicroseconds(2);
digitalWrite(trigPin, HIGH);
delayMicroseconds(10);
digitalWrite(trigPin, LOW);
unsigned long duration = pulseIn(echoPin, HIGH, SENSOR_TIMEOUT_US);
// No echo received: return a value meaning "no vehicle detected".
if (duration == 0) {
return 999.0;
}
return (duration * 0.0343) / 2.0;
}
void setup() {
pinMode(TRIG_A, OUTPUT);
pinMode(ECHO_A, INPUT);
pinMode(TRIG_B, OUTPUT);
pinMode(ECHO_B, INPUT);
pinMode(LED_A, OUTPUT);
pinMode(BUZZER_A, OUTPUT);
pinMode(LED_B, OUTPUT);
pinMode(BUZZER_B, OUTPUT);
digitalWrite(TRIG_A, LOW);
digitalWrite(TRIG_B, LOW);
Serial.begin(9600);
}
void loop() {
float distanceA = readDistanceCM(TRIG_A, ECHO_A);
delay(50); // Reduces interference between ultrasonic sensors
float distanceB = readDistanceCM(TRIG_B, ECHO_B);
// A vehicle on Side A warns traffic approaching from Side B.
bool warnSideB = (distanceA > 2.0 &&
distanceA <= DETECT_DISTANCE_CM);
// A vehicle on Side B warns traffic approaching from Side A.
bool warnSideA = (distanceB > 2.0 &&
distanceB <= DETECT_DISTANCE_CM);
digitalWrite(LED_A, warnSideA ? HIGH : LOW);
digitalWrite(BUZZER_A, warnSideA ? HIGH : LOW);
digitalWrite(LED_B, warnSideB ? HIGH : LOW);
digitalWrite(BUZZER_B, warnSideB ? HIGH : LOW);
Serial.print("Side A distance: ");
Serial.print(distanceA);
Serial.print(" cm | Side B distance: ");
Serial.print(distanceB);
Serial.println(" cm");
delay(100);
}
Solar Powered Blind Curve Warning System Page 3
