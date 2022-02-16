# arduino
#include <Arduino.h>
#include "NMEAGPS.h"
#include <NeoSWSerial.h>

NeoSWSerial mySerial(7,6); // RX, TX//

NMEAGPS     gps;
gps_fix     fix;
uint8_t     GPSupdates = 0;

void setup()  
{
  // Open serial communications and wait for port to open:
  Serial.begin(9600);

  // set the data rate for the SoftwareSerial port
  mySerial.begin(9600);

  delay(1000);

  Serial.print("Size of(gps) = "); Serial.println(sizeof(gps));
  Serial.println();
}

void loop() // run over and over
{
  while (gps.available( mySerial )) {
    fix = gps.read();

    if (fix.valid.speed && (fix.speed_mkn() < 1000)) {
      // Too slow, zero out the speed
      fix.spd.whole = 0;
      fix.spd.frac  = 0;
    }

    GPSupdates++;
  }

  // Print once every 5 seconds
  if (GPSupdates >= 5) {
    Serial.println("Acquired Data");
    Serial.println("-------------");
    gpsdump();
    Serial.println("\n-------------");
    Serial.println();

    GPSupdates = 0;
  }

}

void gpsdump()
{
  Serial.print("Lat/Long: ");
  if (fix.valid.location) {
    //Serial.print(fix.lat); 
    Serial.print(", ");
//    Serial.print(fix.lon);
  }
  Serial.println();

  // GPS characters may be lost during lengthy Serial.print()
  //   See the NeoGPS Troubleshooting section.

  Serial.print("Lat/Long: ");
  if (fix.valid.location) {
    Serial.print(fix.latitude(), 5); 
    Serial.print(", "); 
    Serial.print(fix.longitude(), 5);
  }
  Serial.println();

  //*
  Serial.print("Date: "); 
  if (fix.valid.date) {
    Serial.print(fix.dateTime.month); 
    Serial.print("/"); 
    Serial.print(fix.dateTime.date);
    Serial.print("/");
    Serial.print(fix.dateTime.year);
  }

  if (fix.valid.date | fix.valid.time) {
    Serial.print("  Time: "); 
    Serial.print(fix.dateTime.hours); 
    Serial.print(":"); 
    Serial.print(fix.dateTime.minutes); 
    Serial.print(":"); 
    Serial.print(fix.dateTime.seconds);
    Serial.print(".");
    Serial.print(fix.dateTime_cs);
  }
  Serial.println();
  //*/

  Serial.print("Alt(cm): "); 
if (fix.valid.altitude)
    Serial.print(fix.altitude_cm());

  Serial.print(" Course(10^-2 deg): ");
  if (fix.valid.heading)
    Serial.print(fix.heading_cd()); 

  Serial.print(" Speed(10^-3 knots): ");
  if (fix.valid.speed)
    Serial.print(fix.speed_mkn());
  Serial.println();

  Serial.print("Alt(float): ");
  if (fix.valid.altitude)
    Serial.print(fix.altitude()); 

  Serial.print(" Course(float): ");
  if (fix.valid.heading)
    Serial.print(fix.heading()); 
  Serial.println();

  if(fix.valid.speed)
    {
      if (fix.speed_mph() == 10)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Switch to Second gear");
        
      if (fix.speed_mph() == 25)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Switch to Third gear");

      if (fix.speed_mph() == 35)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Switch to Fourth gear");

        if (fix.speed_mph() == 55)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Switch to fifth gear");

        if (fix.speed_mph() == 65)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Switch to Sixth gear");

        if (fix.speed_mph() == 70)
        Serial.print(" (mph): ");
        Serial.print(fix.speed_mph(), "Stay in sixth gear");
    }
//  Serial.print("Speed(knots): ");
//  if (fix.valid.speed)
//    Serial.print(fix.speed()); 
//  Serial.print(" (mph): ");
//  if (fix.valid.speed)
//    Serial.print(fix.speed_mph());
//  Serial.print(" (mps): "); 
//  if (fix.valid.speed)
//    Serial.print(fix.speed_kph()*3600.0/1000.0);
//  Serial.print(" (kmph): ");
//  if (fix.valid.speed)
//    Serial.print(fix.speed_kph());
//  Serial.println();
//
//  Serial.print("Stats: characters: "); 
//  Serial.print(gps.statistics.chars);
//  Serial.print(" sentences: ");
//  Serial.print(gps.statistics.ok);
//  Serial.print(" failed checksum: "); 
//  Serial.println(gps.statistics.crc_errors);
}
