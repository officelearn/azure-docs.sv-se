---
title: Fråga på Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Utvecklarguide – fråga synxtax för meddelanderoutning i Azure IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 8e9321e72727c1a3149ff2e78b8cb1248734cb88
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978513"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub meddelanderoutning frågesyntax

Meddelanderoutning gör att användarna kan nämligen dirigera olika datatyper, meddelanden från enheten telemetri, Livscykelhändelser för enhet och enhetstvillingen ändra händelser till olika slutpunkter. Du kan också använda komplexa frågor till dessa data innan du skicka det för att ta emot de data som är viktiga för dig. Den här artikeln beskriver meddelande routning frågespråk för IoT Hub och innehåller några vanliga frågemönster 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Meddelanderoutning kan du fråga på meddelandeegenskaperna och meddelandetexten som enheten twin taggar och tvillingegenskaper. Om meddelandetexten inte är JSON, meddelanderoutning kan vidarebefordra meddelandet fortfarande, men frågor kan inte användas i meddelandetexten.  Frågorna är beskrivs som booleska uttryck där en booleskt sann gör att frågan lyckas som dirigerar alla inkommande data, och booleskt falsk misslyckas frågan och inga data skickas. Om uttrycket utvärderas till null eller odefinierad, behandlas den som FALSKT och kommer att genereras ett fel i diagnostikloggar om ett fel uppstår. Frågesyntaxen måste vara korrekt för vägen som ska sparas och utvärderas.  

## <a name="message-routing-query-based-on-message-properties"></a>Routning fråga för statusmeddelandet baserat på meddelandeegenskaper 

IoT-hubben som definierar en [vanligt format](../iot-hub/iot-hub-devguide-messages-construct.md) för alla enhet till moln-meddelanden för interoperatbility oavsett protokoll. IoT Hub-meddelande förutsätter följande JSON-representation av meddelandet. Systemegenskaper har lagts till för alla användare och identifiera innehållet i meddelandet. Användare kan selektivt lägga till egenskaper för program i meddelandet. Vi rekommenderar att du använder unika egenskapsnamn som IoT Hub-enhet till moln-meddelanden inte är skiftlägeskänsligt. Till exempel om du har flera egenskaper med samma namn, skickar IoT Hub bara en av egenskaperna.  

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

Systemegenskaper att identifiera innehåll och källan för meddelanden. 

| Egenskap  | Typ | Beskrivning |
| -------- | ---- | ----------- |
| contentType | sträng | Användaren anger innehållstypen för meddelandet. För att fråga på meddelandets brödtext ska sättas det här värdet application/JSON. |
| contentEncoding | sträng | Användaren anger kodningstyp för meddelandet. Tillåtna värden är UTF-8, UTF-16, UTF-32 om contentType har angetts till application/JSON. |
| ConnectionDeviceId | sträng | Det här värdet anges av IoT Hub och identifierar källan för meddelanden. Det kan enheten telemetrimeddelanden, device twin ändringsmeddelanden eller Livscykelhändelser för enhet. Detta kan inte frågas. |
| iothub-enqueuedtime | sträng | Det här värdet anges av IoT Hub och visar faktiska enqueuing meddelandet i UTC-tid. Om du vill fråga, Använd `'enqueuedTime'`. |

Mer information finns i den [meddelanden från IoT Hub](iot-hub-devguide-messages-construct.md), det finns ytterligare Systemegenskaper i ett meddelande. Förutom **contentType**, **contentEncoding** och **enqueuedTime**, **connectionDeviceId** och  **connectionModuleId** kan också efterfrågas.

### <a name="application-properties"></a>Egenskaper för program

Egenskaper för program är en användardefinierad strängar som kan läggas till i meddelandet. De här fälten är valfria.  

### <a name="query-expressions"></a>Frågeuttryck

En fråga på meddelandet Systemegenskaper måste föregås av den `'$'` symbolen. Frågor om egenskaper för program som nås med deras namn och ska inte föregås av den `'$'`symbolen. Om ett program egenskapsnamn som börjar med `'$'`, sedan IoT-hubben ska söka efter den i systemegenskaperna för och det finns inte sedan det ut i egenskaperna för programmet. Exempel: 

Att fråga på system egenskapen contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Att fråga på program egenskapen processingPath
```sql
processingPath = 'hot'
```

Om du vill kombinera dessa frågor kan du använda booleskt uttryck och funktioner 
```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

En fullständig lista över stöds operatorer och funktioner finns i [uttryck och villkor](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language#expressions-and-conditions
)

## <a name="message-routing-query-based-on-message-body"></a>Routning fråga för statusmeddelandet baserat på meddelandetext 

Om du vill aktivera frågor på meddelandetexten, ska meddelandet vara i en JSON-kodad i UTF-8, UTF-16- eller UTF-32. Den `contentType` måste anges till `application/JSON` och `contentEncoding` till någon av stöds UTF-kodningar i Systemegenskapen. Om dessa egenskaper inte anges, utvärderas inte frågeuttryck på meddelandetexten i IoT Hub. 

I följande exempel visas hur du skapar ett meddelande med en korrekt formaterad och kodade JSON-texten: 

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

### <a name="query-expressions"></a>Frågeuttryck

En fråga på meddelandetexten måste föregås av den `$body`. Du kan använda en brödtext-referens, brödtext matrisreferens eller flera brödtext referenser i frågeuttrycket. Din frågeuttryck kan också kombinera en brödtext-referens med meddelandeegenskaper för system och program egenskaper referens. Följande är alla giltiga frågeuttryck: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Routning fråga för statusmeddelandet baserat på enhetstvilling 

Meddelanderoutning kan du fråga på [Enhetstvillingen](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) taggar och egenskaper som är JSON-objekt. Observera att frågor på modultvilling inte stöds. Ett exempel på Enhetstvilling-taggar och egenskaper visas nedan.

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

En fråga på meddelandetexten måste föregås av den `$twin`. Din frågeuttryck kan också kombinera twin tagg eller egenskapen referens med en brödtext-referens, meddelandeegenskaper för system och program egenskaper referens. Vi rekommenderar att du använder unika namn i taggar och egenskaper som frågan inte är skiftlägeskänsligt. Också avstå från att använda `twin`, `$twin`, `body` eller `$body`, som ett egenskapsnamn. Följande är alla giltiga frågeuttryck: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```
```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [meddelanderoutning](iot-hub-devguide-messages-d2c.md)
* Prova den [message routing självstudien](tutorial-routing.md)