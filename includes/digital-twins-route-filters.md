---
author: baanders
description: inkludera fil för Azure Digitals dubbla väg filter alternativ
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902220"
---
| Filternamn | Beskrivning | Filtrera text schema | Värden som stöds | 
| --- | --- | --- | --- |
| True/false | Tillåter att du skapar en väg utan filtrering eller inaktiverar en väg så att inga händelser skickas | `<true/false>` | `true`= Route är aktiverat utan filtrering <br> `false`= vägen är inaktive rad |
| Typ | Den [typ av händelse](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) som flödar genom den digitala dubbla instansen | `type = '<eventType>'` | Här följer möjliga värden för händelse typ: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Källa | Namn på Azure Digitals dubbla instanser | `source = '<hostname>'`| Här följer möjliga värden för värdnamn: <br> **För meddelanden**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **För telemetri**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Ämne | En beskrivning av händelsen i kontexten för händelse källan ovan | `subject = '<subject>'` | Här följer möjliga ämnes värden: <br>**För meddelanden**: ämnet är`<twinid>` <br> eller ett URI-format för ämnen som identifieras unikt av flera delar eller ID:<br>`<twinid>/relationships/<relationshipid>`<br> **För telemetri**: ämnet är komponent Sök vägen (om telemetri skickas från en dubbel komponent), till exempel `comp1.comp2` . Om telemetri inte genereras från en komponent är dess ämnes fält tomt. |
| Data schema | DTDL modell-ID | `dataschema = '<model-dtmi-ID>'` | **För telemetri**: dataschemat är modell-ID: t för den dubbla eller komponenten som utvärderar Telemetrin. Till exempel `dtmi:example:com:floor4;2` <br>**För meddelanden**: data schema stöds inte|
| Innehållstyp | Innehålls typ för data värde | `datacontenttype = '<contentType>'` | Innehålls typen är`application/json` |
| Specifikations version | Den version av händelse schemat som du använder | `specversion = '<version>'` | Versionen måste vara `1.0` . Detta anger CloudEvents-schema version 1,0 |

Det är också möjligt att kombinera filter med följande åtgärder:

| Filternamn | Filtrera text schema | Exempel | 
| --- | --- | --- |
| OCH/ELLER | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| OCH/ELLER | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Kapslade åtgärder | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Under för hands versionen stöds endast sträng likhets tecken (=,! =).

När du implementerar eller uppdaterar ett filter kan det ta några minuter innan ändringen visas i data pipelinen.
