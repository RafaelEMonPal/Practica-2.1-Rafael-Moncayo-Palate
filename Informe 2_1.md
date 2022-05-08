# Practica-2.1-Rafael-Moncayo-Palate

## Codi de la pràctica
```cpp
#include <Arduino.h>
 
struct Button {
  const uint8_t PIN;
  uint32_t numberKeyPresses;
  bool pressed;
};
 
Button button1 = {5, 0, false};
 
void IRAM_ATTR isr() {
  button1.numberKeyPresses += 1;
  button1.pressed = true;
}
 
void setup()
{
  Serial.begin(115200);
  pinMode(button1.PIN, INPUT_PULLUP);
  attachInterrupt(button1.PIN, isr, FALLING);
}
 
void loop()
{
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

## Explicació de la pràctica

En aquesta pràctica ajuda per entendre que són les interrupcions, en aquest cas: per GPIO (General Porpouse InPut/OutPut).
Aquí primer creem un struct que serà el que ens crei una tupla per declarar un botó, amb pin d'entrada, un comptador de vegedas que ses pitja el botó i un boleà que ens ajudarà a declarar el seu estat (pressed(true)/not pressed(false)). Amb el VOID IRAM_ATTR lo que es crea és una espècie de handler per les interrupcions que fa que l'estat sigui pressed (true) i el contador augmenti +1. 
El setup  declara tot el que hem d'utilitzar en el loop i ens connecta l'interruptor amb el pin del botó i el procés IRAM_ATTR isr. En el loop el que passa es que cada vegada que el botó està en true, surt un missatge per pantalla. I després d'1 minut l'interruptor es desconnecta amb el "detachInterrupt".

I després de clicar vàries vegades els missatges que haurien de sortir serien aquests:

Button 1 has been pressed 1 times
Button 1 has been pressed 2 times
Button 1 has been pressed 3 times
Button 1 has been pressed 4 times
... 
I quan passi un minut per pantalla sortirà:

Interrupt Detached!

