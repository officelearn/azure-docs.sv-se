---
title: "Övervaka Azure händelse rutnätet meddelandeleverans"
description: "Beskriver hur du övervakar leverans av Azure händelse rutnätet meddelanden."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1552174589e91c370c3b85a9af7b5cc1106cbb90
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Övervaka händelse rutnätet meddelandeleverans 

Den här artikeln beskriver hur du kan använda portalen för att se status för händelse leveranser.

Händelsen rutnätet innehåller varaktiga leverans. Den ger varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till registrerade webhooken för varje prenumeration. Om en webhook inte bekräfta mottagandet av en händelse inom 60 sekunder från det första försöket leverans, försöker händelse rutnätet leverans av händelsen.

Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Leverans mått

Portalen visar mätvärden för status för att leverera meddelanden om händelser.

För ämnen är mätvärden:

* **Publicera lyckades**: händelse har skickats till ämnet och bearbetas med 2xx svar.
* **Publicera misslyckades**: händelse skickas till ämnet men avvisas med en felkod.
* **Omatchade**: händelse har publiceras till ämnet, men inte matchas till en händelseprenumeration. Händelsen har tagits bort.

För prenumerationer är mätvärden:

* **Överföringen lyckades**: händelse har levererats till prenumerationens slutpunkt och får 2xx svar.
* **Leverans misslyckades**: händelse skickas till prenumerationens slutpunkt, men tog emot en 4xx eller 5xx-svar.
* **Upphört att gälla händelser**: händelsen inte leverera och alla nya försök har skickats. Händelsen har tagits bort.
* **Matchat händelser**: händelsen i avsnittet matchades av händelseprenumerationen.

## <a name="event-subscription-status"></a>Prenumerationsstatus för händelse

Om du vill visa måtten för en händelseprenumerationen söka efter **händelse rutnätet prenumerationer** i tjänsterna och markera den.

![Sök efter händelseprenumerationer](./media/monitor-event-delivery/select-event-subscriptions.png)

Filtrera efter typ av händelse, prenumeration och plats. Välj **mått** för prenumerationen att visa.

![Filtrera prenumerationer på händelser](./media/monitor-event-delivery/filter-events.png)

Visa mått för händelsen ämnet och prenumerationen.

![Visa händelse mått](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>Anpassade händelsestatus

Om du har publicerat en anpassad avsnittet kan visa du mätvärden för den. Välj den resursgrupp som innehåller avsnittet och välj ämnet.

![Välja anpassade ämne](./media/monitor-event-delivery/select-custom-topic.png)

Visa mått för avsnittet anpassade händelsen.

![Visa händelse mått](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
