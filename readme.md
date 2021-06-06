# PRÀCTICA 2
## Apartat A
```
#include <Arduino.h>

struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
};

Button button1 = {0, 0, false};

void IRAM_ATTR isr() {
  button1.numberKeyPresses += 1;
  button1.pressed = true;
}

void setup() {
  Serial.begin(9600);
  pinMode(button1.PIN, INPUT_PULLUP);
  attachInterrupt(button1.PIN, isr, FALLING);
}

void loop() {
  if (button1.pressed) {
      Serial.printf("Button 1 has been pressed %u times\n", button1.numberKeyPresses);
      button1.pressed = false;
  }

  //Detach Interrupt after 1 Minute
  static uint32_t lastMillis = 0;
  if (millis() - lastMillis > 60000) {
    lastMillis = millis();
    detachInterrupt(button1.PIN);
     Serial.println("Interrupt Detached!");
  }
}
```
## FUNCIONAMIENTO

Primero utilizamos la función attachInterrupt( ISR, Mode,GPIOPin); para establecer una interrupción en base a un pin por pin. 
En button1.PIN estamos denominando que clavija GPIO usaremos como una clavija de interrupción. En FALLING estamos indicando que los disparadores interrumpen cuando el pin va de HIGH a LOW. Y en isr estamos llamando a la rutina de servicio de interrupción.
Seguidamente crearemos un (bucle) en el que cada vez que se pulse el button1 el monitor mostrará por pantalla el número de veces que se ha pulsado el botón.
Al pasar 1 minuto, automaticamente desconectara la interrupción del pin GPIO.


