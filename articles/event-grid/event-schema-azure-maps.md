---
title: Azure Event Grid Azure Maps-händelseschema
description: Beskriver egenskaperna och schemat för Azure Maps-händelser med Azure Event Grid
services: event-grid
author: femila
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: femila
ms.openlocfilehash: 9acef524521e8fac6ce6f8f61e5ff3fbbb81d18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77486367"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Event Grid-händelseschema för Azure Maps

Den här artikeln innehåller egenskaper och schema för Azure Maps-händelser. En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Ett Azure Maps-konto avger följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Maps.Geofence har angetts | Upphöjda när koordinater som mottagits har flyttat från utsidan av en given geofence till inom |
| Microsoft.Maps.GeofenceExiterad | Upphöjda när koordinater som mottagits har flyttats inifrån en given geofence till utsidan |
| Microsoft.Maps.GeofenceResult | Utlöses varje gång en geofencing-fråga returnerar ett resultat, oavsett tillstånd |

## <a name="event-examples"></a>Exempel på evenemang

I följande exempel visas schemat för en **GeofenceEntered-händelse**

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

I följande exempel visas schema för **GeofenceResult** 

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

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Geofencing händelsedata. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| apiKategori | sträng | API-kategori för händelsen. |
| apiName (apiName) | sträng | API-namnet på händelsen. |
| problem | objekt | Visar problem som uppstått under bearbetningen. Om några problem returneras kommer inga geometrier att returneras med svaret. |
| responseCode | nummer | HTTP-svarskod |
| Geometrier | objekt | Visar en lista över de stängselgeometrier som innehåller koordinatpositionen eller överlappar searchBuffer runt positionen. |

Felobjektet returneras när ett fel inträffar i Maps API. Felobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fel | ErrorDetails |Det här objektet returneras när ett fel inträffar i Maps API  |

ErrorDetails-objektet returneras när ett fel inträffar i Maps API. ErrorDetails eller-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| kod | sträng | HTTP-statuskoden. |
| meddelande | sträng | Om det finns en läsbar beskrivning av felet. |
| innererror | InnerError | Om tillgängligt, ett objekt som innehåller tjänstspecifik information om felet. |

InnerError är ett objekt som innehåller tjänstspecifik information om felet. InnerError-objektet har följande egenskaper: 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| kod | sträng | Felmeddelandet. |

Geometrier-objektet, visar geometri-ID:er för de geofences som har upphört att gälla i förhållande till användartiden i begäran. Geometrier objektet har geometri objekt med följande egenskaper: 

| Egenskap | Typ | Beskrivning |
|:-------- |:---- |:----------- |
| Deviceid | sträng | ID för enheten. |
| avstånd | sträng | <p>Avstånd från koordinaten till den närmaste gränsen för geofence. Positivt betyder att koordinaten är utanför geofence. Om koordinaten ligger utanför geofence, men mer än värdet av searchBuffer bort från närmaste geofence gränsen, då värdet är 999. Negativ betyder att koordinaten är inne i geofence. Om koordinaten finns inuti polygonen, men mer än värdet för searchBuffer bort från närmaste geofencing-kant, är värdet -999. Värdet på 999 innebär att det finns ett stort förtroende för att koordinaten ligger långt utanför geofence. Ett värde på -999 innebär att det finns stort förtroende koordinaten är väl inom geofence.<p> |
| geometryid |sträng | Det unika id identifierar geofence geometri. |
| närmaste | nummer | Latitud för geometrins närmaste punkt. |
| nearestlon (närmaste) | nummer | Longitud för geometrins närmaste punkt. |
| udId (på) | sträng | Det unika id:t som returnerades från användaruppladdningstjänsten när du laddar upp en geofence. Kommer inte att ingå i geofencing post API. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| utgångnaGeofenceGeometryId | sträng[] | Visar en lista över geometri-ID:et för geofence som har upphört att gälla i förhållande till användartiden i begäran. |
| Geometrier | geometrier[] |Visar en lista över de stängselgeometrier som innehåller koordinatpositionen eller överlappar searchBuffer runt positionen. |
| invalidPeriodGeofenceGeometryId | sträng[]  | Visar en lista över geometri-ID:t för geofence som är ogiltigt i förhållande till användartiden i begäran. |
| isEventPublished | boolean | Sant om minst en händelse publiceras på Azure Maps-händelseprenumeranten, falskt om ingen händelse publiceras till Azure Maps-händelseprenumeranten. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).