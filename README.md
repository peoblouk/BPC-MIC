<!-- @format -->
<h1 align="center">
  <img alt="logo" src="logo.png" > BPC-MIC test 2 
</h1>

## Definice proměnných pro radost
```
typedef unsigned char uint8_t;
typedef unsigned int uint16_t;
```
----------------------------------------------

## Pull-up odpory pro tlacitka
```
const uint8_t PTAPE_INIT_VAL    = PTAPE_PTAPE6_MASK   // SW1
                                | PTAPE_PTAPE4_MASK   // SW3
                                | PTAPE_PTAPE7_MASK;  // SW2
      // SW4 už pull-up má, tudíž není třeba nastavovat

PTAPE = PTAPE_INIT_VAL;
```
----------------------------------------------

## Nastavení směru toku dat na portech LED
- <b> 0 - Input
- 1 - Output</b>
```
const uint8_t PTCDD_INIT_VAL    = PTCDD_PTCDD2_MASK   // LED1
                                | PTCDD_PTCDD3_MASK   // LED2
                                | PTCDD_PTCDD4_MASK   // LED3
                                | PTCDD_PTCDD5_MASK;  // LED4
 
PTCDD = PTCDD_INIT_VAL;
```
----------------------------------------------

## Čtení z tlačítek ("proměnné" co obsahují stav tlačítka)
- Čtení z tlačítek ("proměnné" co obsahují stav tlačítka)
- PULL-UP OFF
- 1 - Sepnuto; 0 - Rozepnuto
- PULL-UP ON
- 1 - Rozepnuto; 0 - Sepnuto

PTAD_PTAD6; // SW1
PTAD_PTAD7; // SW2
PTAD_PTAD4; // SW3 
PTAD_PTCD7; // SW4

```
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
----------------------------------------------

## Nastavení KBI (Keyboard Interrupt)
- Zápis do registrů nad EnableInterrupts()!!!!!
- KBIPE REGISTR - Povolení generace interruptu z vybraných KBI pinů

```
const uint8_t KBIPE_INIT_VAL    = KBIPE_KBIPE6_MASK     // SW1
                                | KBIPE_KBIPE7_MASK     // SW2
                                | KBIPE_KBIPE4_MASK;    // SW3
                                
// PRO DETEKCI POUZE HRAN (VĚTŠINOU POŽADOVÁNO) 
const uint8_t KBISC_INIT_VAL    = 0;       
// PRO DETEKCI OBOU HRAN A ÚROVNĚ
const uint8_t KBISC_INIT_VAL    = KBISC_KBIMOD_MASK;                                
/*
*   POSTUP ZAPNUTÍ KBI INTERRUPTU
*/
KBISC_KBIE = KBISC_INIT_VAL; 
KBIPE = KBIPE_INIT_VAL;
KBISC_KBACK = 1;
KBISC_KBIE = 1;

//Interrupt TOD ISR   -  Hlavička
interrupt VectorNumber_Vkeyboard void kbiISR(void);

//Interrupt TOD ISR   -  Základní tělo
interrupt VectorNumber_Vkeyboard void kbiISR(void)
{
	KBISC_KBACK = 1;
}
```
----------------------------------------------

## Nastavení Time of Day interruptu
- Zápis do registrů nad EnableInterrupts()!!!!!
- TODC REGISTR
- TODC_TODCLKS - Nastavení používaného hodinového vstupu
- Na hodinách jsme používali buď LPO nebo OSCOUT
- 00 - OSCOUT            
- 01 - LPO
- TODC_TODPS - Nastavení předděličky
-  00 - Použijeme pro LPO
-  01 - Použijeme pro OSCOUT
-   TĚSNĚ PŘED EnableInterrupts je poté nutné povolit Time of Day pomocí následujícího řádku*/ <b>TODC  |= TODC_TODEN_MASK; </b>

-   TODSC REGISTR ->
- 	TODSC_MTCHEN - Povoléní matchování
- 	TODSC_MTCHIE - Povolení přerušení při každé match-sekundě
- 	TODSC_QSECIE - Povolení přerušení při každé čtvrt-sekundě
- 	TODSC_SECIE  - Povolení přerušení při každé sekundě
-   POKUD SE POUŽÍVÁ MATCH, JE TŘEBA NASTAVIT MATCH HODNOTU
-   TODM - Match registr. Vložit do něj počet požadovaných Match-sekund

```
// UKÁZKA NASTAVENÍ TODC REGISTRU
//-----------
// Pokud se používají LPO 
const uint8_t TODC_INIT_VAL = TODC_TODCLKS0_MASK; 

TODC = TODC_INIT_VAL;

// Pokud se používají OSCOUT
const uint8_t TODC_INIT_VAL     = TODC_TODPS0_MASK;
const uint8_t ICSC2_INIT_VAL    = ICSC2_EREFS_MASK
			                    | ICSC2_ERCLKEN_MASK;
TODC =  TODC_INIT_VAL;
ICSC2 = ICSC2_INIT_VAL;

//-----------
// UKÁZKA NASTAVENÍ TODSC REGISTRU A MATCH REGISTRU
//-----------
const uint8_t TODSC_INIT_VAL    = TODSC_MTCHIE_MASK
                                | TODSC_MTCHEN_MASK
                                | TODSC_QSECIE_MASK
                                | TODSC_SECIE_MASK;

const uint8_t TODM_INIT_VAL = 4;

TODSC = TODSC_INIT_VAL;
TODM  = TODM_INIT_VAL;

//Interrupt TOD ISR   -  Hlavička
interrupt VectorNumber_Vtod void todISR(void);

//Interrupt TOD ISR   -  Základní tělo
interrupt VectorNumber_Vtod void todISR(void)
{
    /*Interrupt handler*/

    TODSC = TODSC; // ACK interruptu
}
```
----------------------------------------------

