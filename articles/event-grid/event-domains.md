---
title: Händelse-domäner i Azure Event Grid
description: Beskriver hur du använder Event domäner att hantera avsnitt i Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634322"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Förstå händelse domäner för att hantera Event Grid-ämnen

Den här artikeln beskriver hur du använder Event domäner för att hantera flödet av anpassade händelser och dina olika företag, organisationer, kunder eller program. Använd Event domäner till:

* Hantera flera innehavare eventing arkitekturer i stor skala.
* Hantera auktorisering och autentisering.
* Partitionera dina ämnen utan att hantera varje individuellt.
* Undvik att publicera individuellt till var och en av dina slutpunkter i avsnittet.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Översikt över Event-domäner

En händelse-domän är ett hanteringsverktyg för stora mängder Event Grid-ämnen som rör samma program. Du kan se det som ett meta-ämne som kan innehålla tusentals enskilda avsnitt.

Händelse med domäner är arkitekturen Azure-tjänster som lagring och IoT Hub använder för att publicera sina händelser som är tillgängliga för dig att använda i ditt system. De gör att du kan publicera händelser till tusentals olika ämnen. Domäner också ge autentisering och auktorisering kontroll över varje avsnitt så att du kan partitionera dina klienter.

### <a name="example-use-case"></a>Exempel användningsfall

Händelsen domäner beskrivs enklast med ett exempel. Vi antar att du kör Contoso konstruktion maskiner, där du tillverkar att hjul, gräva utrustning och andra tunga maskiner. Som en del av företaget, push-Överför information i realtid till kunder om Underhåll hälsotillståndet, uppdateringar m.m.-kontrakt. All information går till olika slutpunkter som din app, kund-slutpunkter och andra infrastruktur-kunder har konfigurerat.

Händelse-domäner kan du modellen Contoso konstruktion maskiner som en enda eventing-enhet. Var och en av dina kunder representeras ett ämne i domänen / autentisering och auktorisering hanteras med hjälp av Azure Active Directory. Var och en av dina kunder kan prenumerera på deras ämne och få de händelser som levereras till dem. Hanterad åtkomst via Event domänen säkerställer att de kan bara komma åt sina avsnittet.

Du får också en enda slutpunkt som du kan publicera alla kundhändelser till. Event Grid hand tar om att se till att varje avsnitt är bara medveten om händelser som är begränsade till dess klient.

![Exempel för Contoso-konstruktion](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Åtkomsthantering

 Med en domän får du noggrann autentisering och auktorisering kontroll över varje avsnitt via Azure rollbaserad Kontrollera (RBAC). Du kan använda dessa roller för att begränsa varje klient i din app för endast de avsnitt som du vill ge åtkomst till.

Rollbaserad Kontrollera (RBAC) i händelse domäner fungerar på samma sätt [hanteras åtkomstkontroll](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) fungerar i resten av Event Grid och Azure. Använd RBAC för att skapa och genomdriva anpassade rolldefinitioner i händelsen domäner.

### <a name="built-in-roles"></a>Inbyggda roller

Event Grid har två inbyggda rolldefinitioner att underlätta RBAC:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription deltagare (förhandsgranskning)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription läsare (förhandsgranskning)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>Prenumerera på ämnen

Prenumerera på händelser på ett ämne i en domän för händelsen är samma som [skapa en händelseprenumeration på ett anpassat ämne](./custom-event-quickstart.md) eller prenumererar på någon av inbyggda händelse utgivare Azure erbjuder.

### <a name="domain-scope-subscriptions"></a>Domän omfång prenumerationer

Händelse-domäner kan även domän omfång prenumerationer. En händelseprenumeration på en domän för händelse får alla händelser som skickas till domänen, oavsett händelserna som ska skickas till ämnet. Domän omfång prenumerationer kan vara användbart för hanterings- och granskningsändamål.

## <a name="publishing-to-an-event-domain"></a>Publicering till en händelsedomän

När du skapar en händelse-domän, får du en publiceringsslutpunkten som liknar om du har skapat ett ämne i Event Grid. 

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

Händelsen domäner hantera publicering till avsnitt åt dig. I stället för att publicera händelser på varje avsnitt som du hanterar individuellt, kan du publicera alla händelser till domänens slutpunkt och Event Grid tar hand om att se till att varje händelse skickas till rätt avsnitt.

## <a name="limits-and-quotas"></a>Begränsningar och kvoter

### <a name="control-plane"></a>Kontrollplanet

I förhandsversionen begränsas händelse domäner till 1 000 ämnen inom en domän och 50 händelseprenumerationer per avsnitt inom en domän. Händelseprenumerationer domän omfång också begränsas till 50.

### <a name="data-plane"></a>Dataplanet

I förhandsversionen begränsas händelsegenomflöde för en händelsedomän till samma 5 000 händelser per andra inmatning hastighet som anpassade ämnen är begränsad till.

## <a name="pricing"></a>Prissättning

I förhandsversionen händelse domäner kommer att använda samma [operations priser](https://azure.microsoft.com/pricing/details/event-grid/) med alla andra funktioner i Event Grid.

Åtgärder fungerar på samma sätt i händelsen domäner som i anpassade ämnen. Varje ingångshändelser på en händelse till en händelse-domän är en åtgärd och varje leveransförsök för en händelse är en åtgärd.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar Event domäner, hur du skapar ämnen, skapa prenumerationer på händelser och publicera händelser, se [hantera domäner för händelsen](./how-to-event-domains.md).