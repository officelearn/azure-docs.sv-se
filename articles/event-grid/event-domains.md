---
title: Händelse domäner i Azure Event Grid
description: Beskriver hur händelse domäner används för att hantera ämnen i Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: ef0a9213d095d0b7ae4343e2af145236a7e005a1
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305413"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelse domäner för att hantera Event Grid ämnen

Den här artikeln beskriver hur du använder händelse domäner för att hantera flödet av anpassade händelser till olika affärs organisationer, kunder eller program. Använd händelse domäner för att:

* Hantera händelse arkitekturer för flera klient organisationer i stor skala.
* Hantera din auktorisering och autentisering.
* Partitionera dina ämnen utan att behöva hantera dem individuellt.
* Undvik att individuellt publicera till var och en av dina ämnes slut punkter.

## <a name="event-domain-overview"></a>Översikt över händelse domän

En händelse domän är ett hanterings verktyg för ett stort antal Event Grid ämnen relaterade till samma program. Du kan tänka på det som ett meta-ämne som kan ha tusentals enskilda ämnen.

Händelse domäner är tillgängliga för dig samma arkitektur som används av Azure-tjänster (t. ex. lagring och IoT Hub) för att publicera sina händelser. De gör att du kan publicera händelser till tusentals ämnen. Domäner ger dig också auktoriserings-och verifierings kontroll över varje ämne så att du kan partitionera dina klienter.

### <a name="example-use-case"></a>Exempel på användningsfall

Händelse domäner förklaras enklast med ett exempel. Anta att du kör Contosos konstruktions maskiner, där du tillverkar traktorer, utforska-utrustning och andra tunga maskiner. Som en del av att köra verksamheten kan du skicka real tids information till kunder om utrustnings underhåll, system hälsa och kontrakt uppdateringar. All den här informationen går till olika slut punkter, inklusive din app, kund slut punkter och annan infrastruktur som kunderna har konfigurerat.

Med händelse domäner kan du modellera Contosos konstruktions maskiner som en enskild händelse enhet. Var och en av dina kunder visas som ett ämne i domänen. Autentisering och auktorisering hanteras med hjälp av Azure Active Directory. Var och en av dina kunder kan prenumerera på sitt ämne och få sina händelser levererade till dem. Hanterad åtkomst via händelse domänen säkerställer att de endast kan komma åt sina ämnen.

Du får också en enda slut punkt, som du kan publicera alla kund händelser till. Event Grid bör vara noga med att se till att varje ämne bara känner till de händelser som omfattas av klienten.

![Exempel på Contoso-konstruktion](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Åtkomst hantering

Med en domän får du detaljerad auktorisering och verifierings kontroll över varje ämne via Azures rollbaserad åtkomst kontroll (RBAC). Du kan använda dessa roller för att begränsa varje klient i ditt program till endast de ämnen som du vill ge dem åtkomst till.

RBAC i händelse domäner fungerar på samma sätt som [hanterad åtkomst kontroll](security-authentication.md#management-access-control) fungerar i resten av event Grid och Azure. Använd RBAC för att skapa och tillämpa anpassade roll definitioner i händelse domäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda roll definitioner som gör det enklare för RBAC att arbeta med händelse domäner. De här rollerna är **EventGrid EventSubscription Contributor (för hands version)** och **EventGrid EventSubscription Reader (för hands version)** . Du tilldelar dessa roller till användare som behöver prenumerera på ämnen i din händelse domän. Du omfångerar roll tilldelningen till endast det ämne som användarna behöver för att prenumerera på.

Information om dessa roller finns i [inbyggda roller för Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Prenumererar på ämnen

Att prenumerera på händelser för ett ämne i en händelse domän är detsamma som att [skapa en händelse prenumeration i ett anpassat ämne](./custom-event-quickstart.md) eller prenumerera på en händelse från en Azure-tjänst.

### <a name="domain-scope-subscriptions"></a>Domän omfattnings prenumerationer

Händelse domäner tillåter även DNS-omfångs prenumerationer. En händelse prenumeration på en händelse domän får alla händelser som skickas till domänen, oavsett vilket ämne händelserna skickas till. DNS-omfångs prenumerationer kan vara användbara för hanterings-och gransknings syfte.

## <a name="publishing-to-an-event-domain"></a>Publicera till en händelse domän

När du skapar en händelse domän får du en publicerings slut punkt som liknar om du har skapat ett ämne i Event Grid. 

Om du vill publicera händelser till ett ämne i en händelse domän, pusha händelserna till domänens slut punkt på [samma sätt som du skulle göra för ett anpassat ämne](./post-to-custom-topic.md). Den enda skillnaden är att du måste ange det ämne som du vill att händelsen ska leverera till.

Exempel: om du publicerar följande händelse mat ris `"id": "1111"` skickas händelse med till ämne `foo` medan händelsen med `"id": "2222"` skulle skickas till ämnet `bar`:

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

Händelse domäner hanterar publicering till ämnen. I stället för att publicera händelser till varje ämne som du hanterar individuellt kan du publicera alla dina händelser till domänens slut punkt. Event Grid ser till att varje händelse skickas till rätt ämne.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter
Här följer de gränser och kvoter som rör händelse domäner:

- 100 000 ämnen per händelse domän 
- 100 händelse domäner per Azure-prenumeration 
- 500 händelseprenumerationer per ämne i en händelsedomän
- 50 domän omfångs prenumerationer 
- antal 5 000 händelser per sekund (till en domän)

Om dessa begränsningar inte passar dig kan du kontakta produkt teamet genom att öppna ett support ärende eller genom att skicka ett e [askgrid@microsoft.com](mailto:askgrid@microsoft.com)-postmeddelande till. 

## <a name="pricing"></a>Prissättning
Händelse domäner använder samma Operations- [prissättning](https://azure.microsoft.com/pricing/details/event-grid/) som alla andra funktioner i Event Grid använda.

Åtgärder fungerar på samma sätt i händelse domäner som i anpassade ämnen. Varje ingress av en händelse till en händelse domän är en åtgärd, och varje leverans försök för en händelse är en åtgärd.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar händelse domäner, skapar ämnen, skapar händelse prenumerationer och publicerar händelser finns i [Hantera händelse domäner](./how-to-event-domains.md).
