---
title: 'Dela data: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Dela data i Azure Machine Learning för att dela upp en datauppsättning i två distinkta uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455901"
---
# <a name="split-data-module"></a>Modul för delade data

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd modulen Dela data för att dela upp en datauppsättning i två distinkta uppsättningar.

Den här modulen är användbar när du behöver separera data i utbildnings- och testuppsättningar. Du kan också anpassa hur data delas upp. Vissa alternativ stöder randomisering av data. Andra är skräddarsydda för en viss datatyp eller modelltyp.

## <a name="configure-the-module"></a>Konfigurera modulen

> [!TIP]
> Innan du väljer delningsläge läser du alla alternativ för att bestämma vilken typ av delning du behöver.
> Om du ändrar delningsläget kan alla andra alternativ återställas.

1. Lägg till modulen **Dela data** i pipelinen i designern. Du hittar den här modulen under **Dataomvandling**i kategorin **Exempel och Dela.**

1. **Delningsläge:** Välj ett av följande lägen, beroende på vilken typ av data du har och hur du vill dela upp den. Varje delningsläge har olika alternativ.

   - **Delade rader:** Använd det här alternativet om du bara vill dela upp data i två delar. Du kan ange hur många procent av data som ska läggas i varje delning. Som standard delas data 50/50.

     Du kan också randomisera valet av rader i varje grupp och använda stratifierad sampling. I stratifierad sampling måste du välja en enda kolumn med data som du vill att värdena ska fördelas lika mellan de två resultatdatauppsättningarna.  

   - **Delning av reguljära uttryck:** Välj det här alternativet när du vill dela upp datauppsättningen genom att testa en enskild kolumn för ett värde.

     Om du till exempel analyserar sentimentet kan du söka efter förekomsten av ett visst produktnamn i ett textfält. Du kan sedan dela upp datauppsättningen i rader med målproduktens namn och rader utan målproduktnamnet.

   - **Delning av relativa uttryck:** Använd det här alternativet när du vill använda ett villkor på en talkolumn. Talet kan vara ett datum-/tidsfält, en kolumn som innehåller ålders- eller dollarbelopp eller till och med en procentsats. Du kanske till exempel vill dela upp datauppsättningen baserat på kostnaden för artiklarna, gruppera personer efter åldersintervall eller separera data efter ett kalenderdatum.

### <a name="split-rows"></a>Dela rader

1. Lägg till modulen [Dela data](./split-data.md) i pipelinen i designern och anslut den datauppsättning som du vill dela.
  
1. För **delningsläge**väljer du **Dela rader**. 

1. **Bråk av rader i den första utdatauppsättningen**: Använd det här alternativet för att bestämma hur många rader som ska gå in i den första (vänstra sidan) utdata. Alla andra rader går in i den andra (högra sidan) utdata.

   Förhållandet representerar procentandelen rader som skickas till den första utdatauppsättningen, så du måste ange ett decimaltal mellan 0 och 1.
     
   Om du till exempel anger **0,75** som värde delas datauppsättningen 75/25. I den här delningen skickas 75 procent av raderna till den första utdatauppsättningen. Resterande 25 procent skickas till den andra utdatauppsättningen.
  
1. Välj alternativet **Randomiserad delning** om du vill randomisera valet av data i de två grupperna. Det här är det alternativ som föredras när du skapar tränings- och testdatauppsättningar.

1. **Slumpmässigt utsprut:** Ange ett icke-negativt heltalsvärde för att starta pseudorandom-sekvensen av förekomster som ska användas. Det här standardutsädet används i alla moduler som genererar slumptal. 

   Om du anger ett frö kan resultaten reproduceras. Om du behöver upprepa resultatet av en delad operation bör du ange ett frö för slumptalsgeneratorn. Annars ställs det slumpmässiga ut fröet som standard till **0**, vilket innebär att det ursprungliga frövärdet erhålls från systemklockan. Därför kan fördelningen av data vara något annorlunda varje gång du utför en delning. 

1. **Stratifierad split**: Ställ in det här alternativet på **True** för att säkerställa att de två utdatauppsättningarna innehåller ett representativt urval av värdena i *stratakolumnen* eller *stratifieringsnyckelkolumnen*. 

   Med stratifierad sampling delas data upp så att varje utdatauppsättning får ungefär samma procentandel av varje målvärde. Du kanske till exempel vill se till att dina tränings- och testuppsättningar är ungefär balanserade när det gäller resultatet eller någon annan kolumn (till exempel kön).

1. Skicka pipelinen.


## <a name="select-a-regular-expression"></a>Markera ett reguljärt uttryck

1. Lägg till modulen [Dela data](./split-data.md) i pipelinen och anslut den som indata till den datauppsättning som du vill dela.  
  
1. För **delningsläge**väljer du **Delning av reguljärt uttryck**.

1. Ange ett giltigt reguljärt uttryck i rutan **Reguljärt uttryck.** 
  
   Det reguljära uttrycket bör följa Python-syntaxen för reguljära uttryck.

1. Skicka pipelinen.

   Baserat på det reguljära uttryck som du anger är datauppsättningen uppdelad i två rader: rader med värden som matchar uttrycket och alla återstående rader. 

Följande exempel visar hur du delar upp en datauppsättning med alternativet **Reguljärt uttryck.** 

### <a name="single-whole-word"></a>Ett helt ord 

I det här exemplet placeras alla rader som `Gryphon` innehåller texten `Text`i kolumnen i den första datauppsättningen . Det placerar andra rader i den andra utdata av **Split Data**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Delsträng

Det här exemplet söker efter den angivna strängen på valfri position i den andra kolumnen i datauppsättningen. Positionen betecknas här av indexvärdet 1. Matchen är skiftlägeskänslig.

```text
(\1) ^[a-f]
```

Den första resultatdatauppsättningen innehåller alla rader där indexkolumnen börjar `a` `b`med `c` `d`ett `e` `f`av följande tecken: , , , , , . Alla andra rader dirigeras till den andra utmatningen.

## <a name="select-a-relative-expression"></a>Markera ett relativt uttryck

1. Lägg till modulen [Dela data](./split-data.md) i pipelinen och anslut den som indata till den datauppsättning som du vill dela.
  
1. För **delningsläge**väljer du **Relativt uttryck**.
  
1. I rutan **Relationsuttryck** anger du ett uttryck som utför en jämförelseåtgärd i en enda kolumn.

   För **numerisk kolumn:**
   - Kolumnen innehåller tal av alla numeriska datatyper, inklusive datum- och tidsdatatyper.
   - Uttrycket kan referera till högst ett kolumnnamn.
   - Använd et-tecknet `&`, för åtgärden OCH. Använd `|`pipe-tecknet, för ELLER-funktionen.
   - Följande operatorer stöds: `<` `>`, `<=` `>=`, `==` `!=`, , .
   - Du kan inte gruppera `(` `)`åtgärder med hjälp av och .
   
   För **strängkolumn:**
   - Följande operatorer stöds: `==` `!=`, .

1. Skicka pipelinen.

   Uttrycket delar upp datauppsättningen i två rader: rader med värden som uppfyller villkoret och alla återstående rader.

Följande exempel visar hur du delar upp en datauppsättning med alternativet **Relativt uttryck** i modulen **Dela data.**  

### <a name="calendar-year"></a>Kalenderår

Ett vanligt scenario är att dela upp en datauppsättning med år. Följande uttryck markerar alla rader där värdena `Year` i `2010`kolumnen är större än .

```text
\"Year" > 2010
```

Datumuttrycket måste ta hänsyn till alla datumdelar som ingår i datakolumnen. Formatet på datumen i datakolumnen måste vara konsekvent. 

I en datumkolumn som `mmddyyyy`använder formatet ska uttrycket till exempel vara ungefär så här:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Kolumnindex

Följande uttryck visar hur du kan använda kolumnindexet för att markera alla rader i den första kolumnen i datauppsättningen som innehåller värden som är mindre än eller lika med 30, men inte lika med 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
