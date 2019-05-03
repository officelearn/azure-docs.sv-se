---
title: 'Välj kolumner i datauppsättning: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Välj kolumner i datauppsättning modulen i Azure Machine Learning-tjänsten för att välja en delmängd med kolumner som ska användas i nedströms operationer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028102"
---
# <a name="select-columns-in-dataset-module"></a>Välj kolumner i datauppsättning modulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att välja en delmängd med kolumner som ska användas i nedströms operationer. Modulen tas inte fysiskt bort kolumner från källdatauppsättningen; i stället skapar den en delmängd med kolumner, ungefär som en databas *visa* eller *projektion*.

Den här modulen är användbart när du vill begränsa kolumnerna som är tillgängliga för en underordnad åtgärd, eller om du vill minska storleken på datauppsättningen genom att ta bort onödiga kolumner.

Kolumner i datauppsättningen är utdata i samma ordning som i den ursprungliga informationen, även om du anger dem i en annan ordning.

## <a name="how-to-use"></a>Hur du ska använda detta

Den här modulen har inga parametrar. Du kan använda kolumnväljaren för att välja kolumnerna som ska tas med eller undantas.

### <a name="choose-columns-by-name"></a>Välj kolumner efter namn

Det finns flera alternativ i modulen för att välja kolumner efter namn: 

+ Filter och sökning

    Klicka på den **efter namn** alternativet.

    Om du har anslutit en datauppsättning som redan är ifyllda, visas en lista över tillgängliga kolumner. Om inga kolumner visas kan behöva du köra överordnade moduler om du vill visa kolumnlistan.

    Skriv i sökrutan för att filtrera listan. Exempel: Om du skriver in bokstaven `w` i sökrutan listan filtreras för att visa namn på kolumnerna som innehåller bokstaven `w`.

    Välj kolumner och klicka på högerpilen för att flytta de markerade kolumnerna i listan i den högra rutan.

    + För att välja ett intervall av kolumnnamn, trycker du på **Skift + klicka**.
    + Om du vill lägga till enskilda kolumner i markeringen, trycker du på **Ctrl + klicka**.

    Klicka på bockmarkeringen för att spara och Stäng.

+ Genom att använda namnen i kombination med andra regler

    Klicka på den **med regler** alternativet.
    
    Välj en regel, till exempel som visar kolumner i en viss datatyp.

    Klicka på enskilda kolumner av den typen av namn, lägga till dem i urvalslistan.

+ Skriv eller klistra in en kommaavgränsad lista med kolumnnamn

    Om din datauppsättning är bred kan vara enklare att använda index eller genereras en lista över namn i stället för att välja kolumner individuellt. Om vi antar att du har förberett listan i förväg:

    1. Klicka på den **med regler** alternativet. 
    2. Välj **inga kolumner**väljer **inkludera**, och klickar sedan i textrutan med rött utropstecken. 
    3. Klistra in eller ange en kommaavgränsad lista över tidigare verifierade kolumnnamn. Du kan inte spara modulen om någon kolumn har ett ogiltigt namn, så måste du kontrollera namnen i förväg.
    
    Du kan också använda den här metoden för att ange en lista med kolumner med hjälp av deras indexvärden. 

### <a name="choose-by-type"></a>Välj efter typ

Om du använder den **med regler** alternativ, kan du använda flera villkor på kolumnen val. Du kan behöva hämta bara funktionen kolumner i en numerisk datatyp.

Den **börjar med** alternativet avgör din startpunkt och är viktiga för att förstå resultatet. 

+ Om du väljer den **alla kolumner** alternativet, alla kolumner läggs till i listan. Sedan måste du använda den **undanta** alternativet att *ta bort* kolumner som uppfyller vissa villkor. 

    Du kan till exempel börja med alla kolumner och ta bort kolumner efter namn eller efter typ.

+ Om du väljer den **nr kolumner** alternativet listan över kolumner startar tom. Sedan kan du ange villkor för *lägga till* kolumner i listan. 

    Om du tillämpar flera regler varje villkor är **additiva**. Anta exempelvis att du börjar med inga kolumner och Lägg sedan till en regel för att hämta alla numeriska kolumner. I Automobile price datauppsättningen som resulterar i 16 kolumner. Sedan klickar du på den **+** logga att lägga till ett nytt villkor och välj **innehåller alla funktioner**. Den resulterande datauppsättningen innehåller de numeriska kolumnerna samt alla funktionen kolumner, inklusive vissa kolumner för sträng-funktionen.

### <a name="choose-by-column-index"></a>Välj genom kolumnindex

Kolumnindex syftar på uppdrag kolumnen i den ursprungliga datauppsättningen.

+ Kolumner är numrerade sekventiellt börjar på 1.  
+ Hämta ett antal kolumner med ett bindestreck. 
+ Öppna specifikationer som `1-` eller `-3` tillåts inte.
+ Duplicerade indexet värden (eller kolumnnamn) tillåts inte och kan resultera i ett fel.

Till exempel förutsatt att din datauppsättning har minst åtta kolumner, du kan klistra in i någon av följande exempel för att returnera flera icke-sammanhängande kolumner: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Det sista exemplet resulterar inte i ett fel. men den returnerar en enda instans av kolumnen `4`.



### <a name="change-order-of-columns"></a>Ändra ordningen på kolumnerna

Alternativet **tillåter dubbletter och bevara kolumnordning i val av** börjar med en tom lista och lägger till kolumner som du anger efter namn eller index. Till skillnad från andra alternativ, som alltid returnera kolumner i sina ”naturlig order”, det här alternativet visar kolumnerna i ordningen som du namnger eller lista dem. 

I en datauppsättning med kolumner Kol1, Col2, Col3 och Col4, kan du till exempel vända på ordningen på kolumnerna och utelämna kolumn 2, genom att ange något av följande listor:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 