---
title: 'Dela data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen dela data i Azure Machine Learning-tjänsten för att dela upp en data uppsättning i två distinkta mängder.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 31612e10e7978e94f1ed467b5ffbecde40910ef9
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128469"
---
# <a name="split-data-module"></a>Modulen dela data

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att dela upp en data uppsättning i två distinkta mängder.

Den här modulen är särskilt användbar när du behöver separera data i utbildning och testnings uppsättningar. Du kan anpassa hur data delas upp. Vissa alternativ stöder slumpmässig data, andra skräddarsys för en viss datatyp eller modell typ.

## <a name="how-to-configure"></a>Så här konfigurerar du

> [!TIP]
> Innan du väljer delnings läget kan du läsa alla alternativ för att avgöra vilken typ av delning du behöver.
> Om du ändrar delnings läget kan alla andra alternativ återställas.

1. Lägg till modulen **dela data** i ditt experiment i gränssnittet. Du hittar den här modulen under **data omvandling**, i kategorin **exempel och Split** .

2. **Delnings läge**: Välj något av följande lägen, beroende på vilken typ av data du har och hur du vill dela upp den. Varje delnings läge har olika alternativ. Klicka på följande avsnitt om du vill ha detaljerade instruktioner och exempel. 

    - **Dela rader**: Använd det här alternativet om du bara vill dela upp data i två delar. Du kan ange den procent andel av data som ska placeras i varje delning, men som standard delas data upp 50-50.

        Du kan också göra en slumpmässig markering av rader i varje grupp och använda Stratified-sampling. I Stratified-sampling måste du välja en enskild kolumn med data för vilka du vill att värden ska fördelas lika mellan de två resultat data uppsättningarna.  

    - **Delning av reguljärt uttryck**  Välj det här alternativet om du vill dela upp data uppsättningen genom att testa en enskild kolumn för ett värde.

        Om du till exempel analyserar sentiment kan du kontrol lera om det finns ett visst produkt namn i ett textfält och sedan dela upp data uppsättningen i rader med mål produktens namn och de utan.

    - **Delning av relativt uttryck**:  Använd det här alternativet när du vill tillämpa ett villkor för en tal kolumn. Talet kan vara ett datum/tid-fält, en kolumn som innehåller ålders-eller dollar belopp eller till och med en procent andel. Du kanske t. ex. vill dela upp din data uppsättning beroende på kostnaden för objekten, gruppera personer efter ålders intervall eller dela data efter kalender datum.

### <a name="split-rows"></a>Dela rader
1.  Lägg till modulen [dela data](./split-data.md) i ditt experiment i gränssnittet och Anslut den data uppsättning som du vill dela.
  
2.  För **delnings läge**väljer du **dela upp rader**. 

3.  **Bråkdel av rader i den första data uppsättningen för utdata**. Använd det här alternativet för att avgöra hur många rader som ska gå till den första (vänstra) utmatningen. Alla andra rader flyttas till den andra (högra) utmatningen.

    Förhållandet representerar procent andelen rader som har skickats till den första data uppsättningen, så du måste ange ett decimal tal mellan 0 och 1.
     
     Om du till exempel skriver 0,75 som värde, skulle data uppsättningen delas med hjälp av ett 75:25-förhållande, med 75% av raderna som skickas till den första data uppsättningen och 25% som skickas till den andra utdata-datauppsättningen.
  
4. Välj det **slumpmässiga delnings** alternativet om du vill slumpmässig data urvalet i de två grupperna. Det här är det bästa alternativet när du skapar utbildnings-och test data uppsättningar.

5.  **Slumpmässigt utsäde**: Ange ett icke-negativt heltals värde för att initiera pseudorandom-sekvensen av instanser som ska användas. Standardvärdet för start används i alla moduler som genererar slumpmässiga tal. 

     Om du anger ett startvärden blir resultatet i allmänhet reproducerat. Om du behöver upprepa resultatet av en delad åtgärd bör du ange ett start värde för slump tals generatorn. Annars anges det slumpmässiga startvärdet som standard till 0, vilket innebär att det första startvärdet hämtas från system klockan. Därför kan data fördelningen vara något annorlunda varje gången du utför en delning. 

6. **Stratified-delning**: Ange det här alternativet till **Sant** för att säkerställa att de två data uppsättningarna innehåller ett representativt exempel på värdena i *kolumnen Strata* eller *Stratification Key*. 

    Med Stratified-sampling delas data upp så att varje utdata-datauppsättning får ungefär samma procent andel av varje målvärde. Du kanske till exempel vill se till att dina utbildnings-och test uppsättningar är ungefär balanserade med avseende på resultatet eller med avseende på en annan kolumn, till exempel kön.

7. Kör experimentet.


## <a name="regular-expression-split"></a>Delning av reguljärt uttryck

1.  Lägg till modulen [dela data](./split-data.md) i experimentet och Anslut den som indata till den data uppsättning som du vill dela.  
  
2.  För **delnings läge**väljer du **delad reguljära uttryck**.

3. I rutan **reguljära uttryck** skriver du ett giltigt reguljärt uttryck. 
  
   Det reguljära uttrycket bör följa syntaxen för reguljära uttryck i python.


4. Kör experimentet.

    Baserat på det reguljära uttrycket som du anger är data uppsättningen uppdelad i två uppsättningar med rader: rader med värden som matchar uttrycket och alla återstående rader. 

## <a name="relative-expression-split"></a>Delat relativt uttryck.

1. Lägg till modulen [dela data](./split-data.md) i experimentet och Anslut den som indata till den data uppsättning som du vill dela.
  
2. För **delnings läge**väljer du **delat relativt uttryck**.
  
3. I text rutan **Relations uttryck** skriver du ett uttryck som utför en jämförelse-åtgärd i en enskild kolumn:


 - Numerisk kolumn:
    - Kolumnen innehåller siffror av vilken numerisk datatyp som helst, inklusive datum-och tids data typer.

    - Uttrycket kan referera till högst ett kolumn namn.

    - Använd et-tecknet (&) för och-åtgärden och Använd pipe-tecknet (|) för åtgärden eller.

    - Följande operatorer stöds: `<` `<=` `>`, `>=` ,`==`,,,`!=`

    - Du kan inte gruppera åtgärder med `(` hjälp `)`av och.

 - Sträng kolumn: 
    - Följande operatorer stöds: `==`,`!=`



4. Kör experimentet.

    Uttrycket delar in data uppsättningen i två uppsättningar med rader: rader med värden som uppfyller villkoret och alla återstående rader.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 