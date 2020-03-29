---
title: Övervaka leverans av Azure Event Grid-meddelande
description: I den här artikeln beskrivs hur du använder Azure-portalen för att se status för leveransen av Azure Event Grid-meddelanden.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722143"
---
# <a name="monitor-event-grid-message-delivery"></a>Leverans av meddelande från monitorn för händelserutnät 

I den här artikeln beskrivs hur du använder portalen för att se status för händelseleveranser.

Event Grid ger hållbar leverans. Det ger varje meddelande minst en gång för varje prenumeration. Händelser skickas till den registrerade webhooken för varje prenumeration omedelbart. Om en webhook inte bekräftar mottagandet av en händelse inom 60 sekunder efter det första leveransförsöket, försöker Event Grid leverera händelsen igen.

För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Leveransmått

Portalen visar mått för statusen för att leverera händelsemeddelanden.

För ämnen är måtten:

* **Publicera lyckades:** Händelsen har skickats till ämnet och bearbetats med ett 2xx-svar.
* **Publicering misslyckades**: Händelsen skickades till ämnet men avvisades med en felkod.
* **Omatchade**: Händelsen har publicerats i ämnet, men inte matchat med en händelseprenumeration. Händelsen togs bort.

För prenumerationer är måtten:

* **Leveransen lyckades:** Händelsen har levererats till prenumerationens slutpunkt och fick ett 2xx-svar.
* **Leverans misslyckades:** Händelsen skickades till prenumerationens slutpunkt, men fick ett 4xx- eller 5xx-svar.
* **Utgångna händelser:** Händelsen levererades inte och alla försök till återförsök skickades. Händelsen togs bort.
* **Matchade händelser:** Händelsen i avsnittet matchades av händelseprenumerationen.

## <a name="event-subscription-status"></a>Status för händelseprenumeration

Om du vill visa mått för en händelseprenumeration kan du antingen söka efter prenumerationstyp eller efter prenumerationer för en viss resurs.

Om du vill söka efter händelseprenumerationstyp väljer du **Alla tjänster**.

![Välj alla tjänster](./media/monitor-event-delivery/all-services.png)

Sök efter **händelserutnät** och välj Prenumerationer på **eventrutnät** bland de tillgängliga alternativen.

![Sök efter evenemangsprenumerationer](./media/monitor-event-delivery/search-and-select.png)

Filtrera efter typ av händelse, prenumeration och plats. Välj **Mått** för prenumerationen som ska visas.

![Filtrera händelseprenumerationer](./media/monitor-event-delivery/filter-events.png)

Visa måtten för händelseämnet och prenumerationen.

![Visa händelsemått](./media/monitor-event-delivery/subscription-metrics.png)

Om du vill hitta måtten för en viss resurs väljer du den resursen. Välj sedan **Händelser**.

![Välj händelser för en resurs](./media/monitor-event-delivery/select-events.png)

Du ser måtten för prenumerationer för den resursen.

## <a name="custom-event-status"></a>Anpassad händelsestatus

Om du har publicerat ett anpassat ämne kan du visa måtten för det. Markera resursgruppen för ämnet och välj avsnittet.

![Välja anpassat ämne](./media/monitor-event-delivery/select-custom-topic.png)

Visa måtten för det anpassade händelseavsnittet.

![Visa händelsemått](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Ange aviseringar

Du kan ange aviseringar om ämnes- och domännivåmått för anpassade ämnen och händelsedomäner. I översiktsbladet för väljer du **Aviseringar** från vänster resursmenyn för att visa, hantera och skapa varningsregler. [Läs mer om Azure Monitor-aviseringar](../azure-monitor/platform/alerts-overview.md)

![Visa händelsemått](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Nästa steg

* För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
