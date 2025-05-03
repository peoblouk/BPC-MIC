<!-- @format -->
<h1 align="center">
  <img alt="logo" src="logo.png" > BPC-ALD 
</h1>

Definice proměnných pro radost
```
typedef unsigned char uint8_t;
typedef unsigned int uint16_t;
```
----------------------------------------------
##Pull-up odpory pro tlacitka
```
const uint8_t PTAPE_INIT_VAL    = PTAPE_PTAPE6_MASK   // SW1
                                | PTAPE_PTAPE4_MASK   // SW3
                                | PTAPE_PTAPE7_MASK;  // SW2
      // SW4 už pull-up má, tudíž není třeba nastavovat

PTAPE = PTAPE_INIT_VAL;
```
----------------------------------------------
##Nastavení směru toku dat na portech LED
<b> 0 - Input           1 - Output</b>
```
/*
*   
*       
*/
const uint8_t PTCDD_INIT_VAL    = PTCDD_PTCDD2_MASK   // LED1
                                | PTCDD_PTCDD3_MASK   // LED2
                                | PTCDD_PTCDD4_MASK   // LED3
                                | PTCDD_PTCDD5_MASK;  // LED4
 
PTCDD = PTCDD_INIT_VAL;
```
----------------------------------------------
Čtení z tlačítek ("proměnné" co obsahují stav tlačítka)
*   Čtení z tlačítek ("proměnné" co obsahují stav tlačítka)
*   PULL-UP OFF
*   1 - Sepnuto; 0 - Rozepnuto
*   PULL-UP ON
*   1 - Rozepnuto; 0 - Sepnuto

PTAD_PTAD6; // SW1
PTAD_PTAD7; // SW2
PTAD_PTAD4; // SW3 
PTAD_PTCD7; // SW4

```
/*
*/
----------------------------------------------

/*
*   Nastavení IRQ interruptu. Zápis do IRQSC nad EnableInterrupts()!!!!!
*
*   IRQSC_IRQIE_MASK  - Povolení interruptu
*   IRQSC_IRQACK_MASK - Zápis pro clear náhodného interruptu při startu
*   IRQSC_IRQPE_MASK - Povolení pinu (SW4) jako generátoru IRQ interruptu
*   IRQSC_IRQPDD_MASK - Disable interniho pull-up odporu (davame protoze uz tam jeden pull up mame)
*   IRQSC_IRQEDG_MASK - Pokud přidáme, generuje interrupt na rising edge (tzn. na rozepnutí tlačítka)
*/
const uint8_t IRQSC_INIT_VAL  	= IRQSC_IRQIE_MASK
								| IRQSC_IRQACK_MASK
								| IRQSC_IRQPE_MASK
								| IRQSC_IRQPDD_MASK
                                /*| IRQSC_IRQEDG_MASK*/;
IRQSC = IRQSC_INIT_VAL;

//Interrupt IRQ ISR   -  Hlavička
interrupt VectorNumber_Virq void irqISR(void);

//Interrupt IRQ ISR   -  Základní tělo
interrupt VectorNumber_Virq void irqISR(void)
{
    /*Interrupt handler*/

    IRQSC_IRQACK = 1; // ACK interruptu
}
```



