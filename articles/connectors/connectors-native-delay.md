---
title: Lägg till en fördröjning i logic apps | Microsoft Docs
description: Översikt över fördröjning och fördröjning-tills åtgärder och hur du använder dem med Azure logic app.
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 15e581454b60319ab734f2fa5faf0d90e0a7c8bf
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893732"
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Kom igång med fördröjning och fördröjning-tills åtgärder
Med hjälp av fördröjningen och ”fördröjning-tills” åtgärder, kan du slutföra arbetsflödet scenarier.

Du kan till exempel:

* Vänta tills en veckodag att skicka en statusuppdatering via e-post.
* Fördröja arbetsflödet tills ett HTTP-anrop har tid att slutföra innan du återupptar och hämta resultatet.

Kom igång med delay-åtgärden i en logikapp, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-delay-actions"></a>Använd åtgärderna fördröjning

En åtgärd är en åtgärd som utförs av arbetsflödet som definieras i en logikapp. 
[Läs mer om åtgärder](../connectors/apis-list.md).

Här är en Exempelsekvens av hur du använder en fördröjning steg i en logikapp:

1. När du lägger till en utlösare, klickar du på **nytt steg** att lägga till en åtgärd.
2. Sök efter **fördröjning** att ta fram fördröjning-åtgärder. I det här exemplet väljer vi **fördröjning**.
   
    ![Fördröjning åtgärder](./media/connectors-native-delay/using-action-1.png)
3. Slutför någon av egenskaperna för åtgärden att konfigurera fördröjning.
   
    ![Fördröjning config](./media/connectors-native-delay/using-action-2.png)
4. Klicka på **spara** att publicera och aktivera logikappen.

## <a name="action-details"></a>Åtgärdsinformation
Upprepningsutlösaren har följande egenskaper som kan konfigureras.

### <a name="delay-action"></a>Delay-åtgärden
Den här åtgärden försenar körningen för ett visst tidsintervall.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| Antal * |count |Antalet tidsenheter att fördröja |
| Enhet * |enhet |Tidsenheten: `Second`, `Minute`, `Hour`, eller `Day` |

<br>

### <a name="delay-until-action"></a>Fördröjning-förrän åtgärden
Den här åtgärden försenar körningen tills ett angivet datum/tid.
A * innebär att det är ett obligatoriskt fält.

| Visningsnamn | Egenskapsnamn | Beskrivning |
| --- | --- | --- |
| År * |tidsstämpel |Årets Fördröj tills (GMT) |
| Månad * |tidsstämpel |Månad för att fördröja tills (GMT) |
| Dag * |tidsstämpel |Dag för att fördröja tills (GMT) |

<br>

## <a name="next-steps"></a>Nästa steg
Nu kan prova att använda plattformen och [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Du kan utforska andra tillgängliga anslutningsappar i logic apps genom att titta på våra [API: er lista](apis-list.md).

