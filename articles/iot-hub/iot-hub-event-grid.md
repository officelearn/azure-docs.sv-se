---
title: Azure IoT Hub och Event Grid | Microsoft-dokument
description: Använd Azure Event Grid för att utlösa processer baserat på åtgärder som händer i IoT Hub.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: a1fd99ee595c4ae91ccd06aa41fa421ca8fcc074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284556"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>Reagera på IoT Hub-händelser med hjälp av Händelserutnät för att utlösa åtgärder

Azure IoT Hub integreras med Azure Event Grid så att du kan skicka händelsemeddelanden till andra tjänster och utlösa underordnade processer. Konfigurera dina affärsprogram för att lyssna efter IoT Hub-händelser så att du kan reagera på kritiska händelser på ett tillförlitligt, skalbart och säkert sätt.Till exempel skapa ett program som uppdaterar en databas, skapar en arbetsbiljett och levererar ett e-postmeddelande varje gång en ny IoT-enhet registreras till din IoT-hubb.

[Azure Event Grid](../event-grid/overview.md) är en fullständigt hanterad händelseroutningstjänst som använder en publiceringsprenumerera-modell. Event Grid har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och Azure [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)och kan leverera händelseaviseringar till icke-Azure-tjänster med webhooks. En fullständig lista över de händelsehanterare som Event Grid stöder finns [i En introduktion till Azure Event Grid](../event-grid/overview.md).

![Azure Event Grid-arkitektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Integreringen av eventrutnät är tillgänglig för IoT-hubbar i de regioner där Event Grid stöds. Den senaste listan över regioner finns [i En introduktion till Azure Event Grid](../event-grid/overview.md).

## <a name="event-types"></a>Händelsetyper

IoT Hub publicerar följande händelsetyper:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceSkapad | Publicerad när en enhet är registrerad på en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerad när en enhet tas bort från en IoT-hubb. |
| Microsoft.Devices.DeviceConnected | Publicerad när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected Microsoft.DevicesDisconnected Microsoft.Devices.DeviceDisconnected Microsoft.Devices. | Publicerad när en enhet kopplas från en IoT-hubb. |
| Microsoft.Devices.DeviceTelemetry | Publicerat när ett enhetstelemetrimeddelande skickas till en IoT-hubb |

Använd antingen Azure-portalen eller Azure CLI för att konfigurera vilka händelser som ska publiceras från varje IoT-hubb. Ett exempel kan du prova självstudien [Skicka e-postmeddelanden om Azure IoT Hub-händelser med Hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Händelseschema

IoT Hub-händelser innehåller all information du behöver för att svara på ändringar i enhetens livscykel. Du kan identifiera en IoT Hub-händelse genom att kontrollera att eventType-egenskapen börjar med **Microsoft.Devices**. Mer information om hur du använder händelserutnätsegenskaper finns i [händelseschemat för Händelserutnät](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Schema för enhetsansluten enhet

I följande exempel visas schemat för en enhetsansluten händelse:

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

### <a name="device-telemetry-schema"></a>Schema för enhetstelmetri

Enhetstelemetrimeddelandet måste vara i ett giltigt JSON-format med contentType inställd på **application/json** och contentEncoding inställd på **UTF-8** i [meddelandesystemets egenskaper](iot-hub-devguide-routing-query-syntax.md#system-properties). Båda dessa egenskaper är fall okänslig. Om innehållskodningen inte är inställd skriver IoT Hub meddelandena i bas 64-kodat format.

Du kan berika enhetstelemetrihändelser innan de publiceras i Event Grid genom att välja slutpunkten som händelserutnät. Mer information finns i [Översikt över meddelandeanrikningar](iot-hub-message-enrichments-overview.md).

I följande exempel visas schemat för en enhetstelemetrihändelse:

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

### <a name="device-created-schema"></a>Schema för enhetsskapade

I följande exempel visas schemat för en enhet som skapats:

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

En detaljerad beskrivning av varje egenskap finns i [Azure Event Grid-händelseschema för IoT Hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Filtrera händelser

IoT Hub-händelseprenumerationer kan filtrera händelser baserat på händelsetyp, datainnehåll och ämne, vilket är enhetens namn.

Event Grid möjliggör [filtrering](../event-grid/event-filtering.md) på händelsetyper, ämnen och datainnehåll. När du skapar prenumerationen på Event Grid kan du välja att prenumerera på valda IoT-händelser. Ämnesfilter i händelserutnätsarbete baserat på matchningar för **Begins With** (prefix) och **Slutar med** (suffix). Filtret använder `AND` en operator, så händelser med ett ämne som matchar både prefixet och suffixet levereras till prenumeranten.

Ämnet IoT-händelser använder formatet:

```json
devices/{deviceId}
```

Event Grid gör det också möjligt att filtrera på attribut för varje händelse, inklusive datainnehållet. På så sätt kan du välja vilka händelser som ska levereras baserat innehåll i telemetrimeddelandet. Se [avancerad filtrering](../event-grid/event-filtering.md#advanced-filtering) för att visa exempel. För filtrering på telemetrimeddelandetexten måste du ange contentType till **application/json** och contentEncoding till **UTF-8** i [meddelandesystemets egenskaper](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties). Båda dessa egenskaper är fall okänslig.

För icke-telemetrihändelser som DeviceConnected, DeviceDisconnected, DeviceCreated och DeviceDeleted kan filtrering av händelserutnät användas när prenumerationen skapas. För telemetrihändelser kan användare, förutom filtreringen i Händelserutnät, även filtrera på enhetstvillingar, meddelandeegenskaper och brödtext via meddelanderoutningsfrågan. 

När du prenumererar på telemetrihändelser via Event Grid skapar IoT Hub en standardmeddelandeväg för att skicka enhetsmeddelanden för datakälltyp till Event Grid. Mer information om meddelanderoutning finns i [IoT Hub-meddelanderoutning](iot-hub-devguide-messages-d2c.md). Den här vägen visas i portalen under IoT Hub > Meddelanderoutning. Endast en väg till Event Grid skapas oavsett antalet EG-prenumerationer som skapats för telemetrihändelser. Så om du behöver flera prenumerationer med olika filter kan du använda operatorn ELLER i dessa frågor på samma väg. Skapandet och borttagningen av rutten styrs genom prenumeration på telemetrihändelser via Event Grid. Du kan inte skapa eller ta bort en väg till Händelserutnät med hjälp av IoT Hub Message Routing.

Om du vill filtrera meddelanden innan telemetridata skickas kan du uppdatera [routningsfrågan](iot-hub-devguide-routing-query-syntax.md). Observera att routningsfrågan endast kan tillämpas på meddelandetexten om brödtexten är JSON. Du måste också ange contentType till **application/json** och contentEncoding till **UTF-8** i egenskaperna för [meddelandesystemet](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#system-properties).

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Begränsningar för enhet ansluten/enhet frånkopplad-händelser

För att ta emot enhetsanslutningstillståndshändelser måste en enhet antingen göra en "D2C Send Telemetry" eller en C2D-mottagningsmeddelande med Iot Hub. Observera dock att om en enhet använder AMQP-protokoll för att ansluta till Iot Hub, rekommenderas att de gör en "C2D Receive Message"-åtgärd annars kan deras meddelanden om anslutningstillstånd fördröjas med några minuter. Om enheten använder MQTT-protokollet håller IoT Hub C2D-länken öppen. För AMQP kan du öppna C2D-länken genom att anropa [Receive Async API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet), för IoT Hub C# SDK eller [enhetsklient för AMQP](iot-hub-amqp-support.md#device-client).

D2C-länken är öppen om du skickar telemetri. 

Om enhetsanslutningen flimrar, vilket innebär att enheten ansluter och kopplar från ofta, skickar vi inte varenda anslutningstillstånd, men publicerar det aktuella anslutningstillståndet som tas vid en periodisk ögonblicksbild tills flimmer fortsätter. Ta emot antingen samma anslutningstillstånd händelse med olika sekvensnummer eller olika anslutningstillstånd händelser båda innebär att det fanns en förändring i enheten anslutningstillstånd.

## <a name="tips-for-consuming-events"></a>Tips för att konsumera händelser

Program som hanterar IoT Hub-händelser bör följa dessa föreslagna metoder:

* Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, så anta inte att händelser kommer från en viss källa. Kontrollera alltid meddelandeämnet för att se till att det kommer från IoT-hubben som du förväntar dig.

* Anta inte att alla händelser du får är de typer som du förväntar dig. Kontrollera alltid eventType innan du bearbetar meddelandet.

* Meddelanden kan komma i oordning eller efter en fördröjning. Använd etag-fältet för att förstå om din information om objekt är uppdaterad för enhetsskapade eller enhetsuttagna händelser.

## <a name="next-steps"></a>Nästa steg

* [Prova självstudiekursen för IoT Hub-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Lär dig att ordna enhetsanslutningshändelser och frånkopplingar](iot-hub-how-to-order-connection-state-events.md)

* [Läs mer om Event Grid](../event-grid/overview.md)

* [Jämföra skillnaderna mellan routning av IoT Hub-händelser och meddelanden](iot-hub-event-grid-routing-comparison.md)

* [Lär dig hur du använder IoT-telemetrihändelser för att implementera IoT spatial analytics med Azure Maps](../azure-maps/tutorial-iot-hub-maps.md#create-an-azure-function-and-add-an-event-grid-subscription)
