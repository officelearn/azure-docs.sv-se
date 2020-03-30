---
title: Fråga på Azure IoT Hub-meddelanderoutning | Microsoft-dokument
description: Lär dig mer om frågespråket IoT Hub-meddelanderoutning som du kan använda för att tillämpa omfattande frågor på meddelanden för att ta emot de data som är viktiga för dig.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271114"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub-frågesyntaxen för meddelandedirigering

Med meddelanderoutning kan användare dirigera olika datatyper, nämligen enhetselemetrimeddelanden, enhetslivscykelhändelser och händelser för ändring av enhetstvilling till olika slutpunkter. Du kan också använda omfattande frågor på dessa data innan du dirigerar dem för att ta emot de data som är viktiga för dig. I den här artikeln beskrivs frågespråket IoT Hub-meddelanderoutning och några vanliga frågemönster.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du fråga efter meddelandeegenskaperna och meddelandetexten samt enhetstvillingtaggar och enhetstvillingegenskaper. Om meddelandetexten inte är JSON kan meddelanderoutning fortfarande cirkulera meddelandet, men frågor kan inte tillämpas på meddelandetexten.  Frågor beskrivs som booleska uttryck där en boolesk true gör att frågan lyckas som dirigerar alla inkommande data och Boolean false misslyckas frågan och inga data dirigeras. Om uttrycket utvärderas till null eller odefinierad behandlas det som falskt och ett fel genereras i diagnostikloggar i händelse av ett fel. Frågesyntaxen måste vara korrekt för att flödet ska kunna sparas och utvärderas.  

## <a name="message-routing-query-based-on-message-properties"></a>Fråga för meddelanderoutning baserat på meddelandeegenskaper 

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för alla enheter-till-moln-meddelanden för interoperabilitet mellan protokoll. IoT Hub-meddelandet förutsätter följande JSON-representation av meddelandet. Systemegenskaper läggs till för alla användare och identifierar innehållet i meddelandet. Användare kan selektivt lägga till programegenskaper i meddelandet. Vi rekommenderar att du använder unika egenskapsnamn eftersom IoT Hub-meddelanden från enhet till moln inte är skiftlägeskänsliga. Om du till exempel har flera egenskaper med samma namn skickar IoT Hub bara en av egenskaperna.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systemegenskaper

Systemegenskaper hjälper till att identifiera innehållet och källan till meddelandena. 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Contenttype | sträng | Användaren anger meddelandets innehållstyp. Om du vill tillåta fråga på meddelandetexten bör det här värdet anges som program/JSON. |
| innehållKodning | sträng | Användaren anger meddelandets kodningstyp. Tillåtna värden är UTF-8, UTF-16, UTF-32 om contentType är inställd på application/JSON. |
| iothub-anslutning-enhet-id | sträng | Det här värdet anges av IoT Hub och identifierar enhetens ID. Om du `$connectionDeviceId`vill fråga använder du . |
| iothub-enqueuedtime | sträng | Det här värdet anges av IoT Hub och representerar den faktiska tiden för att köa meddelandet i UTC. Om du `enqueuedTime`vill fråga använder du . |
| iothub-interface-namn | sträng | Det här värdet anges av användaren och representerar namnet på det digitala tvillinggränssnitt som implementerar telemetrimeddelandet. Om du `$interfaceName`vill fråga använder du . Den här funktionen är tillgänglig som en del av den offentliga förhandsversionen av [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md). |

Som beskrivs i [IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md)finns det ytterligare systemegenskaper i ett meddelande. Förutom **contentType**, **contentEncoding**och **enqueuedTime**kan **även connectionDeviceId** och **connectionModuleId** efterfrågas.

### <a name="application-properties"></a>Egenskaper för program

Programegenskaper är användardefinierade strängar som kan läggas till i meddelandet. Dessa fält är valfria.  

### <a name="query-expressions"></a>Frågeuttryck

En fråga om egenskaper för meddelandesystem måste `$` föregås av symbolen. Frågor om programegenskaper används med sitt namn och bör inte `$`föregås av symbolen. Om ett programegenskapsnamn börjar med `$`kommer IoT Hub att söka efter det i systemegenskaperna och det inte hittas, då kommer det att leta i programegenskaperna. Ett exempel: 

Så här frågar du om systemegenskapsinnehållKodning 

```sql
$contentEncoding = 'UTF-8'
```

Så här frågar du på programgenskapsbearbetningPath:

```sql
processingPath = 'hot'
```

Om du vill kombinera dessa frågor kan du använda booleska uttryck och funktioner:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

En fullständig lista över operatorer och funktioner som stöds visas i [Uttryck och villkor](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Fråga för meddelanderoutning baserat på meddelandetext

För att aktivera frågor om meddelandetexten ska meddelandet finnas i en JSON-kodad i antingen UTF-8, UTF-16 eller UTF-32. Måste `contentType` ställas in `application/JSON` `contentEncoding` på och till en av de UTF-kodningar som stöds i systemegenskapen. Om dessa egenskaper inte anges utvärderas inte frågeuttrycket i meddelandetexten i IoT Hub. 

I följande exempel visas hur du skapar ett meddelande med en korrekt utformad och kodad JSON-brödtext: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Detta visar hur man hanterar kodning av kroppen i javascript. Om du vill se ett exempel i C#laddar du ned [Azure IoT C# Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp filen master.zip. Visual Studio-lösningen *SimulatedDevice*'s Program.cs fil visar hur du kodar och skickar meddelanden till en IoT Hub. Det här är samma exempel som används för att testa meddelanderoutningen, vilket förklaras i [självstudiekursen För meddelanderoutning](tutorial-routing.md). Längst ner i Program.cs, den har också en metod för att läsa i en av de kodade filerna, avkoda den och skriva tillbaka den som ASCII så att du kan läsa den. 


### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelandetext måste föregås `$body`av . Du kan använda en brödtextreferens, brödrrayreferens eller flera brödtextreferenser i frågeuttrycket. Frågeuttrycket kan också kombinera en brödtextreferens med egenskaper för meddelandesystem och referens för egenskaper för meddelandeprogram. Följande är till exempel alla giltiga frågeuttryck: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Fråga för meddelanderoutning baserat på enhetstvilling 

Med meddelanderoutning kan du fråga efter [Device Twin-taggar](iot-hub-devguide-device-twins.md) och egenskaper, som är JSON-objekt. Det stöds också att fråga på modultvilling. Nedan visas ett exempel på Device Twin-taggar och egenskaper.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelandetvilling måste `$twin`föregås av . Frågeuttrycket kan också kombinera en tvillingtagg eller egenskapsreferens med en brödtextreferens, meddelandesystemegenskaper och referens för meddelandeprogramegenskaper. Vi rekommenderar att du använder unika namn i taggar och egenskaper eftersom frågan inte är skiftlägeskänslig. Detta gäller både enhetstvillingar och modultvillingar. Avstå också `twin`från `$twin` `body`att `$body`använda , , eller , som egenskapsnamn. Följande är till exempel alla giltiga frågeuttryck: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routningsfråga på brödtext- eller enhetstvilling med en punkt i nyttolasten eller egenskapsnamnet stöds inte.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [meddelanderoutning](iot-hub-devguide-messages-d2c.md).
* Prova [självstudiekursen för meddelanderoutning](tutorial-routing.md).
