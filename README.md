<!-- @format -->
<h1 align="center">
  <img alt="logo" src="img/logo.png" > BPC-ALD 
</h1>

<b>Slovník</b>
- Iterátor - slouží obečně pro iterování v nějakém souboru dat (neboli procházení třeba jednotlivých složkách seznamu
- ADT - alternativní datový typ
- časová Amortizace - znamená, že když dojde k naplnění například pole, tak se dopředu naalokuje 2x více paměti, naopak při odstraňování když klesne na 75% kapacity, tak se pole uvolní
----------------------------------------------
## TVector

- Typ Vector spravuje dynamicky alokované pole prvků typu VectorElement a umožňuje s nimi pracovat pomocí definovaného API
  
- <b>POZOR !!! Pokud jsem změnil typ TVectorElement z int na char musím najít makro #define TVECTOR_ELEMENT_FRMSTR   "%d" </b>

```
struct TVector
	{
	TVectorElement *iValues;	///< Ukazatel na počáteční prvek pole hodnot typu VectorElement
	size_t iSize;	           	///< Počet elementů vektoru
	};
struct TVectorIterator
	{
	struct TVector *iVector;	///< Ukazatel na navázaný vektor (mutable iterátor - umožňuje měnit elementy VectorElement)
	size_t iPos;			///< Aktuální pozice pro indexaci elementu v navázaném vektoru
	};
```
<img alt="TVector_funkce" src="img/tvector_funkce.png" >

----------------------------------------------
