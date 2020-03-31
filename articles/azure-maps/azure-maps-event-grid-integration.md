---
title: Reagera på karthändelser med hjälp av Händelserutnät | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du reagerar på Microsoft Azure Maps-händelser med hjälp av Event Grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335719"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagera på Azure Maps-händelser med hjälp av Event Grid 

Azure Maps integreras med Azure Event Grid, så att användare kan skicka händelsemeddelanden till andra tjänster och utlösa underordnade processer. Syftet med den här artikeln är att hjälpa dig att konfigurera dina företagsprogram för att lyssna på Azure Maps-händelser. På så sätt kan användarna reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt. Användare kan till exempel skapa ett program för att uppdatera en databas, skapa en biljett och leverera ett e-postmeddelande, varje gång en enhet går in i en geofence.

Azure Event Grid är en fullständigt hanterad händelseroutningstjänst som använder en publiceringsprenumerera-modell. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) och Azure [Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview). Det kan leverera händelseaviseringar till icke-Azure-tjänster med webhooks. En fullständig lista över de händelsehanterare som Event Grid stöder finns [i En introduktion till Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Funktionell modell för Azure Event Grid](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Evenemangstyper för Azure Maps

Event grid använder [händelseprenumerationer](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Ett Azure Maps-konto avger följande händelsetyper: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Maps.Geofence har angetts | Upphöjda när mottagna koordinater har flyttat från utsidan av en given geofence till inom |
| Microsoft.Maps.GeofenceExiterad | Upphöjda när mottagna koordinater har flyttats inifrån en given geofence till utsidan |
| Microsoft.Maps.GeofenceResult | Utlöses varje gång en geofencing-fråga returnerar ett resultat, oavsett tillstånd |

## <a name="event-schema"></a>Händelseschema

I följande exempel visas schemat för GeofenceResult:

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

## <a name="tips-for-consuming-events"></a>Tips för att konsumera händelser

Program som hanterar Geofence-händelser i Azure Maps bör följa några rekommenderade metoder:

* Konfigurera flera prenumerationer för att dirigera händelser till samma händelsehanterare. Det är viktigt att inte anta att händelser kommer från en viss källa. Kontrollera alltid meddelandeavsnittet för att se till att meddelandet kommer från den källa som du förväntar dig.
* Använd `X-Correlation-id` fältet i svarshuvudet för att förstå om din information om objekt är uppdaterad. Meddelanden kan komma i oordning eller efter en fördröjning.
* När en GET- eller POST-begäran i Geofence-API:et anropas med parametern läge inställd på `EnterAndExit`genereras en Enter- eller Exit-händelse för varje geometri i den geofence som statusen har ändrats från det tidigare Geofence API-anropet.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder geofencing för att styra verksamheten på en byggarbetsplats finns i:

> [!div class="nextstepaction"] 
> [Konfigurera en geofence med hjälp av Azure Maps](tutorial-geofence.md)
