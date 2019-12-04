---
title: Lägg till åtgärden fråga i Logic Apps
description: Översikt över frågans åtgärd för att utföra åtgärder som filter mat ris
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/20/2016
tags: connectors
ms.openlocfilehash: 2e5c9d371a280b776699b2e10d3e8e94b5f41f6f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787140"
---
# <a name="get-started-with-the-query-action"></a>Kom igång med frågans åtgärd

Med hjälp av åtgärden fråga kan du arbeta med batchar och matriser för att utföra arbets flöden för:

* Skapa en uppgift för alla hög prioritets poster från en databas.
* Spara alla PDF-bilagor för e-postmeddelanden i en Azure-blob.

För att komma igång med att använda fråge åtgärden i en Logic app, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-query-action"></a>Använd åtgärden fråga

En åtgärd är en åtgärd som utförs av det arbets flöde som definieras i en Logic app. 
[Läs mer om åtgärder](../connectors/apis-list.md).  

Frågans åtgärd har för närvarande en åtgärd, som kallas för den filter mat ris, som visas i designern. På så sätt kan du fråga en matris och returnera en uppsättning filtrerade resultat.

Så här kan du lägga till det i en Logic app:

1. Välj knappen **nytt steg** .
2. Välj **Lägg till en åtgärd**.
3. I sökrutan åtgärd skriver du **filter** för att visa en lista över **filter mat ris** åtgärden.
   
    ![Välj åtgärden fråga](./media/connectors-native-query/using-action-1.png)
4. Välj en matris att filtrera. (Följande skärm bild visar en matris med resultat från en Twitter-sökning.)
5. Skapa ett villkor för att utvärdera varje objekt. (Följande skärm bild filtrerar tweets från användare som har fler än 100 följare.)
   
    ![Slutför åtgärden fråga](./media/connectors-native-query/using-action-2.png)
   
    Åtgärden skapar en ny matris som bara innehåller resultat som uppfyller filter kraven.
6. Klicka på det övre vänstra hörnet i verktygsfältet för att spara och din Logi Kap par kommer både att spara och publicera (aktivera).

\* om du anropar en HTTP-slutpunkt och tar emot ett JSON-svar använder du åtgärden _parsa JSON_ för att PARSa JSON-svaret. Utan att vidta det här steget kommer _filter mat ris_ bara att se brödtext och inte förstå JSON-nyttolastens struktur.

## <a name="query-action"></a>Fråga åtgärd
Här följer information om den åtgärd som stöds av den här anslutningen. Anslutningen har en möjlig åtgärd.

| Åtgärd | Beskrivning |
| --- | --- |
| Filtrera matris |Utvärderar ett villkor för varje objekt i en matris och returnerar resultaten |

## <a name="action-details"></a>Åtgärds information
Frågan åtgärdas med en möjlig åtgärd. I följande tabeller beskrivs de obligatoriska och valfria inmatnings fälten för åtgärden och motsvarande information om utdata som är associerade med att använda åtgärden.

### <a name="filter-array"></a>Filtrera matris
Följande är ingångs fält för åtgärden som gör en HTTP-utgående begäran.
A * betyder att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Som |som |Matrisen som ska filtreras |
| Moduletype |vilken |Villkoret att utvärdera för varje objekt |

<br>

### <a name="output-details"></a>Information om utdata
Följande är information om utdata för HTTP-svaret.

| Egenskapsnamn | Datatyp | Beskrivning |
| --- | --- | --- |
| Filtrerad matris |matris |En matris som innehåller ett objekt för varje filtrerat resultat |

## <a name="next-steps"></a>Nästa steg
Prova nu plattformen och [skapa en Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska de andra tillgängliga anslutningarna i Logic Apps genom att titta på vår [API-lista](apis-list.md).

