---
title: Azure IoT Hub och Event Grid | Microsoft Docs
description: Använd Azure Event Grid för att utlösa processer baserat på åtgärder som sker i IoT Hub.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: a2c49a6ba269321d1903565ace3ebaae3f3b917e
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673861"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder

Azure IoT Hub kan integreras med Azure Event Grid så att du kan skicka händelsemeddelanden till andra tjänster och utlösa underordnade processer. Konfigurera dina affärsprogram att lyssna efter IoT Hub-händelser så att du kan reagera på kritiska händelser på ett sätt som tillförlitlig, skalbar och säker. Till exempel skapa ett program som uppdateras en databas, skapar en biljett för arbete och ger ett e-postmeddelande varje gång en ny IoT-enhet är registrerad på IoT-hubben. 

[Azure Event Grid](../event-grid/overview.md) är en fullständigt hanterad tjänst för händelsedirigering som använder en Publicera-prenumerera modellen. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), och kan skicka aviseringar till icke-Azure-tjänster med webhookar. En fullständig lista över de händelsehanterare som har stöd för Event Grid finns [en introduktion till Azure Event Grid](../event-grid/overview.md). 

![Azure Event Grid-arkitektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Event Grid-integration är tillgänglig för IoT-hubbar finns i regioner där Event Grid stöds. Den senaste listan över regioner finns [en introduktion till Azure Event Grid](../event-grid/overview.md). 

## <a name="event-types"></a>Händelsetyper

IoT Hub publicerar följande händelsetyper: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerade när en enhet registreras till en IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publicerade när en enhet tas bort från en IoT-hubb. |
| Microsoft.Devices.DeviceConnected | Publicerade när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected | Publicerade när en enhet är bortkopplad från en IoT-hubb. |

Använd Azure portal eller Azure CLI för att konfigurera vilka händelser som ska publicera från varje IoT-hubb. Exempelvis kan du prova guiden [skicka e-postaviseringar om Azure IoT Hub-händelser med hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Händelseschema

IoT Hub-händelser innehåller all information du behöver för att svara på ändringar i enhetens livscykel. Du kan identifiera en IoT Hub-händelse genom att kontrollera som egenskapen händelsetyp börjar med **Microsoft.Devices**. Mer information om hur du använder Event Grid händelseegenskaper finns i den [Event Grid Händelseschema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Enheten ansluten schema

I följande exempel visar schemat för en ansluten enhet-händelse: 

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

### <a name="device-created-schema"></a>Schemat för en enhet har skapats

I följande exempel visar schemat för en enhet som skapats av händelse: 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

En detaljerad beskrivning av varje egenskap finns [Azure Event Grid Händelseschema för IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrera händelser

Händelseprenumerationer för IoT Hub kan filtrera händelser baserat på händelse typ och enhetens namn. Föremål filter i Event Grid arbete baserat på **börjar med** (prefix) och **slutar med** (suffix) matchar. Filtret använder en `AND` -operator, så levereras händelser med ett ämne som matchar både prefix och suffix till prenumeranten. 

Ämnet för IoT-händelser använder formatet:

```json
devices/{deviceId}
```
## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Begränsningar för enheter som är kopplade och enheten frånkopplad händelser

För att få enheten är ansluten och enhetshändelser kopplas från, måste du öppna den D2C eller C2D-länk för din enhet. Om din enhet använder MQTT-protokollet, behåller IoT Hub C2D länken öppnas. För AMQP, kan du öppna länken C2D genom att anropa den [ta emot asynkrona API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet). 

Länken D2C är öppen om du skickar telemetri. Om enheten anslutningen flimrar, vilket innebär att enheten ansluter och kopplar från ofta, vi kommer inte att skicka tillstånd för varje enskild anslutning, men ska publicera Anslutningsstatus är ögonblicksbild av varje minut. Vid ett avbrott i IoT Hub publicerar vi enhetens anslutning tillstånd så fort driftstörningarna har över. Om enheten kopplar från under det avbrottet, publiceras enheten frånkopplade händelsen inom 10 minuter.

## <a name="tips-for-consuming-events"></a>Tips för att använda händelser

Program som hanterar IoT Hub-händelser bör följa dessa rekommendationer:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, så att du inte förutsätta att händelser som kommer från en viss källa. Kontrollera alltid meddelande avsnittet för att säkerställa att det kommer från IoT-hubben som du förväntar dig. 

* Inte förutsätter att alla händelser som du får de typer som du förväntar dig. Kontrollera alltid händelsetyp innan behandlingen av meddelandet.

* Meddelanden kan tas emot i fel ordning eller efter en fördröjning. Använd fältet etag för att förstå om din information om objekt är uppdaterad.

## <a name="next-steps"></a>Nästa steg

* [Prova guiden för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](iot-hub-how-to-order-connection-state-events.md)
* [Läs mer om Event Grid](../event-grid/overview.md)
* [Jämför routning IoT Hub-händelser och meddelanden](iot-hub-event-grid-routing-comparison.md)
