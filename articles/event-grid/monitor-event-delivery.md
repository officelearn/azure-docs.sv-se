---
title: Övervaka Azure Event Grid meddelandeleverans
description: Beskriver hur du övervakar leverans av Azure Event Grid-meddelanden.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824175"
---
# <a name="monitor-event-grid-message-delivery"></a>Övervaka Event Grid meddelandeleverans 

Den här artikeln beskriver hur du använder portalen för att se status för händelsen leveranser.

Event Grid förser varaktiga. Du får varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till registrerade webhooken till varje prenumeration. Om en webhook inte bekräfta mottagandet av en händelse inom 60 sekunder det första försökets leverans, försöker Event Grid leverans av händelsen.

Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Leverans mått

Portalen visar mått för status för att leverera händelsemeddelanden.

Måtten är för ämnen:

* **Publiceringen är klar**: Händelsen har skickats till ämnet och bearbetas med ett 2xx svar.
* **Publiceringen misslyckades**: Händelse skickas till ämnet, men avvisas med en felkod.
* **Oöverträffad**: Händelsen publicerats har till ämnet, men matchas inte mot en händelseprenumeration. Händelsen har tagits bort.

För prenumerationer finns följande mått:

* **Leveransen är klar**: Händelsen har levereras till prenumerationens slutpunkt och fått svar 2xx.
* **Överföringen misslyckades**: Händelse skickas till prenumerationens slutpunkt, men tog emot en 4xx eller 5xx-svar.
* **Har upphört att gälla händelser**: Händelsen inte har levererats och alla omförsök har skickats. Händelsen har tagits bort.
* **Matchade händelser**: Händelsen i avsnittet var matchas av händelseprenumerationen.

## <a name="event-subscription-status"></a>Prenumerationsstatus för händelse

Om du vill visa mått för en händelseprenumeration, kan du antingen söka efter prenumerationstyp eller prenumerationer för en viss resurs.

Om du vill söka efter händelsetyp för prenumeration, Välj **alla tjänster**.

![Välj alla tjänster](./media/monitor-event-delivery/all-services.png)

Sök efter **händelserutnät** och välj **Event Grid-prenumerationer** bland de tillgängliga alternativen.

![Sök efter händelseprenumerationer](./media/monitor-event-delivery/search-and-select.png)

Filtrera efter typ av händelse, prenumeration och plats. Välj **mått** för prenumeration för att visa.

![Filtrera prenumerationer på händelser](./media/monitor-event-delivery/filter-events.png)

Visa mått för event ämne och prenumeration.

![Visa händelse mått](./media/monitor-event-delivery/subscription-metrics.png)

För att hitta mått för en specifik resurs, markerar du den resursen. Välj **händelser**.

![Välj händelser för en resurs](./media/monitor-event-delivery/select-events.png)

Du kan se mått för prenumerationer för den resursen.

## <a name="custom-event-status"></a>Status för anpassad händelse

Om du har publicerat ett anpassat ämne, kan du visa måtten för den. Välj resursgrupp för ämnet och välj ämnet.

![Välja anpassat ämne](./media/monitor-event-delivery/select-custom-topic.png)

Visa mått för anpassad händelse ämnet.

![Visa händelse mått](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
