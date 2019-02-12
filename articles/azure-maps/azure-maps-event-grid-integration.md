---
title: Reagera på händelser för Azure Maps med hjälp av Event Grid | Microsoft Docs
description: Lär dig mer om att reagera på händelser för Azure Maps med hjälp av Event Grid.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008512"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagera på händelser för Azure Maps med hjälp av Event Grid 

Azure Maps kan integreras med Azure Event Grid så att du kan skicka händelsemeddelanden till andra tjänster och utlösare underordnade processer. Syftet med den här artikeln är att hjälpa dig att konfigurera dina affärsprogram att lyssna efter Azure Maps-händelser så att du kan reagera på kritiska händelser på ett sätt som tillförlitlig, skalbar och säker. Till exempel skapa ett program som utför flera åtgärder som uppdaterar en databas, skapa en biljett och leverera ett e-postmeddelande varje gång som en enhet i en geofence-området.

Azure Event Grid är en fullständigt hanterad tjänst för händelsedirigering som använder en Publicera-prenumerera modellen. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) och [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview), och kan skicka aviseringar till icke-Azure-tjänster med webhookar. En fullständig lista över de händelsehanterare som har stöd för Event Grid finns [en introduktion till Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Azure Event Grid funktionella modell](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps-händelsetyper

Händelserutnät använder [händelseprenumerationer](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Ett Azure Maps-konto genererar följande händelsetyper: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Aktiveras när koordinater emot har flyttat från utanför en viss geofence-området till i |
| Microsoft.Maps.GeofenceExited | Aktiveras när koordinater emot har flyttats från inom en viss geofence-området till utanför |
| Microsoft.Maps.GeofenceResult | Aktiveras varje gång en geofencing-fråga returnerar ett resultat oavsett status |

## <a name="event-schema"></a>Händelseschema

Följande exempel visar schemat för GeofenceResult

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Tips för att använda händelser

Program som hanterar Azure Maps geofence-området händelser bör följa några rekommenderade metoder:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanteraren. Det är viktigt att inte förutsätter att händelser som kommer från en viss källa. Kontrollera alltid meddelande avsnittet för att säkerställa att det kommer från den källa som du förväntar dig.
* Meddelanden kan tas emot i fel ordning eller efter en fördröjning. Använd den `X-Correlation-id` i svarshuvudet att förstå om din information om objekt är uppdaterad.
* När får och INLÄGG geofence-området API anropas med lägesparametern inställd `EnterAndExit`, en RETUR eller avsluta händelse genereras för varje geometri i geofence-området för vilken status har ändrats från det föregående geofence-området API-anropet.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder denna geofencing till kontrollåtgärder på en plats för konstruktion, se:

> [!div class="nextstepaction"] 
> [Konfigurera en geofence-området med hjälp av Azure Maps](tutorial-geofence.md)