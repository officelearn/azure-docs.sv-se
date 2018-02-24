---
title: "Azure IoT-hubb och händelsen rutnät | Microsoft Docs"
description: "Använd Azure händelse rutnätet för att utlösa processer baserat på åtgärder som sker i IoT-hubb."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 7c75a65714898f27ab0008ad5a30a5714d7174f4
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Ta hänsyn till IoT-hubb händelser med hjälp av händelse rutnätet att utlösaråtgärder - förhandsgranskning

Azure IoT-hubb kan integreras med Azure händelse rutnätet så att du kan skicka händelsemeddelanden till andra tjänster och starta underordnade processer. Konfigurera dina affärsprogram att lyssna efter IoT-hubb händelser så att du kan reagera på kritiska händelser på ett tillförlitligt, skalbara och säkert sätt. Till exempel skapa ett program för att utföra flera åtgärder som uppdaterar en databas, skapar en biljett och leverera ett e-postmeddelande varje gång en ny IoT-enhet är registrerad för din IoT-hubb. 

[Azure händelse rutnätet] [ lnk-eg-overview] är en helt hanterad händelse routning tjänst som använder ett Publicera-prenumerera modellen. Händelsen rutnätet har inbyggt stöd för Azure-tjänster som [Azure Functions](../azure-functions/functions-overview.md) och [Azure Logikappar](../logic-apps/logic-apps-what-are-logic-apps.md), och kan skicka aviseringar till Azure-tjänster med hjälp av webhooks. En fullständig lista över händelsehanterare som har stöd för händelsen rutnätet finns [en introduktion till Azure händelse rutnätet][lnk-eg-overview]. 

![Azure händelse rutnätet-arkitektur](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regional tillgänglighet

Händelsen rutnätet integrationen är tillgänglig för IoT-hubbar i regioner där händelsen rutnätet stöds. Den senaste listan över regioner finns [en introduktion till Azure händelse rutnätet][lnk-eg-overview]. 

## <a name="event-types"></a>Händelsetyper

IoT-hubb publicerar följande händelse: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerade när en enhet är registrerad på en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerade när en enhet tas bort från en IoT-hubb. | 

Använd Azure-portalen eller Azure CLI för att konfigurera vilka händelser som ska publicera från varje IoT-hubb. Ett exempel försök kursen [skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Händelseschema

IoT-hubb-händelser innehåller all information som du måste svara för ändringar i livscykel för enhet. Du kan identifiera en händelse genom att kontrollera IoT-hubb som egenskapen eventType börjar med **Microsoft.Devices**. Mer information om hur du använder händelsen rutnätet händelseegenskaper finns i [händelse rutnätet Händelseschema](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Enheten som skapade schemat

I följande exempel visas schemat för en enhet som skapats i händelsen: 

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
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

En detaljerad beskrivning av varje egenskap finns [Azure händelse rutnätet Händelseschema för IoT-hubb](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtrera händelser

IoT-hubb händelseprenumerationer kan filtrera händelser baserat på typen och enheten händelsenamn. Ämne filter i händelsen rutnätet arbete baserat på **prefix** och **suffix** matchar. Filtret använder en `AND` operator, så levereras händelser med ett ämne som matchar både prefixet och suffixet till prenumeranten. 

Ämnet för IoT-händelser använder formatet:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Tips för förbrukning av händelser

Program som hanterar IoT-hubb händelser bör följa dessa rekommendationer:

* Flera prenumerationer kan konfigureras på vägen händelser för samma händelsehanteraren så att det är viktigt att inte förutsätter att händelser från en viss källa. Kontrollera alltid avsnittet meddelandet för att säkerställa att det kommer från IoT-hubb som du förväntar dig. 
* Inte förutsätt att alla händelser som visas är de typer som du förväntar dig. Kontrollera alltid eventType innan bearbetningen av meddelandet.
* Meddelanden kan tas emot i ordning eller efter en stund. Använd fältet etag för att förstå om din information om objekt är aktuell.



## <a name="next-steps"></a>Nästa steg

* [Försök självstudier för IoT-hubb-händelser](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Mer information om händelsen rutnätet][lnk-eg-overview]
* [Jämför skillnaderna mellan routning IoT-hubb händelser och meddelanden][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md