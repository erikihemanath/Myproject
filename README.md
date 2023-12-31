# Myproject
##GAS, TEMPERATURE AND FALL DETECTION AND MONITORING USING IOT IN SMART HELMET
A smart helmet equipped with gas, fall, and temperature sensors is a crucial safety device used in mining industries to protect workers from various hazards. Here's a brief overview of its features and functions:

Gas Sensor: The smart helmet incorporates a gas sensor capable of detecting toxic or flammable gases commonly found in mining environments, such as methane, carbon monoxide, or hydrogen sulfide. If the sensor detects high gas levels, it can trigger an alarm or provide real-time alerts to the wearer, indicating the need for immediate evacuation or precautionary measures.

Fall Sensor: The fall sensor is designed to detect sudden movements or impacts that may occur during accidents or falls. It utilizes accelerometers or gyroscopes to monitor changes in position or velocity. If a significant fall or impact is detected, the helmet can automatically send distress signals to the control center or designated personnel, enabling prompt response and rescue operations.

Temperature Sensor: The temperature sensor in the smart helmet monitors the ambient temperature within the mining environment. It helps detect excessive heat or cold conditions that could pose a threat to worker safety. High-temperature alerts can be crucial for preventing heatstroke or heat exhaustion, while low-temperature warnings can ensure workers take appropriate measures to prevent frostbite or hypothermia.

Real-time Monitoring: The smart helmet is typically equipped with wireless connectivity and data transmission capabilities. It allows continuous monitoring of the gas levels, fall events, and temperature readings in real-time. The collected data can be transmitted to a central control system or a cloud-based platform for analysis and tracking. This information enables supervisors or safety personnel to monitor the conditions of multiple workers simultaneously and take immediate action in case of emergencies.

Alarm and Alert Systems: The smart helmet is equipped with built-in speakers or vibration mechanisms to deliver alarms and alerts to the wearer. When hazardous gas levels are detected, or a fall event occurs, the helmet can emit audible alarms, visual indicators, or send vibrations to warn the worker. Additionally, the helmet can communicate with other safety devices, such as gas detectors or emergency response systems, to trigger a synchronized response in case of emergencies.

Data Logging and Analytics: The smart helmet may have the capability to store sensor data locally or transmit it to a centralized database for further analysis. This data can be used for monitoring worker safety and identifying patterns or trends that can help improve safety protocols and prevent accidents in the future.

Overall, the integration of gas, fall, and temperature sensors into smart helmets for mining industries enhances worker safety by providing early warnings, real-time monitoring, and facilitating quick response in hazardous situations. It helps mitigate risks and ensures a safer working environment for miners.

################# CODE ##################### 
#include <LiquidCrystal.h> 
#include <SoftwareSerial.h>

float t=0;
char data = 0; 
String apiKey = "ZY6DICQ136QRGTH1"; // Write API key
// connect 8 to TX of ESP
// connect 9 to RX of ESP 
SoftwareSerial ser(8,9); 
// RX, TX 
const int rs = 13, en = 12, d4 = 6, d5 = 5, d6 = 4, d7 = 3;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
float temp = 0;
unsigned long start, finished, elapsed;
int period = 250; 
unsigned long time_now = 0; 
int const PINO_SGAS = A0;
int belt=A2; 
int red=2;
int green=11;
int yellow=1;
int tilt=7;
int buzzer=A4;
int valor; 
void setup()
{
Serial.begin(9600); 
Serial.begin(9600); 
Serial.println(F("reading temperature begin. \n")); 
pinMode(red, OUTPUT); 
digitalWrite(red, LOW);
pinMode(green, OUTPUT); 
digitalWrite(green, LOW); 
pinMode(yellow, OUTPUT); 
digitalWrite(yellow, LOW);
pinMode(buzzer, OUTPUT);
pinMode(belt, INPUT); // set up the LCD's number of columns and rows: 
lcd.begin(16, 2);

Serial.println("AT"); // Attenuation

delay(300);

Serial.println("AT+GMR"); // To view version info for ESP-01 output: 00160901 and ESP-12 output: 0018000902-AI03

delay(500);

Serial.println("AT+CWMODE=3"); // To determine WiFi mode /* 1 = Station mode (client) 2 = AP mode (host) 3 = AP + Station mode (ESP8266 has a dual mode) */

delay(1000);

Serial.println("AT+RST"); // To restart the module

delay(1000);

Serial.println("AT+CIPMUX=1"); // Enable multiple connections /*

0: Single connection
1: Multiple connections (MAX 4)
*/
delay(1000);

//String cmd="AT+CWJAP="SSID","PASSWORD""; // connect to Wi-Fi 
String cmd="AT+CWJAP="Thiha Htun","12345678""; 
Serial.println(cmd); 
delay(1000);
Serial.println("AT+CIFSR"); // Return or get the local IP address delay(1000); 
} 
void loop()
{ 
digitalWrite(green, LOW);
digitalWrite(red, LOW); 
lcd.clear(); 
if(analogRead(belt)>=500)
{ 
int start=millis()/1000; 
digitalWrite(green,HIGH); 
lcd.print("Helmet Wearing"); 
lcd.setCursor(0,2); 
lcd.print("Helmet is now running"); 
static unsigned long sensortStamp = 0; 
valor = analogRead(PINO_SGAS);
t=valor;

if(valor>800)
{ gas(); } 
if(millis() - sensortStamp > 1000)
{ 
sensortStamp = millis(); 
int reading = analogRead(A1);
Serial.print(F("Real Time Temp: ")); // converting that reading to voltage, for 3.3v arduino use 3.3 
float voltage = reading * 5.0; 
voltage /= 1024.0;
// print out the voltage // 
Serial.print(voltage);
Serial.println(" volts"); // now print out the temperature
float temperatureC = (voltage - 0.5) * 100 ; //converting from 10 mv per degree wit 500 mV offset //to degrees ((voltage - 500mV) times 100) 
Serial.print(temperatureC); 
Serial.print(" degrees C ");

for (int positionCounter = 0; positionCounter < 13; positionCounter++)
{ // scroll one position left:
lcd.scrollDisplayLeft(); // wait a bit:
delay(300); 
} 
lcd.setCursor(0,1);
lcd.print(temperatureC); 
lcd.print(" ");
lcd.print("C");
lcd.print(" "); // now convert to Fahrenheit 
float temperatureF = (temperatureC * 9.0 / 5.0) + 32.0;
Serial.print(temperatureF); 
Serial.println(" degrees F");
lcd.setCursor(9, 1);
lcd.print(temperatureF);
lcd.print(" ");
lcd.print("F");
lcd.print(" ");

if(digitalRead(tilt)==HIGH)
{ 
lcd.clear();
lcd.print("Fall Detect"); 
lcd.setCursor(0,1); 
lcd.print("Wait 5 seconds"); 
start=millis(); 
delay(5000);
finished=millis();
Serial.println("Finished"); 
elapsed=finished-start; 
Serial.print(elapsed);
Serial.println(" milliseconds elapsed");
Serial.println(); 
delay(500);
lcd.clear(); 
tone(buzzer, 440, 250); 
lcd.print("Sending ALERT"); 
lcd.setCursor(0,1);
lcd.print("SMS & Emerg call");
delay(3000); 
lcd.clear();
} 
if(temperatureC > 38)
{ 
time_now = millis();
digitalWrite(green, LOW); 
digitalWrite(yellow, LOW);
digitalWrite(red, HIGH);
lcd.clear();
lcd.print("Alert Abnormal"); 
lcd.setCursor(0,1); 
lcd.print("Body Temperature"); 
while(millis() < time_now + period)
{ //wait approx. [period] ms }
}

}

}
int stop=(millis()/1000); 
int escape=stop-start;
int percent=map(escape,0,36000,0,100); \
Serial.print(percent);
Serial.print(" % "); 
// Serial.print(escape);
Serial.println("s");

}

void esp_8266()

{

// TCP connection AT+CIPSTART=4,"TCP","184.106.153.149",80

String cmd = "\nAT+CIPSTART=4,\"TCP\",\"";  // Establish TCP connection
/*
 AT+CIPSTART=id,type,addr,port
 
id: 0-4, id of connection
type: String, “TCP” or “UDP”
addr: String, remote IP
port: String, remote port

*/
cmd += "184.106.153.149"; // api.thingspeak.com

cmd += "\",80";

ser.println(cmd);

Serial.println(cmd); 

if(ser.find("Error"))

{

  Serial.println("AT+CIPSTART error");

  return;

}
String getStr = "GET /update?api_key="; // API key

getStr += apiKey;

//getStr +="&field1=";

//getStr +=String(h);

getStr +="&field1=";

getStr +=String(t);

getStr += "\r\n\r\n";

// send data length

cmd = "AT+CIPSEND="; // Send data AT+CIPSEND=id,length

cmd += String(getStr.length());

Serial.println(cmd);

Serial.println(cmd);

delay(1000);

Serial.print(getStr);

Serial.println(getStr);

// thingspeak needs 16 sec delay between updates

delay(16000);

} 
void gas()
{ 
if(valor>800)
{ 
tone(buzzer, 440, 250);
delay(2000); 
digitalWrite(red, HIGH); 
lcd.clear(); 
lcd.print("Harmful Gas"); 
lcd.setCursor(0,1); 
lcd.print("condition");
delay(1000); 
lcd.clear(); 
Serial.println(valor); 
}
}
