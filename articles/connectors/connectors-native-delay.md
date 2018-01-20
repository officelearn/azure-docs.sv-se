---
title: "Lägg till en fördröjning i logikappar | Microsoft Docs"
description: "Översikt över fördröjning och fördröjning-tills åtgärder och hur du använder dem med en Azure logikapp."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 6cde5b8ba8d770a07199816286b666e952394de1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Kom igång med fördröjning och fördröjning-tills åtgärder
Med hjälp av fördröjningen och ”fördröjning-tills” åtgärder, kan du slutföra arbetsflödet scenarier.

Du kan till exempel:

* Vänta tills en veckodag att skicka en statusuppdatering via e-post.
* Fördröjer arbetsflödet tills ett HTTP-anrop har tid att slutföra innan du fortsätter och hämta resultatet.

Om du vill komma igång med åtgärden fördröjning i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Använd åtgärderna fördröjning
En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. [Mer information om åtgärder](connectors-overview.md).

Här är en Exempelsekvens av hur du använder en fördröjning steg i en logikapp:

1. När du lägger till en utlösare, klickar du på **nytt steg** att lägga till en åtgärd.
2. Sök efter **fördröjning** så att åtgärderna som fördröjning. I det här exemplet väljer vi **fördröjning**.
   
    ![Fördröjning åtgärder](./media/connectors-native-delay/using-action-1.png)
3. Slutför eventuella egenskaper för åtgärden för att konfigurera fördröjningen.
   
    ![Fördröjning config](./media/connectors-native-delay/using-action-2.png)
4. Klicka på **spara** att publicera och aktivera logikappen.

## <a name="action-details"></a>Åtgärdsinformation
Återkommande utlösaren har följande egenskaper som kan konfigureras.

### <a name="delay-action"></a>Fördröjning åtgärd
Den här åtgärden fördröjningar kör för ett visst tidsintervall.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Antal * |antal |Antalet tidsenheter fördröjning |
| A * |enhet |Tidsenhet: `Second`, `Minute`, `Hour`, eller`Day` |

<br>

### <a name="delay-until-action"></a>Fördröjning-förrän åtgärden
Den här åtgärden fördröjningar körs tills angivet datum/tid.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| År * |tidsstämpel |År till fördröja till (GMT) |
| Månad * |tidsstämpel |Månad för att fördröja till (GMT) |
| Dag * |tidsstämpel |Dag för att fördröja till (GMT) |

<br>

## <a name="next-steps"></a>Nästa steg
Prova nu, plattform och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga kopplingar i logikappar genom att titta på vår [API: er listan](apis-list.md).

