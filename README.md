# ˗ˏˋTrabajo Practico N°5´ˎ˗
## ⿻Consignas:
##### Retome el programa del TP3 y modifiquelo tal que:
##### 1. Utilice Interrupciones por TIMER para controlar la velocidad del efecto de luces.
##### 2. Utilizar detección de flanco y eliminación de rebote por interrupción eliminando el código bloqueante.

# ˗ˏˋInforme´ˎ˗
 
 ##  ⿻Codigo 
#### » lo primero a realizar fue declarar y definir las macros y funciones.
###### ↳ Defino los pines(macros.h)
```c
#define bot1 ((PINC >> 5) & 0x01) // Defino los botones
#define bot2 ((PINC >> 4) & 0x01) // Defino los botones
#define bot3 ((PINC >> 3) & 0x01) // Defino los botones
#define bot4 ((PINC >> 2) & 0x01) // Defino los botones

#define led1 (PORTB |= (1 << PB1)) // Defino Led1
#define led2 (PORTB |= (1 << PB0)) // Defino Led2
#define led3 (PORTD |= (1 << PD2)) // Defino Led3
#define led4 (PORTD |= (1 << PD3)) // Defino Led4
#define led5 (PORTD |= (1 << PD4)) // Defino Led5
#define led6 (PORTD |= (1 << PD5)) // Defino Led6
#define led7 (PORTD |= (1 << PD6)) // Defino Led7
#define led7 (PORTD |= (1 << PD6)) // Defino Led7
#define led8 (PORTD |= (1 << PD7)) // Defino Led8
```
###### ↳ Luego Declaro los botones como entradas y los leds como salida
```c
void config_outint(void);
void config_outint(void){
     DDRC &= ~(1 << PC5); // aca configuro como entrada
  PORTC |= (1 << PC5); // aca prendo el pull up
  DDRC &= ~(1 << PC4); // aca configuro como entrada
  PORTC |= (1 << PC4); // aca prendo el pull up
  DDRC &= ~(1 << PC3); // aca configuro como entrada
  PORTC |= (1 << PC3); // aca prendo el pull up
  DDRC &= ~(1 << PC2); // aca configuro como entrada
  PORTC |= (1 << PC2); // aca prendo el pull up

  DDRB |= (1 << PB1); // En esta linea configuro como salida
  DDRB |= (1 << PB0); // En esta linea configuro como salida
  DDRD |= (1 << PD2); // En esta linea configuro como salida
  DDRD |= (1 << PD3); // En esta linea configuro como salida
  DDRD |= (1 << PD4); // En esta linea configuro como salida
  DDRD |= (1 << PD5); // En esta linea configuro como salida
  DDRD |= (1 << PD6); // En esta linea configuro como salida
  DDRD |= (1 << PD7); // En esta linea configuro como salida
}
```
###### ↳ Defino las variables(macros.h)
```c
int ccont_bto1=0;
int FLAG_b1=0;
int EST_1=0;
int ccont_bto23=0;
int FLAG_b23=0;
int EST_23=0;
int ccont_bto24=0;
int FLAG_b24=0;
int EST_24=0;

#define si 1
#define no 0
```
###### ↳ inicio
```c
#include <Arduino.h>
#include "macros.h"
#include "util/delay.h" // habilito la carpeta del delay
void config_TIMER1(void); // Funcion del Timer
uint16_t T1 = 200; // declaro la variable tiempo (T1)
```

#### » Lo segundo a realizar fue el codigo dentro del main
```c
int main()
{
  config_outint();

  config_TIMER1(); // configuro el Timer1(16 bits)
  sei();           // habilita las interrupciones

  while (1)
  {
  }
}
```
#### » Lo tercero a realizar fue el codigo dentro de la interrucion
###### ↳ Empieza con el antirrebote del Boton1
```c
ISR(TIMER1_COMPA_vect) // cual de las interrucion quiero
{

  static uint8_t on;
  if (bot1 == 0) // si el boton 1 esta prendido...
  {
    if (ccont_bto1 < 255) // no se pase del valor max
    {
      ccont_bto1++;
    }
  }
  else
  {
    ccont_bto1 = 0;
  }
  if (ccont_bto1 > 50) // espero hasta 50 mseg
  {
    FLAG_b1 = 1; // si se detecto que el boton 1 paso esos 50 mseg apretado la flag cambia de estado a 1
  }
  else
  {
    FLAG_b1 = 0; // de lo contrario su estado sera 0
  }

  if (EST_1 == 0 && FLAG_b1 == 1) // En este if comparo los estados del estado anterior y la flag
  {

    if (on == si) // Todo este if sirve para apagar o prender el boton(si= 1---no=0)
    {
      on = no;
    }
    else
    {
      on = si;
    }
  }
  EST_1 = FLAG_b1; // Actualizo el valor del estado
```

###### ↳ Pregunto a los botones correspondientes para disminuir la velocidad del efecto con su respectivo antirrebote
 ```c
 if ((bot2 == 0 && bot3 == 0)) // si el boton 2 y 3 estan prendidos...
  {
    if (ccont_bto23 < 255) // no sobrepase el valor max
    {
      ccont_bto23++;
    }
  }
  else
  {
    ccont_bto23 = 0;
  }
  if (ccont_bto23 > 50) // espero hasta 50 mseg
  {
    FLAG_b23 = 1; // si se detecto que el boton 2 y3  pasasaron esos 50 mseg apretados la flag cambia de estado a 1
  }
  else
  {
    FLAG_b23 = 0; // de lo contrario su estado sera 0
  }
  if (EST_23 == 0 && FLAG_b23 == 1) // En este if comparo los estados del estado anterior y la flag
  {
    if (T1 >= 500) // condiciono a la variable t para que no sobrepase 500 la cual elegi como limite (limite del uint8_t = 255)
    {
      T1 = 500; // se queda en el valor hasta que no se veulva a modificar
    }

    T1 += 100; // Esta linea hace que la velocidad(t) se incremente  y disminuya la velocidad
  }
  EST_23 = FLAG_b23; // Actualizo el valor del estado
```
###### ↳ Pregunto a los botones correspondientes para aumentar la velocidad del efecto con su respectivo antirrebote
 ```c
 if ((bot2 == 0 && bot4 == 0)) // si el boton 2 y 3 estan prendidos...
  {
    if (ccont_bto24 < 255) // no sobrepase el valor max
    {
      ccont_bto24++;
    }
  }
  else
  {
    ccont_bto24 = 0;
  }
  if (ccont_bto24 > 50) // espero hasta 50 mseg
  {
    FLAG_b24 = 1; // si se detecto que el boton 2 y 4  pasasaron esos 50 mseg apretados la flag cambia de estado a 1
  }
else
{
  FLAG_b24 = 0; // de lo contrario su estado sera 0
}
if (EST_24 == 0 && FLAG_b24 == 1) // En este if comparo los estados del estado anterior y la flag
{
  if (T1 <= 200) // condiciono a la velocidad (t) a no decrementar mas de 70
  {
    T1 = 200; // se queda en el valor hasta que no se vuelva a modificar
  }
  T1 -= 50; // Esta linea hace que la velocidad (t) se decremente y aumente la velocidad
}
EST_24 = FLAG_b24; // Actualizo el valor del estado
```
###### ↳Llamo a la secuencia 
```c
if (on)
{
  static uint16_t ccnt2 = 0; // Este es el contador del TIMER
  ccnt2++;
  if (ccnt2 >= T1)
  {
    if (cont >= 15) //  Condiciono al contador no sobrepasar a la posicion 15
    {
      cont = 0; // si llega a la posicion 15 vuelve a la posicion 0
    }
    cont++;
    PORTD = (PORTD & 0b00000011) | (efecto_leds[cont] & 0b11111100); // |--> ENMASCARAMIENTO: El enmascaramiento sirve para utilizar distintos pines de distintos puertos(PORTD)
    PORTB = (PORTB & 0b11111100) | (efecto_leds[cont] & 0b00000011); // |--> ENMASCARAMIENTO: El enmascaramiento sirve para utilizar distintos pines de distintos puertos(PORTB)

    ccnt2 = 0;
  }
}
}
```

###### ↳ Asignacion del timer
```c
void config_TIMER1(void)
{


  // TCCR0A = 0xFF;
  TCCR1A = 0b00000000; // CTC
  TCCR1B = 0b00001011; // clk/64
  TCCR1C = 0;
  OCR1A = 249;
  TIMSK1 = (1 << OCIE1A);
}

```
# ˗ˏˋDiagrama de flujo´ˎ˗

![Diagrama de Flujo](Diagrama_tp5.drawio.png"Diagrama")

