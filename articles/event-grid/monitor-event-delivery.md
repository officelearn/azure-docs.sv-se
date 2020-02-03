---
title: Övervaka Azure Event Grid meddelande leverans
description: I den här artikeln beskrivs hur du använder Azure Portal för att se statusen för leverans av Azure Event Grid meddelanden.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722143"
---
# <a name="monitor-event-grid-message-delivery"></a>Övervaka Event Grid meddelande leverans 

I den här artikeln beskrivs hur du använder portalen för att se statusen för händelse leveranser.

Event Grid tillhandahåller varaktig leverans. Den levererar varje meddelande minst en gång för varje prenumeration. Händelser skickas till den registrerade webhooken för varje prenumeration direkt. Om en webhook inte bekräftar mottagandet av en händelse inom 60 sekunder från det första leverans försöket, Event Grid förnyar leveransen av händelsen.

Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Leverans mått

Portalen visar mått för statusen för att leverera händelse meddelanden.

För ämnen är måtten:

* **Publiceringen lyckades**: händelsen har skickats till ämnet och bearbetats med ett 2xx-svar.
* **Publiceringen misslyckades**: händelsen skickades till ämnet men avvisades med en felkod.
* **Omatchad**: händelsen har publicerats till ämnet, men inte matchad med en händelse prenumeration. Händelsen släpptes.

För prenumerationer är måtten:

* **Leveransen lyckades**: händelsen har levererats till prenumerationens slut punkt och ett 2xx-svar togs emot.
* **Leveransen misslyckades**: händelsen som skickades till prenumerationens slut punkt, men tog emot ett 4xx-eller 5XX-svar.
* **Utgångna händelser**: händelsen levererades inte och alla nya återförsök har skickats. Händelsen släpptes.
* **Matchade händelser**: händelsen i ämnet matchades av händelse prenumerationen.

## <a name="event-subscription-status"></a>Status för händelse prenumeration

Om du vill se mått för en händelse prenumeration kan du antingen söka efter prenumerations typ eller prenumerationer för en speciell resurs.

Om du vill söka efter händelse prenumerations typ väljer du **alla tjänster**.

![Välj alla tjänster](./media/monitor-event-delivery/all-services.png)

Sök efter **Event Grid** och välj **Event Grid prenumerationer** från de tillgängliga alternativen.

![Sök efter händelse prenumerationer](./media/monitor-event-delivery/search-and-select.png)

Filtrera efter typ av händelse, prenumeration och plats. Välj **mått** för den prenumeration som du vill visa.

![Filtrera händelse prenumerationer](./media/monitor-event-delivery/filter-events.png)

Visa måtten för händelse ämnet och prenumerationen.

![Visa händelse mått](./media/monitor-event-delivery/subscription-metrics.png)

Om du vill hitta måtten för en speciell resurs väljer du den resursen. Välj sedan **händelser**.

![Välj händelser för en resurs](./media/monitor-event-delivery/select-events.png)

Du ser måtten för prenumerationer för resursen.

## <a name="custom-event-status"></a>Status för anpassad händelse

Om du har publicerat ett anpassat ämne kan du visa måtten för det. Välj resurs grupp för ämnet och välj ämnet.

![Välja anpassat ämne](./media/monitor-event-delivery/select-custom-topic.png)

Visa måtten för avsnittet anpassad händelse.

![Visa händelse mått](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Ange aviseringar

Du kan ställa in aviseringar för ämnes-och domän nivå mått för anpassade ämnen och händelse domäner. I översikts bladet för väljer du **aviseringar** från den vänstra resurs menyn för att visa, hantera och skapa aviserings regler. [Läs mer om Azure Monitor aviseringar](../azure-monitor/platform/alerts-overview.md)

![Visa händelse mått](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
