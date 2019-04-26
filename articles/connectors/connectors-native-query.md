---
title: Lägg till fråga-åtgärd i logic apps | Microsoft Docs
description: Översikt över frågeåtgärden för att utföra åtgärder som att filtrera matris.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 2a82afe396039857e5b9ad6b8a6d0e710573037f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60538251"
---
# <a name="get-started-with-the-query-action"></a>Kom igång med frågeåtgärden
Genom att använda frågeåtgärden kan arbeta du med batchar och matriser till att utföra arbetsflöden för att:

* Skapa en uppgift för alla viktiga poster från en databas.
* Spara alla bifogade PDF-filer för e-postmeddelanden till en Azure-blob.

Kom igång med frågeåtgärden i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Använd frågeåtgärden
En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. 
[Läs mer om åtgärder](../connectors/apis-list.md).  

Frågeåtgärden har för närvarande en åtgärd som kallas filtermatris som exponeras i designern. På så sätt kan du fråga en matris och returnerar en uppsättning med filtrerade resultat.

Här är hur du kan lägga till den i en logikapp:

1. Välj den **nytt steg** knappen.
2. Välj **Lägg till en åtgärd**.
3. Skriv i sökrutan åtgärd **filter** listan den **filtermatris** åtgärd.
   
    ![Välj frågeåtgärden](./media/connectors-native-query/using-action-1.png)
4. Välj en matris att filtrera. (Följande skärmbild visar ett antal resultat från en Twitter-sökning.)
5. Skapa ett villkor utvärderas på varje objekt. (Följande skärmbild filtrerar tweets från användare som har fler än 100 följare.)
   
    ![Slutför frågeåtgärden](./media/connectors-native-query/using-action-2.png)
   
    Åtgärden kommer utdata i en ny matris som innehåller endast de resultat som uppfyller kraven för filtret.
6. Klicka på det övre vänstra hörnet i verktygsfältet för att spara och din logikapp kommer både spara och publicera (aktivera).

\* Om du arbetar anropar en HTTP-slutpunkt och få ett JSON-svar kan använda den _parsa JSON_ åtgärder för att parsa JSON-svar. Utan att det här steget _Filtermatris_ kommer att visa endast brödtext och inte förstå strukturen för JSON-nyttolast.

## <a name="query-action"></a>Frågeåtgärden
Här följer information om vad som har stöd för den här anslutningen. Kopplingen har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| Filtermatris |Utvärderar ett villkor för varje objekt i en matris och returnerar resultaten |

## <a name="action-details"></a>Åtgärdsinformation
Frågeåtgärden levereras med en möjlig åtgärd. I följande tabeller beskrivs de obligatoriska och valfria inmatningsfält för åtgärden och de motsvarande utdatainformation som är associerade med hjälp av åtgärden.

### <a name="filter-array"></a>Filtermatris
Här följer inmatningsfält för åtgärden, vilket gör en utgående HTTP-begäran.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| From* |från |Matris att filtrera |
| Villkor * |där |Villkor att utvärdera för varje objekt |

<br>

### <a name="output-details"></a>Utdatainformation
Här följer utdatainformation för HTTP-svaret.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Filtrerade matris |matris |En matris som innehåller ett-objekt för varje filtrerat resultat |

## <a name="next-steps"></a>Nästa steg
Nu kan prova att använda plattformen och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga anslutningsappar i logic apps genom att titta på våra [API: er lista](apis-list.md).

