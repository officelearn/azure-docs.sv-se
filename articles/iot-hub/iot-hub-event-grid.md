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
ms.openlocfilehash: 73a9aebfd0c5338f63927860ce3f6c57b20428a4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754769"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagera på IoT Hub-händelser med Event Grid för att utlösaråtgärder

Azure IoT Hub kan integreras med Azure Event Grid så att du kan skicka händelsemeddelanden till andra tjänster och utlösa underordnade processer. Konfigurera dina affärsprogram att lyssna efter IoT Hub-händelser så att du kan reagera på kritiska händelser på ett sätt som tillförlitlig, skalbar och säker. Till exempel skapa ett program som uppdateras en databas, skapar en biljett för arbete och ger ett e-postmeddelande varje gång en ny IoT-enhet är registrerad på IoT-hubben.

[Azure Event Grid](../event-grid/overview.md) är en fullständigt hanterad tjänst för händelsedirigering som använder en Publicera-prenumerera modellen. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md), och kan skicka aviseringar till icke-Azure-tjänster med webhookar. En fullständig lista över de händelsehanterare som har stöd för Event Grid finns [en introduktion till Azure Event Grid](../event-grid/overview.md).

![Azure Event Grid-arkitektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Event Grid-integration är tillgänglig för IoT-hubbar finns i regioner där Event Grid stöds. Alla enhetshändelser förutom telemetri enhetshändelser är allmänt tillgängliga. Enheten telemetri händelse finns i offentlig förhandsversion och är tillgänglig i alla regioner utom östra USA, västra USA, Västeuropa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure Kina 21Vianet](/azure/china), och [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/). Den senaste listan över regioner finns [en introduktion till Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Händelsetyper

IoT Hub publicerar följande händelsetyper:

| eventType | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerade när en enhet registreras till en IoT hub. |
| Microsoft.Devices.DeviceDeleted | Publicerade när en enhet tas bort från en IoT-hubb. |
| Microsoft.Devices.DeviceConnected | Publicerade när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected | Publicerade när en enhet är bortkopplad från en IoT-hubb. |
| Microsoft.Devices.DeviceTelemetry | Publicerade när ett telemetri enhet-meddelande skickas till en IoT-hubb |

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

### <a name="device-telemetry-schema"></a>Enheten telemetri schema

Enheten telemetrimeddelanden måste vara ett giltigt JSON-format med contentType angetts till JSON och contentEncoding inställd UTF-8 i meddelandet [Systemegenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Om detta inte är inställt sedan skriver IoT Hub meddelanden i base 64-kodat format.

Du kan utöka telemetri enhetshändelser innan de publiceras till Event Grid genom att välja slutpunkten som Event Grid. Mer information finns i [meddelande Enrichments översikt](iot-hub-message-enrichments-overview.md).

I följande exempel visar schemat för en enhet telemetri händelse:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
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

Händelseprenumerationer för IoT Hub kan filtrera händelser utifrån händelsetyp, datainnehåll och ämne, vilket är namnet på enheten.

Event Grid kan [filtrering](../event-grid/event-filtering.md) på händelsen, ämnen och innehåll. När du skapar Event Grid-prenumeration måste välja du att prenumerera på valda IoT-händelser. Föremål filter i Event Grid arbete baserat på **börjar med** (prefix) och **slutar med** (suffix) matchar. Filtret använder en `AND` -operator, så levereras händelser med ett ämne som matchar både prefix och suffix till prenumeranten.

Ämnet för IoT-händelser använder formatet:

```json
devices/{deviceId}
```

Event Grid kan även användas för filtrering på attribut för varje händelse, inklusive datainnehåll. På så sätt kan du välja vilka händelser levereras meddelandets telemetri baserat innehåll. Se [avancerad filtrering](../event-grid/event-filtering.md#advanced-filtering) att visa exempel.

För icke-telemetrihändelser som DeviceConnected, DeviceDisconnected, DeviceCreated och DeviceDeleted, kan Event Grid filtrering användas när du skapar prenumerationen. Användare kan också filtrera på enhetstvillingar, meddelandeegenskaperna och brödtext via Routning-fråga för statusmeddelandet för telemetrihändelser förutom filtrering i Event Grid. Vi skapar en standard [väg](iot-hub-devguide-messages-d2c.md) baserat på Event Grid-prenumeration till enhetstelemetri i IoT Hub. Den här enda vägen kan hantera alla dina Event Grid-prenumerationer. Om du vill filtrera meddelanden innan dessa data skickas, kan du uppdatera din [routning fråga](iot-hub-devguide-routing-query-syntax.md). Observera routning frågan kan tillämpas på meddelandetexten endast om brödtexten är JSON.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Begränsningar för enheter som är kopplade och enheten frånkopplad händelser

För att få enheten är ansluten och enhetshändelser kopplas från, måste du öppna den D2C eller C2D-länk för din enhet. Om din enhet använder MQTT-protokollet, behåller IoT Hub C2D länken öppnas. För AMQP, kan du öppna länken C2D genom att anropa den [ta emot asynkrona API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet).

Länken D2C är öppen om du skickar telemetri. Om enheten anslutningen flimrar, vilket innebär att enheten ansluter och kopplar från ofta, vi kommer inte att skicka tillstånd för varje enskild anslutning, men ska publicera anslutningsstatus som en ögonblicksbild tas varje minut. Vid ett avbrott i IoT Hub publicerar vi enhetens anslutning tillstånd så fort driftstörningarna har över. Om enheten kopplar från under det avbrottet, publiceras enheten frånkopplade händelsen inom 10 minuter.

## <a name="tips-for-consuming-events"></a>Tips för att använda händelser

Program som hanterar IoT Hub-händelser bör följa dessa rekommendationer:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, så att du inte förutsätta att händelser som kommer från en viss källa. Kontrollera alltid meddelande avsnittet för att säkerställa att det kommer från IoT-hubben som du förväntar dig.

* Inte förutsätter att alla händelser som du får de typer som du förväntar dig. Kontrollera alltid händelsetyp innan behandlingen av meddelandet.

* Meddelanden kan tas emot i fel ordning eller efter en fördröjning. Använd fältet etag för att förstå om din information om objekt är aktuell för en enhet har skapats eller enhetshändelser tas bort.

## <a name="next-steps"></a>Nästa steg

* [Prova guiden för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](iot-hub-how-to-order-connection-state-events.md)

* [Läs mer om Event Grid](../event-grid/overview.md)

* [Jämför routning IoT Hub-händelser och meddelanden](iot-hub-event-grid-routing-comparison.md)
