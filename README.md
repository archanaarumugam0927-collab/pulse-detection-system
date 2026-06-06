# pulse-detection-system

/* Heart Pulse Sensor with Arduino + OLED
   https://srituhobby.com
*/

#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 display(128, 64, &Wire);

#define sensor A0
#define Highpulse 540

int sX = 0;
int sY = 60;
int x = 0;

int Svalue;
int value;

long Stime = 0;
long Ltime = 0;

int count = 0;
int Bpm = 0;

void setup() {
  Serial.begin(9600);

  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  delay(1000);

  display.clearDisplay();
}

void loop() {
  Svalue = analogRead(sensor);
  Serial.println(Svalue);

  value = map(Svalue, 0, 1023, 0, 45);
  int y = 60 - value;

  if (x > 128) {
    x = 0;
    sX = 0;
    display.clearDisplay();
  }

  display.drawLine(sX, sY, x, y, WHITE);

  sX = x;
  sY = y;
  x++;

  BPM();

  display.setCursor(0, 0);
  display.setTextSize(2);
  display.setTextColor(SSD1306_WHITE);
  display.print("BPM:");
  display.display();
}

void BPM() {
  if (Svalue > Highpulse) {
    Stime = millis() - Ltime;
    count++;

    if (Stime >= 60000) {   // 1 minute
      Ltime = millis();

      Serial.println(count);

      display.setCursor(60, 0);
      display.setTextSize(2);
      display.setTextColor(SSD1306_WHITE);

      display.print(count);
      display.print(" ");

      display.display();

      count = 0;
    }
  }
}
