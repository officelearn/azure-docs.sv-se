---
title: 'Dela Data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen dela Data i Azure Machine Learning-tjänsten för att dela upp en datauppsättning i två olika uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029407"
---
# <a name="split-data-module"></a>Dela data

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att dela upp en datauppsättning i två olika uppsättningar.

Den här modulen är särskilt användbart när du behöver att avgränsa data i utbildning och testningsuppsättningar. Du kan anpassa hur data delas även. Vissa alternativ stöder slumpmässig för data. andra är skräddarsydda för en viss datatyp av eller modelltypen.

## <a name="how-to-configure"></a>Så här konfigurerar du

> [!TIP]
> Du behöver följande innan du väljer delande läge, läsa alla alternativ för att bestämma vilken typ av delning.
> Om du ändrar läget som delar upp gick att återställa alla andra alternativ.

1. Lägg till den **dela Data** modulen i experimentet i gränssnittet. Du hittar den här modulen under **Dataomvandling**i den **exemplet och dela** kategori.

2. **Dela upp läge**: Välj något av följande lägen, beroende på vilken typ av data som du har och hur du vill dela upp den. Det finns olika alternativ för varje delande läge. Klicka på följande avsnitt för detaljerade anvisningar och exempel. 

    - **Dela upp rader**: Använd det här alternativet om du bara vill dela upp data i två delar. Du kan ange hur stor procentandel av data ska placeras i varje delning, men som standard data delas 50 – 50.

        Du kan också slumpgenerera valet av rader i varje grupp och använda stratified sampling. Du måste välja en kolumn med data som du vill använda värden som ska fördelas jämnt mellan de två resultat datauppsättningarna i stratified sampling.  

    - **Reguljära uttryck dela** välja det här alternativet när du vill dela upp datauppsättningen genom att testa en enda kolumn för ett värde.

        Till exempel om du analyserar sentiment, kan du kontrollera om finns ett visst Produktnamn i ett textfält och sedan dela upp datauppsättningen i rader med produktnamnet som mål och de som saknar.

    - **Relativa uttryck dela**:  Använd det här alternativet när du vill tillämpa ett villkor till en kolumn med tal. Kan vara ett datum/tid-fält, en kolumn som innehåller ålder eller kronor eller även en procentandel. Du kanske exempelvis vill dela upp datauppsättningen beroende på kostnaden för objekt, gruppera personer efter åldersintervall eller separat data efter ett kalenderdatum.

### <a name="split-rows"></a>Dela upp rader
1.  Lägg till den [dela Data](./split-data.md) modulen i experimentet i gränssnittet, och anslut den datauppsättning som du vill dela.
  
2.  För **dela läge**, Välj **dela rader**. 

3.  **Del av rader i den första utdatauppsättningen**. Använd det här alternativet för att avgöra hur många rader som går in i första (vänster) utdata. Alla andra rader skickas till den andra (till högra) utdatan.

    Förhållandet mellan representerar procentandelen av rader som skickas till den första utdatauppsättningen så att du måste ange ett värde mellan 0 och 1.
     
     Om du skriver 0,75 som värde skulle datamängden delas med hjälp av ett 75:25-förhållande med 75% av de rader som skickas till den första utdatauppsättningen och 25% som skickas till andra datauppsättningen för utdata.
  
4. Välj den **Randomized dela** om du vill att slumpgenerera valet av data i två grupper. Det här är alternativet som rekommenderas när du skapar datauppsättningar för träning och testning.

5.  **Slumpmässig dirigering**: Ange ett värde för icke-negativt heltal för att initiera pseudoslumpvisa serie instanser som ska användas. Det här standardvärdet för dirigering används i alla moduler som genererar slumptal. 

     Ange ett startvärde blir resultatet Allmänt reproducerbar. Om du vill upprepa resultatet av en split-åtgärden bör du ange ett startvärde för slumpmässiga talgeneratorns startvärden. Annars är slumpmässig dirigering som standard till 0, vilket innebär att inledande seed-värdet hämtas från systemklockan. Därför kan kan fördelningen av data vara något annorlunda varje gång som du utför en delning. 

6. **Stratified dela**: Välja alternativet **SANT** att säkerställa att de två utdata datauppsättningarna innehåller ett representativt urval av värdena i den *strata kolumnen* eller *stratifiering nyckelkolumn*. 

    Data delas med stratified sampling så att varje utdatauppsättningen hämtar ungefär samma procentvärdet för varje mål. Du kanske exempelvis vill vara säker på att din utbildning och testningsuppsättningar är ungefär balanserade avseende resultatet eller med hänsyn ot vissa andra kolumn, till exempel kön.

7. Kör experimentet.


## <a name="regular-expression-split"></a>Reguljärt uttryck dela

1.  Lägg till den [dela Data](./split-data.md) modul till ditt experiment, och koppla den som indata till den datauppsättning som du vill dela.  
  
2.  För **dela läge**väljer **reguljärt uttryck dela**.

3. I den **reguljärt uttryck** skriver du ett giltigt reguljärt uttryck. 
  
   Det reguljära uttrycket bör följa Python syntax för reguljära uttryck.


4. Kör experimentet.

    Utifrån det reguljära uttrycket som du anger datauppsättningen är uppdelad i två uppsättningar med rader: rader med värden som matchar uttrycket och alla återstående rader. 

## <a name="relative-expression-split"></a>Relativa uttryck dela.

1. Lägg till den [dela Data](./split-data.md) modul till ditt experiment, och koppla den som indata till den datauppsättning som du vill dela.
  
2. För **dela läge**väljer **relativa uttryck dela**.
  
3. I den **relationella uttrycket** anger du ett uttryck som utför en jämförelse, på en enda kolumn:


 - Numerisk kolumn:
    - Kolumnen innehåller många olika numeriska datatyper, inklusive datum/tid-datatyper.

    - Uttrycket kan referera till högst ett kolumnnamn.

    - Använd et-tecken (&) för AND-åtgärd och Använd vertikalstrecket (|) för OR-åtgärden.

    - Följande operatorer som stöds: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Du kan gruppera åtgärder med hjälp av `(` och `)`.

 - Strängkolumn: 
    - Följande operatorer som stöds: `==`, `!=`



4. Kör experimentet.

    Uttrycket som delar upp datauppsättningen i två uppsättningar med rader: rader med värden som uppfyller villkoret och alla återstående rader.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 