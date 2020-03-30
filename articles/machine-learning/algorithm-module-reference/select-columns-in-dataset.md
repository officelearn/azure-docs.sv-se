---
title: 'Välj kolumner i datauppsättning: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Välj kolumner i datauppsättning i Azure Machine Learning för att välja en delmängd kolumner som ska användas i nedströmsåtgärder.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153782"
---
# <a name="select-columns-in-dataset-module"></a>Välj kolumner i datauppsättningsmodulen

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen om du vill välja en delmängd kolumner som ska användas i nedströmsåtgärder. Modulen tar inte bort kolumnerna fysiskt från källdatauppsättningen. I stället skapas en delmängd av kolumner, ungefär som en *databasvy* eller *projektion*.

Den här modulen är användbar när du behöver begränsa de kolumner som är tillgängliga för en nedströmsåtgärd, eller om du vill minska storleken på datauppsättningen genom att ta bort onödiga kolumner.

Kolumnerna i datauppsättningen matas ut i samma ordning som i de ursprungliga data, även om du anger dem i en annan ordning.

## <a name="how-to-use"></a>Hur du ska använda detta

Den här modulen har inga parametrar. Du använder kolumnväljaren för att välja vilka kolumner som ska inkluderas eller uteslutas.

### <a name="choose-columns-by-name"></a>Välj kolumner efter namn

Det finns flera alternativ i modulen för att välja kolumner efter namn: 

+ Filtrera och söka

    Klicka på alternativet **EFTER NAMN.**

    Om du har anslutit en datauppsättning som redan är ifylld ska en lista med tillgängliga kolumner visas. Om inga kolumner visas kan du behöva köra uppströmsmoduler för att visa kolumnlistan.

    Om du vill filtrera listan skriver du i sökrutan. Om du till exempel `w` skriver bokstaven i sökrutan filtreras listan så att `w`kolumnnamnen som innehåller bokstaven visas .

    Markera kolumner och klicka på högerpilsknappen om du vill flytta de markerade kolumnerna till listan i det högra fönstret.

    + Om du vill markera ett kontinuerligt intervall med kolumnnamn trycker du på **Skift + Klicka**.
    + Om du vill lägga till enskilda kolumner i markeringen trycker du på **Ctrl + Klicka**.

    Klicka på bockknappen för att spara och stänga.

+ Använda namn i kombination med andra regler

    Klicka på alternativet **MED REGLER.**
    
    Välj en regel, till exempel att visa kolumner av en viss datatyp.

    Klicka sedan på enskilda kolumner av den typen efter namn för att lägga till dem i urvalslistan.

+ Skriva eller klistra in en kommaavgränsad lista med kolumnnamn

    Om datauppsättningen är bred kan det vara enklare att använda index eller genererade namnlistor i stället för att välja kolumner individuellt. Förutsatt att du har förberett listan i förväg:

    1. Klicka på alternativet **MED REGLER.** 
    2. Markera **Inga kolumner**, välj **Inkludera**och klicka sedan i textrutan med det röda utropstecknet. 
    3. Klistra in eller skriv en kommaavgränsad lista med tidigare validerade kolumnnamn. Du kan inte spara modulen om någon kolumn har ett ogiltigt namn, så se till att kontrollera namnen i förväg.
    
    Du kan också använda den här metoden för att ange en lista med kolumner med hjälp av deras indexvärden. 

### <a name="choose-by-type"></a>Välj efter typ

Om du använder alternativet **MED REGLER** kan du använda flera villkor i kolumnvalen. Du kan till exempel behöva bara hämta funktionskolumner av en numerisk datatyp.

Alternativet **BÖRJA MED** avgör din utgångspunkt och är viktigt för att förstå resultaten. 

+ Om du väljer alternativet **ALLA KOLUMNER** läggs alla kolumner till i listan. Sedan måste du använda alternativet **Uteslut** för att *ta bort* kolumner som uppfyller vissa villkor. 

    Du kan till exempel börja med alla kolumner och sedan ta bort kolumner efter namn eller efter typ.

+ Om du väljer alternativet **INGA KOLUMNER** börjar listan med kolumner tomma. Du anger sedan villkor för att *lägga* till kolumner i listan. 

    Om du tillämpar flera regler är varje villkor **additivt**. Anta till exempel att du börjar utan kolumner och sedan lägger till en regel för att hämta alla numeriska kolumner. I bilprisdatauppsättningen resulterar det i 16 kolumner. Sedan klickar du **+** på tecknet för att lägga till ett nytt villkor och väljer **Inkludera alla funktioner**. Den resulterande datauppsättningen innehåller alla numeriska kolumner, plus alla funktionskolumner, inklusive några strängfunktionskolumner.

### <a name="choose-by-column-index"></a>Välj efter kolumnindex

Kolumnindexet refererar till kolumnens ordning i den ursprungliga datauppsättningen.

+ Kolumnerna numreras sekventiellt från 1.  
+ Om du vill hämta en rad kolumner använder du ett bindestreck. 
+ Öppna specifikationer som `1-` eller `-3` är inte tillåtna.
+ Dubblettindexvärden (eller kolumnnamn) är inte tillåtna och kan resultera i ett fel.

Om du till exempel antar att datauppsättningen har minst åtta kolumner kan du klistra in något av följande exempel för att returnera flera icke-sammanhängande kolumner: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Det slutliga exemplet resulterar inte i ett fel. Den returnerar dock en `4`enda instans av kolumnen .



### <a name="change-order-of-columns"></a>Ändra ordning på kolumner

Alternativet **Tillåt dubbletter och bevara kolumnordning i markeringen** börjar med en tom lista och lägger till kolumner som du anger efter namn eller index. Till skillnad från andra alternativ, som alltid returnerar kolumner i sin "naturliga ordning", matar det här alternativet ut kolumnerna i den ordning du namnger eller listar dem. 

I en datauppsättning med kolumnerna Col1, Col2, Col3 och Col4 kan du till exempel ändra ordningen på kolumnerna och utelämna kolumn 2 genom att ange någon av följande listor:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 