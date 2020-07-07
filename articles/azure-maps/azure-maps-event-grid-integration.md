---
title: Reagera på mappa händelser med Event Grid | Microsoft Azure Maps
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335719"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagera på Azure Maps händelser med Event Grid 

Azure Maps integreras med Azure Event Grid, så att användarna kan skicka händelse meddelanden till andra tjänster och utlösa efterföljande processer. Syftet med den här artikeln är att hjälpa dig att konfigurera dina affärs program så att de lyssnar på Azure Maps händelser. Detta gör att användarna kan reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt. Användare kan till exempel skapa ett program för att uppdatera en databas, skapa en biljett och leverera ett e-postmeddelande varje gång en enhet går in på en inhägnad.

Azure Event Grid är en helt hanterad tjänst för händelse dirigering som använder en publicerings prenumerations modell. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) och [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview). Den kan leverera händelse aviseringar till icke-Azure-tjänster som använder Webhooks. En fullständig lista över de händelse hanterare som Event Grid stöder finns i [en introduktion till Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).


![Azure Event Grid funktionell modell](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typer av Azure Maps händelser

Event Grid använder [händelse prenumerationer](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Ett Azure Maps konto avger följande händelse typer: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Utlöses när mottagna koordinater har flyttats från utsidan av ett angivet geografiskt avgränsnings tecken till inom |
| Microsoft. Maps. GeofenceExited | Utlöses när mottagna koordinater har flyttats inifrån ett angivet geografiskt avgränsnings tecken utanför |
| Microsoft. Maps. GeofenceResult | Genereras varje gång en inhägnad fråga returnerar ett resultat, oavsett tillstånd |

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

Program som hanterar Azure Mapss gräns händelser bör följa några rekommenderade metoder:

* Konfigurera flera prenumerationer för att dirigera händelser till samma händelse hanterare. Det är viktigt att inte anta att händelser kommer från en viss källa. Kontrol lera alltid meddelande ämnet för att se till att meddelandet kommer från den källa som du förväntar dig.
* Använd `X-Correlation-id` fältet i svars huvudet för att förstå om informationen om objekt är aktuell. Meddelanden kan tas emot i rätt ordning eller efter en fördröjning.
* När en GET-eller a POST-begäran i det geometriska API: t anropas med läges parametern inställt på `EnterAndExit` , genereras en retur-eller Exit-händelse för varje geometri i det avgränsnings värde som statusen har ändrats från det föregående API-anropet för polystängsel.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder polystaket för att styra åtgärder på en Bygg plats finns i:

> [!div class="nextstepaction"] 
> [Konfigurera en geofence med hjälp av Azure Maps](tutorial-geofence.md)
