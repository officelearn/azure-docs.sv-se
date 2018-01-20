---
title: "Lägg till instruktionen frågan i logikappar | Microsoft Docs"
description: "Översikt över frågeåtgärden för att utföra åtgärder som filter matris."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-query-action"></a>Kom igång med frågeåtgärden
Genom att använda åtgärden fråga kan arbeta du med batchar och matriser för att utföra arbetsflöden för att:

* Skapa en aktivitet för alla viktiga poster från en databas.
* Spara alla bifogade PDF-filer för e-postmeddelanden till en Azure blob.

Om du vill komma igång med åtgärden fråga i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Använd åtgärden fråga
En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. [Mer information om åtgärder](connectors-overview.md).  

Åtgärden fråga har för närvarande en åtgärd som kallas filter-matrisen som visas i designern. På så sätt kan du fråga en matris och returnerar en uppsättning filtrerade resultat.

Här är hur du lägger till den i en logikapp:

1. Välj den **nytt steg** knappen.
2. Välj **lägga till en åtgärd**.
3. Skriv i sökrutan åtgärd **filter** listan den **Filter matris** åtgärd.
   
    ![Välj frågeåtgärden som](./media/connectors-native-query/using-action-1.png)
4. Välj en matris för att filtrera. (Följande skärmbild visar matris av resultat från en Twitter-sökning.)
5. Skapa ett villkor som ska utvärderas på varje objekt. (Följande skärmbild filtrerar tweets från användare som har fler än 100 blandare.)
   
    ![Utför åtgärden fråga](./media/connectors-native-query/using-action-2.png)
   
    Åtgärden kommer att skrivas ut en ny matris som innehåller endast resultat som uppfyller kraven för filtret.
6. Klicka på det övre vänstra hörnet i verktygsfältet för att spara och logikappen både sparar och publicera (aktivera).

\*Om du en HTTP-slutpunkt anropas och ta emot en JSON-svar kan använda den _parsa JSON_ åtgärd att parsa JSON-svar. Utan att det här steget _Filter matris_ kommer endast inte förstå och strukturen för JSON-nyttolast.

## <a name="query-action"></a>Frågeåtgärden
Här följer information om vad som har stöd för den här anslutningen. Kopplingen har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| Filtermatris |Utvärderar ett villkor för varje objekt i en matris och returnerar resultaten |

## <a name="action-details"></a>Åtgärdsinformation
Åtgärden fråga innehåller en möjlig åtgärd. I följande tabeller beskrivs de obligatoriska och valfria indatafält för åtgärden och detaljer om motsvarande utdata som är associerade med hjälp av åtgärden.

### <a name="filter-array"></a>Filtermatris
Följande är inmatningsfält för åtgärden, vilket gör en utgående HTTP-begäran.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Från * |från |Matrisen för att filtrera |
| Villkoret * |där |Villkoret ska utvärderas för varje objekt |

<br>

### <a name="output-details"></a>Utdatainformation
Nedan visas utdata information för HTTP-svaret.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Filtrerade matris |matris |En matris som innehåller ett objekt för varje filtrerade resultat |

## <a name="next-steps"></a>Nästa steg
Prova nu, plattform och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga kopplingar i logikappar genom att titta på vår [API: er listan](apis-list.md).

