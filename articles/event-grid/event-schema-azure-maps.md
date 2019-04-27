---
title: Schema för Azure Event Grid Azure Maps-händelse
description: Beskriver egenskaper och schema för Azure Maps-händelser med Azure Event Grid
services: event-grid
author: walsehgal
ms.service: event-grid
ms.topic: reference
ms.date: 02/08/2019
ms.author: v-musehg
ms.openlocfilehash: 74a3674e632f8dc3f0755bc2ad48376708c7966f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861862"
---
# <a name="azure-event-grid-event-schema-for-azure-maps"></a>Azure Event Grid-Händelseschema för Azure Maps

Den här artikeln innehåller egenskaperna och schema för Azure Maps-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](https://docs.microsoft.com/azure/event-grid/event-schema).

## <a name="available-event-types"></a>Tillgängliga händelsetyper

Ett Azure Maps-konto genererar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Aktiveras när koordinater emot har flyttat från utanför en viss geofence-området till i |
| Microsoft.Maps.GeofenceExited | Aktiveras när koordinater emot har flyttats från inom en viss geofence-området till utanför |
| Microsoft.Maps.GeofenceResult | Aktiveras varje gång en geofencing-fråga returnerar ett resultat oavsett status |

## <a name="event-examples"></a>Händelse-exempel

I följande exempel visas schemat för en **GeofenceEntered** händelse

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

I följande exempel visar schemat för **GeofenceResult** 

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

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | string | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| ämne | string | Publisher-definierade sökvägen till ämne för händelsen. |
| Händelsetyp | string | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | string | Unik identifierare för händelsen. |
| data | objekt | Geofencing händelsedata. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| apiCategory | string | API-kategori för händelsen. |
| ApiName | string | API-namnet på händelsen. |
| Problem | objekt | Visar en lista över problem som uppstod under bearbetning. Om eventuella problem returneras det blir inga geometrier som returneras med svaret. |
| responseCode | nummer | HTTP-svarskoden |
| geometrier | objekt | Listor Avgränsningstecken-geometrier som innehåller koordinaten placera eller överlappar searchBuffer runt positionen. |

Felobjekt returneras när ett fel uppstår i Maps-API. Felobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| fel | Felinformation |Det här objektet som returneras när ett fel uppstår i Maps-API  |

ErrorDetails objekt returnerades när ett fel uppstår i Maps-API. Den felinformation eller det objekt du har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Kod | string | HTTP-statuskoden. |
| meddelande | string | Om det finns en mänsklig läsbar beskrivning av felet. |
| innererror | InnerError | Om det finns ett objekt som innehåller service-specifik information om felet. |

InnerError är ett objekt som innehåller service-specifik information om felet. Objektet InnerError har följande egenskaper: 

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Kod | string | Ett felmeddelande. |

Objektet geometrier listar geometri ID: N för geofence-områden som har upphört att gälla i förhållande till Användartid i begäran. Objektet geometrier har geometri objekt med följande egenskaper: 

| Egenskap  | Typ | Beskrivning |
|:-------- |:---- |:----------- |
| deviceid | string | ID för enheten. |
| avstånd | string | <p>Avståndet från koordinaten till den närmaste kantlinjen för geofence-området. Positiv innebär koordinaten ligger utanför geofence-området. Om koordinaten ligger utanför geofence-området, men mer än värdet för searchBuffer bort från den närmaste geofence-området kantlinjen, är värdet 999. Negativt innebär koordinaten inuti geofence-området. Om koordinaten som finns i polygonen, men mer än värdet för searchBuffer bort från den närmaste geofencing kantlinjen, är värdet-999. Värdet 999 innebär att det finns säkert koordinaten är väl utanför geofence-området. Värdet-999 innebär att det finns säkert koordinaten är väl inom geofence-området.<p> |
| geometryid |string | Unikt id identifierar geofence-området geometri. |
| nearestlat | nummer | Latitud för den närmaste punkten i geometrin. |
| nearestlon | nummer | Longitud för den närmaste punkten i geometrin. |
| udId | string | Unikt id som returneras från användare ladda upp tjänsten när du överför en geofence-området. Inkluderas inte i API: et för geofencing-post. |

Dataobjektet har följande egenskaper:

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | Listor över geometri-ID: T för geofence-området som har upphört att gälla i förhållande till Användartid i begäran. |
| geometrier | geometries[] |Listor Avgränsningstecken-geometrier som innehåller koordinaten placera eller överlappar searchBuffer runt positionen. |
| invalidPeriodGeofenceGeometryId | string[]  | Listor över geometri-ID: T för geofence-området som tillhör ogiltig period i förhållande till Användartid i begäran. |
| isEventPublished | boolesk | SANT har om minst en händelse publiceras till Azure Maps händelse prenumerant, FALSKT om ingen händelse publicerats på Azure Maps händelse prenumeranten. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).