---
title: Azure Maps som Event Grid källa
description: Beskriver de egenskaper och schema som anges för Azure Maps händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 4203bdf5222278b698d656835afebd9769557303
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461994"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Azure Maps som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Azure Maps händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](./event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure Maps som en händelse källa.

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Ett Azure Maps konto avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Utlöses när de mottagna koordinaterna har flyttats från utsidan av ett angivet geografiskt avgränsnings tecken till inom |
| Microsoft. Maps. GeofenceExited | Utlöses när de mottagna koordinaterna har flyttats från ett angivet geografiskt avgränsnings tecken till utsidan |
| Microsoft. Maps. GeofenceResult | Genereras varje gång en inhägnad fråga returnerar ett resultat, oavsett tillstånd |

### <a name="event-examples"></a>Händelse exempel

I följande exempel visas schemat för en **GeofenceEntered** -händelse

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

### <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Avgränsning av händelse data. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| apiCategory | sträng | Händelsens API-kategori. |
| apiName | sträng | Händelsens API-namn. |
| problem | objekt | Visar en lista över problem som uppstått under bearbetningen. Om några problem returneras kommer inga Geometries att returneras med svaret. |
| responseCode | nummer | HTTP-svarskod |
| geometries | objekt | Visar en lista över avgränsnings Geometries som innehåller koordinatens placering eller överlappar searchBuffer omkring positionen. |

Objektet Error returneras när ett fel uppstår i Maps-API: et. Objektet Error har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| fel | ErrorDetails |Det här objektet returneras när ett fel uppstår i Maps-API: et  |

ErrorDetails-objektet returneras när ett fel uppstår i Maps-API: et. ErrorDetails eller-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| kod | sträng | HTTP-statuskod. |
| meddelande | sträng | Om det är tillgängligt kan en läslig Beskrivning av felet. |
| innererror | InnerError | Om det finns ett objekt som innehåller tjänstspecifik information om felet. |

InnerError är ett objekt som innehåller tjänstspecifik information om felet. InnerError-objektet har följande egenskaper: 

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| kod | sträng | Fel meddelandet. |

Geometries-objektet, listar geometri-ID: n för de avgränsningar som har upphört att gälla i förhållande till användar tiden i begäran. Geometries-objektet har geometri objekt med följande egenskaper: 

| Egenskap | Typ | Description |
|:-------- |:---- |:----------- |
| DeviceID | sträng | ID för enhet. |
| avstånd | sträng | <p>Avståndet från koordinaten till den närmaste kanten på den närmaste gränsen. Positivt innebär att koordinaten ligger utanför den yttre gränsen. Om koordinaten ligger utanför den yttre gränsen, men mer än värdet för searchBuffer bort från närmaste yttre gräns, är värdet 999. Negativt innebär att koordinaten ligger innanför det inre avgränsnings gränsen. Om koordinaten är inuti polygonen, men mer än värdet för searchBuffer bort från den närmaste inre gränsen, är värdet-999. Värdet 999 innebär att koordinaten är mycket stor och är väl utanför den yttre gränsen. Värdet-999 innebär att det är mycket bra förtroende att koordinaten är väl inom den andra gränsen.<p> |
| geometryid |sträng | Det unika ID: t identifierar den geometriska geometrin. |
| nearestlat | nummer | Latitud för den närmsta punkten i geometrin. |
| nearestlon | nummer | Longitud för geometrins närmaste punkt. |
| udId | sträng | Det unika ID som returnerades från användar överförings tjänsten vid överföring av en inhägnad. Ingår inte i post-API för polyinhägnad. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | sträng [] | Visar en lista med geometri-ID för den avgränsning som har förfallit i förhållande till användar tiden i begäran. |
| geometries | geometries[] |Visar en lista över avgränsnings Geometries som innehåller koordinatens placering eller överlappar searchBuffer omkring positionen. |
| invalidPeriodGeofenceGeometryId | sträng []  | Visar en lista med geometri-ID för den avgränsning som är i en ogiltig period i förhållande till användar tiden i begäran. |
| isEventPublished | boolean | Sant om minst en händelse publiceras till Azure Maps händelse prenumerant, falskt om ingen händelse publiceras till Azure Maps händelse prenumerant. |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik  |Beskrivning  |
|---------|---------|
| [Reagera på Azure Maps händelser med Event Grid](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Översikt över att integrera Azure Maps med Event Grid. |
| [Självstudie: Konfigurera en gräns](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Den här självstudien vägleder dig igenom de grundläggande stegen för att konfigurera geofence med hjälp av Azure Maps. Du använder Azure Event Grid för att strömma de gränsade resultaten och ställa in ett meddelande baserat på de gränser som anges i det här resultatet. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
