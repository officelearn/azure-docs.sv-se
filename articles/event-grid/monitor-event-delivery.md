---
title: Övervaka Azure händelse rutnätet meddelandeleverans
description: Beskriver hur du övervakar leverans av Azure händelse rutnätet meddelanden.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: tomfitz
ms.openlocfilehash: 625f3e228bb28c85e68fb592914fb2191baf3e4e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626997"
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

Om du vill visa måtten för en händelseprenumeration kan du antingen söka efter prenumerationstypen eller prenumerationer för en viss resurs.

Om du vill söka efter händelsetyp prenumeration, Välj **alla tjänster**.

![Markera alla tjänster](./media/monitor-event-delivery/all-services.png)

Sök efter **händelse rutnätet** och välj **händelse rutnätet prenumerationer** från de tillgängliga alternativen.

![Sök efter händelseprenumerationer](./media/monitor-event-delivery/search-and-select.png)

Filtrera efter typ av händelse, prenumeration och plats. Välj **mått** för prenumerationen att visa.

![Filtrera prenumerationer på händelser](./media/monitor-event-delivery/filter-events.png)

Visa mått för händelsen ämnet och prenumerationen.

![Visa händelse mått](./media/monitor-event-delivery/subscription-metrics.png)

Välj den resursen för att hitta mätvärden för en viss resurs. Markera **händelser**.

![Välj händelser för en resurs](./media/monitor-event-delivery/select-events.png)

Du kan se mått för prenumerationer för den här resursen.

## <a name="custom-event-status"></a>Anpassade händelsestatus

Om du har publicerat en anpassad avsnittet kan visa du måtten för den. Markera resursgruppen i avsnittet och väljer avsnittet.

![Välja anpassade ämne](./media/monitor-event-delivery/select-custom-topic.png)

Visa mått för avsnittet anpassade händelsen.

![Visa händelse mått](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>Nästa steg

* Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
