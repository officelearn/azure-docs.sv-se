---
title: Händelsedomäner i Azure Event Grid
description: I den här artikeln beskrivs hur du använder händelsedomäner för att hantera flödet av anpassade händelser till dina olika affärsorganisationer, kunder eller program.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898869"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelsedomäner för hantering av avsnitt i Event Grid

I den här artikeln beskrivs hur du använder händelsedomäner för att hantera flödet av anpassade händelser till dina olika affärsorganisationer, kunder eller program. Använd händelsedomäner för att:

* Hantera flerenant eventing arkitekturer i stor skala.
* Hantera din auktorisering och autentisering.
* Partitionera dina ämnen utan att hantera varje individuellt.
* Undvik att publicera för var och en av dina ämnesslutpunkter individuellt.

## <a name="event-domain-overview"></a>Översikt över händelsedomän

En händelsedomän är ett hanteringsverktyg för ett stort antal händelserutnätsavsnitt som är relaterade till samma program. Du kan se det som ett metaämne som kan ha tusentals enskilda ämnen.

Händelsedomäner gör samma arkitektur som används av Azure-tjänster (som Lagring och IoT Hub) tillgängliga för publicering av sina händelser. De gör att du kan publicera händelser till tusentals ämnen. Domäner ger dig också auktorisering och autentisering kontroll över varje ämne så att du kan partitionera dina klienter.

### <a name="example-use-case"></a>Exempel på användningsfall

Händelsedomäner förklaras enklast med hjälp av ett exempel. Låt oss säga att du kör Contoso Construction Machinery, där du tillverkar traktorer, grävutrustning och andra tunga maskiner. Som en del av driften av verksamheten skickar du realtidsinformation till kunder om underhåll av utrustning, systemhälsa och kontraktsuppdateringar. All den här informationen går till olika slutpunkter, inklusive appen, kundslutpunkter och annan infrastruktur som kunderna har konfigurerat.

Med händelsedomäner kan du modellera Contoso Construction Machinery som en enda eventingentitet. Var och en av dina kunder representeras som ett ämne inom domänen. Autentisering och auktorisering hanteras med Azure Active Directory. Var och en av dina kunder kan prenumerera på sitt ämne och få sina evenemang levererade till dem. Hanterad åtkomst via händelsedomänen säkerställer att de bara kan komma åt sitt ämne.

Det ger dig också en enda slutpunkt, som du kan publicera alla dina kundhändelser till. Event Grid tar hand om att se till att varje ämne bara är medvetet om händelser som begränsas till dess klientorganisation.

![Exempel på Contoso-konstruktion](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Åtkomsthantering

Med en domän får du finkornig auktorisering och autentiseringskontroll över varje ämne via Azures rollbaserade åtkomstkontroll (RBAC). Du kan använda dessa roller för att begränsa varje klient i ditt program till endast de ämnen som du vill ge dem åtkomst till.

RBAC i händelse domäner fungerar på samma sätt [som hanterad åtkomstkontroll](security-authorization.md) fungerar i resten av Event Grid och Azure. Använd RBAC för att skapa och framtvinga anpassade rolldefinitioner i händelsedomäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda rolldefinitioner för att göra RBAC enklare att arbeta med händelsedomäner. Dessa roller är **EventGrid EventSubscription Contributor (Preview)** och **EventGrid EventSubscription Reader (Preview)**. Du tilldelar dessa roller till användare som behöver prenumerera på ämnen i din händelsedomän. Du scope rolltilldelningen till endast det ämne som användarna behöver prenumerera på.

Information om dessa roller finns [i Inbyggda roller för Händelserutnät](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Prenumerera på ämnen

Att prenumerera på händelser i ett ämne i en händelsedomän är detsamma som att [skapa en händelseprenumeration i ett anpassat ämne](./custom-event-quickstart.md) eller prenumerera på en händelse från en Azure-tjänst.

### <a name="domain-scope-subscriptions"></a>Prenumerationer på domänomfattning

Händelsedomäner tillåter också prenumerationer på domänomfattningar. En händelseprenumeration på en händelsedomän får alla händelser som skickas till domänen oavsett vilket ämne händelserna skickas till. Prenumerationer på domänomfattning kan vara användbara för hanterings- och granskningsändamål.

## <a name="publishing-to-an-event-domain"></a>Publicera till en händelsedomän

När du skapar en händelsedomän får du en publiceringsslutpunkt som liknar om du hade skapat ett ämne i Händelserutnät. 

Om du vill publicera händelser i ett ämne i en händelsedomän skickar du händelserna till domänens slutpunkt [på samma sätt som för ett anpassat ämne](./post-to-custom-topic.md). Den enda skillnaden är att du måste ange vilket ämne du vill att händelsen ska levereras till.

Om du till exempel publicerar följande matris `"id": "1111"` med `foo` händelser skickas `"id": "2222"` händelsen med `bar`till ämnet medan händelsen med skulle skickas till ämnet:

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

Händelsedomäner hanterar publicering till ämnen åt dig. I stället för att publicera händelser i varje ämne som du hanterar individuellt kan du publicera alla dina händelser till domänens slutpunkt. Event Grid ser till att varje händelse skickas till rätt ämne.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter
Här är de gränser och kvoter som är relaterade till händelsedomäner:

- 100 000 ämnen per händelsedomän 
- 100 händelsedomäner per Azure-prenumeration 
- 500 händelseprenumerationer per ämne i en händelsedomän
- 50 prenumerationer på domänomfattning 
- 5 000 händelser per sekunds inmatningsfrekvens (till en domän)

Om dessa gränser inte passar dig, nå ut till produktteamet genom att [askgrid@microsoft.com](mailto:askgrid@microsoft.com)öppna en supportbiljett eller genom att skicka ett e-postmeddelande till . 

## <a name="pricing"></a>Prissättning
Händelsedomäner använder samma [verksamhetspris som](https://azure.microsoft.com/pricing/details/event-grid/) alla andra funktioner i Event Grid använder.

Åtgärder fungerar på samma sätt i händelsedomäner som i anpassade ämnen. Varje inträngning av en händelse till en händelsedomän är en åtgärd och varje leveransförsök för en händelse är en åtgärd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar händelsedomäner, skapar ämnen, skapar händelseprenumerationer och publicerar händelser finns i [Hantera händelsedomäner](./how-to-event-domains.md).
