# GPSModules

#### Testované moduly: 
- NEO-6M - [obchod](https://arduino-shop.cz/arduino/1510-gps-neo-6m-gyneo6mv2-modul-s-antenou.html?gclid=EAIaIQobChMIl8ecrNm54wIVF-d3Ch2MSg4FEAQYASABEgKXpPD_BwE) | [návod](https://navody.arduino-shop.cz/navody-k-produktum/arduino-gps-modul-neo-6m.html)
- NEO-7M - [obchod](https://arduino-shop.cz/arduino/1733-gps-satelitni-urceni-polohy-neo-7m-modul-pro-arduino-stm32-c51.html) | [datasheet](https://arduino-shop.cz/docs/produkty/0/474/1502177743.pdf) 

#### Knihovna

Knihovna: [TinyGPSPlus](https://github.com/mikalhart/TinyGPSPlus) 

Metody: 

```c
gps.location.lat() // Latitude in degrees (double)
gps.location.lng() // Longitude in degrees (double)
gps.location.rawLat().negative ? "-" : "+"
gps.location.rawLat().deg // Raw latitude in whole degrees
gps.location.rawLat().billionths// ... and billionths (u16/u32)
gps.location.rawLng().negative ? "-" : "+");
gps.location.rawLng().deg // Raw longitude in whole degrees
gps.location.rawLng().billionths// ... and billionths (u16/u32)
gps.date.value() // Raw date in DDMMYY format (u32)
gps.date.year() // Year (2000+) (u16)
gps.date.month() // Month (1-12) (u8)
gps.date.day() // Day (1-31) (u8)
gps.time.value() // Raw time in HHMMSSCC format (u32)
gps.time.hour() // Hour (0-23) (u8)
gps.time.minute() // Minute (0-59) (u8)
gps.time.second() // Second (0-59) (u8)
gps.time.centisecond() // 100ths of a second (0-99) (u8)
gps.speed.value() // Raw speed in 100ths of a knot (i32)
gps.speed.knots() // Speed in knots (double)
gps.speed.mph() // Speed in miles per hour (double)
gps.speed.mps() // Speed in meters per second (double)
gps.speed.kmph() // Speed in kilometers per hour (double)
gps.course.value() // Raw course in 100ths of a degree (i32)
gps.course.deg() // Course in degrees (double)
gps.altitude.value() // Raw altitude in centimeters (i32)
gps.altitude.meters() // Altitude in meters (double)
gps.altitude.miles() // Altitude in miles (double)
gps.altitude.kilometers() // Altitude in kilometers (double)
gps.altitude.feet() // Altitude in feet (double)
gps.satellites.value() // Number of satellites in use (u32)
gps.hdop.value() // Horizontal Dim. of Precision (100ths-i32)
```

použití:

```c
#include <TinyGPS++.h>
#include <SoftwareSerial.h>

// Choose two Arduino pins to use for software serial
int RXPin = 2;
int TXPin = 3;

int GPSBaud = 9600;

// Create a TinyGPS++ object
TinyGPSPlus gps;

// Create a software serial port called "gpsSerial"
SoftwareSerial gpsSerial(RXPin, TXPin);

void setup()
{
  // Start the Arduino hardware serial port at 9600 baud
  Serial.begin(9600);

  // Start the software serial port at the GPS's default baud
  gpsSerial.begin(GPSBaud);
}

void loop()
{
  // This sketch displays information every time a new sentence is correctly encoded.
  while (gpsSerial.available() > 0)
    if (gps.encode(gpsSerial.read()))
      displayInfo();

  // If 5000 milliseconds pass and there are no characters coming in
  // over the software serial port, show a "No GPS detected" error
  if (millis() > 5000 && gps.charsProcessed() < 10)
  {
    Serial.println("No GPS detected");
    while(true);
  }
}

void displayInfo()
{
  if (gps.location.isValid())
  {
    Serial.print("Latitude: ");
    Serial.println(gps.location.lat(), 6);
    Serial.print("Longitude: ");
    Serial.println(gps.location.lng(), 6);
    Serial.print("Altitude: ");
    Serial.println(gps.altitude.meters());
  }
  else
  {
    Serial.println("Location: Not Available");
  }
  
  Serial.print("Date: ");
  if (gps.date.isValid())
  {
    Serial.print(gps.date.month());
    Serial.print("/");
    Serial.print(gps.date.day());
    Serial.print("/");
    Serial.println(gps.date.year());
  }
  else
  {
    Serial.println("Not Available");
  }

  Serial.print("Time: ");
  if (gps.time.isValid())
  {
    if (gps.time.hour() < 10) Serial.print(F("0"));
    Serial.print(gps.time.hour());
    Serial.print(":");
    if (gps.time.minute() < 10) Serial.print(F("0"));
    Serial.print(gps.time.minute());
    Serial.print(":");
    if (gps.time.second() < 10) Serial.print(F("0"));
    Serial.print(gps.time.second());
    Serial.print(".");
    if (gps.time.centisecond() < 10) Serial.print(F("0"));
    Serial.println(gps.time.centisecond());
  }
  else
  {
    Serial.println("Not Available");
  }

  Serial.println();
  Serial.println();
  delay(1000);
}
```
