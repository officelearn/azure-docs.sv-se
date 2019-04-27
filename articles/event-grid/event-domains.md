---
title: Händelse-domäner i Azure Event Grid
description: Beskriver hur du använder Event domäner att hantera avsnitt i Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 131a55d130e7ebf619ee283e943c0b0a7b45edfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562034"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelse domäner för att hantera Event Grid-ämnen

Den här artikeln beskriver hur du använder event domäner för att hantera flödet av anpassade händelser och dina olika företag och organisationer, kunder eller program. Använd event domäner till:

* Hantera flera innehavare eventing arkitekturer i stor skala.
* Hantera auktorisering och autentisering.
* Partitionera dina ämnen utan att hantera varje individuellt.
* Undvik att publicera individuellt till var och en av dina slutpunkter i avsnittet.

Den här funktionen är en förhandsversion. För att använda den, måste du installera en förhandsversion av tillägg eller modul. Anvisningar finns i [hantera ämnen och publicera händelser med hjälp av händelse domäner](how-to-event-domains.md).

## <a name="event-domain-overview"></a>Översikt över Event-domän

En händelsedomän är ett hanteringsverktyg för stora mängder Event Grid-ämnen som är relaterade till samma program. Du kan se det som ett meta-ämne som kan ha tusentals enskilda avsnitt.

Händelsen domäner gör tillgänglig för dig samma arkitektur som används av Azure-tjänster (t.ex. lagring och IoT-hubb) för att publicera händelser. De gör att du kan publicera händelser till tusentals olika ämnen. Domäner också ge autentisering och auktorisering kontroll över varje avsnitt så att du kan partitionera dina klienter.

### <a name="example-use-case"></a>Exempel på användningsfall

Händelsen domäner beskrivs enklast med ett exempel. Vi antar att du kör Contoso konstruktion maskiner, där du tillverkar att hjul, gräva utrustning och andra tunga maskiner. Som en del av företaget, skicka information i realtid till kunder om underhåll och hälsotillståndet kontrakt uppdateringar. All information går till olika slutpunkter med din app, kund-slutpunkter och annan infrastruktur som kunder har skapat.

Händelse-domäner kan du modellen Contoso konstruktion maskiner som en enda eventing-enhet. Var och en av dina kunder representeras ett ämne i domänen. Autentisering och auktorisering hanteras med hjälp av Azure Active Directory. Var och en av dina kunder kan prenumerera på deras ämne och få de händelser som levereras till dem. Hanterad åtkomst via event domänen säkerställer att de kan bara komma åt sina avsnittet.

Du får också en enda slutpunkt som du kan publicera alla kundhändelser till. Event Grid hand tar om att se till att varje avsnitt är bara medveten om händelser som är begränsade till dess klient.

![Exempel för Contoso-konstruktion](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Åtkomsthantering

Med en domän får du noggrann autentisering och auktorisering kontroll över varje avsnitt via Azures rollbaserad åtkomstkontroll (RBAC). Du kan använda dessa roller för att begränsa varje klient i din app för endast de avsnitt som du vill ge åtkomst till.

RBAC i händelsen domäner fungerar på samma sätt [hanteras åtkomstkontroll](security-authentication.md#management-access-control) fungerar i resten av Event Grid och Azure. Använd RBAC för att skapa och genomdriva anpassade rolldefinitioner i händelsen domäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda rolldefinitioner att underlätta RBAC för att arbeta med händelsen domäner. Dessa roller är **EventGrid EventSubscription deltagare (förhandsgranskning)** och **EventGrid EventSubscription läsare (förhandsgranskning)**. Du kan tilldela dessa roller till användare som behöver prenumerera på ämnen i händelsedomän. Du kan begränsa rolltilldelning till endast som användarna behöver för att prenumerera på ämnet.

Information om dessa roller finns i [inbyggda roller för Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Prenumerera på ämnen

Prenumerera på händelser på ett ämne i en händelsedomän är samma som [skapa en händelseprenumeration på ett anpassat ämne](./custom-event-quickstart.md) eller prenumerera på en händelse från en Azure-tjänst.

### <a name="domain-scope-subscriptions"></a>Domän omfång prenumerationer

Händelse-domäner kan även domänomfattningen prenumerationer. En händelseprenumeration på en händelsedomän får alla händelser som skickas till domänen, oavsett händelserna som ska skickas till ämnet. Domän omfång prenumerationer kan vara användbart för hanterings- och granskningsändamål.

## <a name="publishing-to-an-event-domain"></a>Publicering till en händelsedomän

När du skapar en händelsedomän, får du en publiceringsslutpunkten som liknar om du har skapat ett ämne i Event Grid. 

För att publicera händelser i något ämne i en domän för händelsen, push-händelser till domänens slutpunkten i [samma sätt som för ett anpassat ämne](./post-to-custom-topic.md). Den enda skillnaden är att du måste ange avsnittet om du vill att händelsen ska levereras till.

Till exempel publicera följande matris med händelser skulle skicka händelse med `"id": "1111"` till ämnet `foo` när händelsen med `"id": "2222"` skulle skickas till ämnet `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Händelsen domäner hantera publicering till avsnitt åt dig. Du kan publicera alla händelser till domänens slutpunkten i stället för att publicera händelser till varje ämne som du hanterar individuellt. Event Grid ser till att varje händelse skickas till rätt avsnitt.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter

### <a name="control-plane"></a>Kontrollplanet

Händelse-domäner är begränsade till 1 000 ämnen inom en domän och 50 händelseprenumerationer per avsnitt inom en domän i förhandsversionen. Händelseprenumerationer domän omfånget är också begränsad till 50.

### <a name="data-plane"></a>Dataplanet

I förhandsversionen begränsas händelsegenomflöde för en händelsedomän till samma 5 000 händelser per andra inmatning hastighet som anpassade ämnen är begränsad till.

## <a name="pricing"></a>Prissättning

I förhandsversionen kan använda event domäner samma [operations priser](https://azure.microsoft.com/pricing/details/event-grid/) med alla andra funktioner i Event Grid.

Åtgärder fungerar på samma sätt i händelsen domäner som i anpassade ämnen. Varje ingångshändelser på en händelse till en händelsedomän är en åtgärd och varje leveransförsök för en händelse är en åtgärd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar event domäner, hur du skapar ämnen, skapa prenumerationer på händelser och publicera händelser, se [hantera händelse domäner](./how-to-event-domains.md).