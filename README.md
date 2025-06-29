# neopixel-fungi
Este proyecto trata de expresar las bioseñales de organismos como son los hongos transformando sus variaciones electricas en una experiencia visual a traves de leds neopixel los cambios de voltaje captados se traducen en un color distinto permitiendo observar la actividad bioelectrica de estos seres.       

bibliotecas externas:


Adafruit_Neopixel.h

Adafruit_ADS1X15.h

```ccp
#include <Adafruit_NeoPixel.h>
#include <Wire.h>
#include <Adafruit_ADS1X15.h>

#define PIN 6              // Pin de datos del anillo LED
#define NUM_LEDS 9         // Número de LEDs en el anillo

Adafruit_NeoPixel leds(NUM_LEDS, PIN, NEO_GRB + NEO_KHZ800);
Adafruit_ADS1115 ads;

int currentR = 0, currentG = 0, currentB = 0;

void setup() {
  Serial.begin(9600);
  leds.begin();
  leds.show();         // Apaga los LEDs al inicio
  ads.begin();         // Inicia el ADC ADS1115
}

void loop() {
  int16_t raw = ads.readADC_Differential_0_1(); // Leer diferencial entre A0 y A1
  float amplified = constrain((raw - 0) * 1.0, 0, 5000); // ya no amplificamos fuerte
  Serial.println(amplified);  // para que puedas ver los valores aún

  if (amplified >= 0 && amplified < 1000){
    fadeToColor(255, 86, 151, 10);    // Rosa pastel
  }
  else if (amplified >= 1000 && amplified < 2000 ) {
    fadeToColor(100, 192, 197, 20);   // Celeste suave
  }
  else if (amplified >= 2000 && amplified < 3000) {
    fadeToColor(84, 255, 127, 25);    // Verde menta
  }
  else if (amplified >= 3000 && amplified < 4000) {
    fadeToColor(255, 252, 60, 35);    // Amarillo pastel
  }
  else if (amplified >= 4000 && amplified <= 5000) {
    fadeToColor(255, 220, 240, 15);   // Rosa fuerte
  } 
  else {
    fadeToColor(0, 0, 0, 10);         // Apagado si no hay señal válida
  }
}

void fadeToColor(uint8_t r, uint8_t g, uint8_t b, int stepDelay) {
  int steps = 30;
  for (int i = 0; i <= steps; i++) {
    currentR = currentR + (r - currentR) / (steps - i + 1);
    currentG = currentG + (g - currentG) / (steps - i + 1);
    currentB = currentB + (b - currentB) / (steps - i + 1);
    setColor(currentR, currentG, currentB);
    delay(stepDelay);
  }
}

void setColor(int r, int g, int b) {
  for (int i = 0; i < NUM_LEDS; i++) {
    leds.setPixelColor(i, leds.Color(r, g, b));
  }
  leds.show();
}
```
