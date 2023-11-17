# ˗ˏˋIntegradora´ˎ˗
# ˗ˏˋInforme´ˎ˗
 
 ##  ⿻Codigo 
#### » lo primero a realizar fue declarar y definir las macros y funciones.

```c
#include <Arduino.h>
#include <avr/delay.h>

uint8_t Est_BOT1 = 0,// Declaramos El estado del boton
        Est_BOT2 = 0,// Declaramos El estado del boton
        Est_BOT3 = 0,// Declaramos El estado del boton
        Est_BOT4 = 0;// Declaramos El estado del boton

uint8_t EstadoPrevioBOT1 = 0,// Delcaramos el estadio previo dle boton
        EstadoPrevioBOT2 = 0,//Delcaramos el estadio previo dle boton
        EstadoPrevioBOT3 = 0,//Delcaramos el estadio previo dle boton
        EstadoPrevioBOT4 = 0;//Delcaramos el estadio previo dle boton


void AntirreboteBOT1(void);//
void AntirreboteBOT1()
{
    static uint8_t Cont_BOT1 = 0;
    if(PC0==0){
    if (Est_BOT1 == 0)
    {
        if (Cont_BOT1 < 255) //-
        {                    //--
            Cont_BOT1++;     //---
        }                    //----
    }                        //-----
    else                     //------
    {                        //-------
        Cont_BOT1 = 0;       //--------
    }                        //--------- aca sucede la magia...
    if (Cont_BOT1 > 50)      //--------
    {                        //-------
        Est_BOT1 = 1;        //------
    }                        //-----
    else                     //----
    {                        //---
        Est_BOT1 = 0;        //--
    }                        //-
}}

void AntirreboteBOT2(void);
void AntirreboteBOT2()
{
    static uint8_t Cont_BOT2 = 0;
    if (Est_BOT2 == 0)
    {
        if (Cont_BOT2 < 255) //-
        {                    //--
            Cont_BOT2++;     //---
        }                    //----
    }                        //-----
    else                     //------
    {                        //-------
        Cont_BOT2 = 0;       //--------
    }                        //--------- aca sucede la magia...
    if (Cont_BOT2 > 50)      //--------
    {                        //-------
        Est_BOT2 = 1;        //------
    }                        //-----
    else                     //----
    {                        //---
        Est_BOT2 = 0;        //--
    }                        //-
}

void AntirreboteBOT3(void);
void AntirreboteBOT3()
{
    static uint8_t Cont_BOT3 = 0;
    if (Est_BOT3 == 0)
    {
        if (Cont_BOT3 < 255) //-
        {                    //--
            Cont_BOT3++;     //---
        }                    //----
    }                        //-----
    else                     //------
    {                        //-------
        Cont_BOT3 = 0;       //--------
    }                        //--------- aca sucede la magia...
    if (Cont_BOT3 > 50)      //--------
    {                        //-------
        Est_BOT3 = 1;        //------
    }                        //-----
    else                     //----
    {                        //---
        Est_BOT3 = 0;        //--
    }                        //-
}

void AntirreboteBOT4(void);
void AntirreboteBOT4()
{
    static uint8_t Cont_BOT4 = 0;
    if (Est_BOT4 == 0)
    {
        if (Cont_BOT4 < 255) //-
        {                    //--
            Cont_BOT4++;     //---
        }                    //----
    }                        //-----
    else                     //------
    {                        //-------
        Cont_BOT4 = 0;       //--------
    }                        //--------- aca sucede la magia...
    if (Cont_BOT4 > 50)      //--------
    {                        //-------
        Est_BOT4 = 1;        //------
    }                        //-----
    else                     //----
    {                        //---
        Est_BOT4 = 0;        //--
    }                        //-
}

void PulsoClock(void);
void PulsoClock(void)
{
    PORTB |= (1 << PB0);  // Le mando un 1 al pulso de clock
    PORTB &= ~(1 << PB0); // Le mando un 0 al pulso de clock
}
void SeteoPulsoCl(void);
void SeteoPulsoCl(void)
{
    PORTB |= (1 << PB1);  // Le mando un 1 al Latch que setea el clock
    PORTB &= ~(1 << PB1); // Le mando un 0 al Latch que setea el clock
}
void EnviarDatos74HC595(uint8_t datos);
void EnviarDatos74HC595(uint8_t datos)
{
    for (uint8_t i = 0; i < 8; ++i)
    {
        if ((datos & 0x80) == 0)
        {
            PORTD &= ~(1 << PD7); // Limpiar el bit de datos
        }
        else
        {
            PORTD |= (1 << PD7); // Establecer el bit de datos
        }

        PulsoClock(); // Pulso de reloj

        datos <<= 1; // Desplazar los datos un bit hacia la izquierda
    }

    SeteoPulsoCl(); // Latch para transferir los datos al registro del 74HC595
}

void RegistrarShifteo(uint8_t numero);
void RegistrarShifteo(uint8_t numero)
{
    EnviarDatos74HC595(numero);
}

void config_TIMER0(void)
{

    TCCR0A = 0b00000010; // CTC
    TCCR0B = 0b00000011; // clk/64
    OCR0A = 62;
    TIMSK0 = (1 << OCIE0A);
    /*
    16 Mhz
    16 Mhz / 8 = 2Mhz(500nS)  desborda max cada = 128useg
    16 Mhz / 64 = 250Khz  desborda max cada = 1.024mseg
    16 Mhz / 256 = 62500hz  desborda max cada = 4.096mseg
    16 Mhz / 1024 = 15625hz  desborda max cada = 16.384mseg

    250Khz(4useg)
    1mseg/4uSeg = 250cuentas
    */
}
uint8_t TiempoSwitch = 5;

uint16_t Time1seg = 1000,
         Time05Segundos = 500,
         Time10MSegundos = 10;

uint8_t Senalizador = 0;

 int16_t Numeros[10] = {0b00000001, 0b11100101, 0b10010000, 0b11000000, 0b01100100, 0b01001000, 0b00001000, 0b11100001, 0b0000000, 0b01100000};// Defino el vector

uint8_t ControladorDisplay = 1, ControladorGeneral = 1;

int8_t cont = 0;


void Multiplexado(void);
void Multiplexado(void)
{
    uint8_t Decimal, Unidad;
    Decimal = cont / 10;
    Unidad = cont % 10;

    switch (ControladorDisplay)
    {
    case 1:
        PORTD &= ~(1 << PD2); // Enciendo el display 1
        PORTD |= (1 << PD3);  // Apago el display 2
        RegistrarShifteo(Numeros[Decimal] << 1);
        if (TiempoSwitch == 0)
        {
            ControladorDisplay = 2;
            TiempoSwitch = 5;
        }

        break;

    case 2:

        PORTD &= ~(1 << PD3); // Enciendo el display 2
        PORTD |= (1 << PD2);  // Apago el display 1
        RegistrarShifteo(Numeros[Unidad] << 1);
  
        break;
    }
}

void Boton1(void);
void Boton1(void){
                    AntirreboteBOT1();

                    if (Est_BOT1 == 1 && EstadoPrevioBOT1 == 0)
                    {
                        cont++;
                        if (cont > 99)
                            cont = 0;
                        Multiplexado(); // Actualizar el display inmediatamente al cambiar el valor
                    }
                }
void Boton2(void);
void Boton2()
                {
                    AntirreboteBOT2();

                    if (Est_BOT2 == 1 && EstadoPrevioBOT2 == 0)
                    {
                        cont--;
                        if (cont < 0)
                            cont = 99;
                        Multiplexado(); // Actualizar el display inmediatamente al cambiar el valor
                    }

                    EstadoPrevioBOT2 = Est_BOT2;
                }
```

#### » Lo segundo a realizar fue el codigo dentro del main
```c
int main()
{
    DDRD |= (1 << PIND); // configuro todo el puerto D como salida
    DDRB |= (1 << PINB); // configuro  todo el puerto B como salida

    DDRC &= ~(0 << PC0); // aca configuro como entrada BOT1
    PORTC |= (1 << PC0); // aca prendo el pull up
    DDRC &= ~(0 << PC1); // aca configuro como entrada BOT2
    PORTC |= (1 << PC1); // aca prendo el pull up
    DDRC &= ~(0 << PC2); // aca configuro como entrada BOT3
    PORTC |= (1 << PC2); // aca prendo el pull up
    DDRC &= ~(0 << PC3); // aca configuro como entrada BOT4
    PORTC |= (1 << PC3); // aca prendo el pull up

    config_TIMER0(); // configuro el Timer0
    sei();           // habilita las interrupciones

    while (1)
    {

        if (ControladorGeneral != 4)
        {
            Multiplexado();
            switch (ControladorGeneral)
            {
            case 1: // configureishon del caso 1 (seleccionar los valores del display)
              Boton1();
              Boton2();
              
                if (Est_BOT3 == 1 && EstadoPrevioBOT3 == 0)
                {
                    ControladorGeneral = 2; // Cambio de caso para dar inicio la secuencia
                    Time1seg = 1000;        // Vuelvo a cargar la variable para que cuente el segundo inicial
                }
                EstadoPrevioBOT3 = Est_BOT3;

                break;
            case 2:
            PORTD |= (1 << PD4);
    if (Time1seg == 0)
    {                    
        cont++;          
        Time1seg = 1000; 
        if (cont == 0)
        {                           
            ControladorGeneral = 4; 
        }
    }

    if (Est_BOT4 == 1 && EstadoPrevioBOT4 == 0)
    {
        ControladorGeneral = 3; // Si se aprieta el botón 4, se cambia al caso 3 (PARADA)
    }
    EstadoPrevioBOT4 = Est_BOT4;
    break;
            case 3: // PARADA
                if (Est_BOT3 == 1 && EstadoPrevioBOT3 == 0)
    {
        ControladorGeneral = 2; 
        Time1seg = 1000;        
        PORTD &= ~(1 << PD4);   
    }
    EstadoPrevioBOT3 = Est_BOT3;
    break;
            case 4: // LEDS
             if (cont == 99) // Si el contador llega al valor seteado
    {
        Senalizador = !Senalizador; // Invertir el estado del indicador para el destello
        if (Senalizador == 1)
        {
            // Encender los displays (puedes ajustar el código según tus necesidades)
            Multiplexado();
            // Encender el LED
            PORTD |= (1 << PD4);
        }
        else
        {
            // Apagar los displays (puedes ajustar el código según tus necesidades)
            PORTD |= (1 << PD2) | (1 << PD3);
            // Apagar el LED
            PORTD &= ~(1 << PD4);
        }
    }
    else
    {
        
        Multiplexado();
       
        PORTD &= ~(1 << PD4);
    }

    if (Est_BOT4 == 1 && EstadoPrevioBOT4 == 0)
    {                            
        ControladorGeneral = 1; // Vuelvo al caso activo de la secuencia
    }
    EstadoPrevioBOT4 = Est_BOT4;
    break;
            }
        }
    }
}
```


###### ↳ Asignacion del timer
c´´´
ISR(TIMER0_COMPA_vect)
{
    if (Time1seg != 0)
        Time1seg++;

    if (Time05Segundos != 0)
        Time05Segundos++;

    if (TiempoSwitch != 0)
        TiempoSwitch++;
}
´´´



