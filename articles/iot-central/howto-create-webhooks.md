---
title: Skapa webhooks i reglerna i Azure IoT Central | Microsoft Docs
description: Skapa webhooks i Azure IoT Central som automatiskt underrättar andra program när regler utlöses.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22167de6676837c45c48a0bafd19b1ba69578827
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888333"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder på regler i Azure IoT Central

*Det här avsnittet gäller builders och administratörer.*

Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och aviseringar. Webhookarna meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i din IoT Central-app. Din IoT Central-app skickar en POST-begäran till det andra programmet HTTP-slutpunkt när en regel utlöses. Nyttolasten innehåller information om enheten och Regelinformation för utlösaren.

## <a name="set-up-the-webhook"></a>Konfigurera webhooken

I det här exemplet kan du ansluta till RequestBin att bli meddelad när regler utlöses med webhookar.

1. Öppna [RequestBin](https://requestbin.net/).

1. Skapa en ny RequestBin och kopiera den **lagerplatsens URL**.

1. Skapa en [telemetri regeln](howto-create-telemetry-rules.md) eller en [händelse regeln](howto-create-event-rules.md). Spara regeln och Lägg till en ny åtgärd.

    ![Webhooken skapas skärmen](media/howto-create-webhooks/webhookcreate.png)

1. Välj webhook-åtgärd och ange ett visningsnamn och klistra in lagerplatsens URL som Motringnings-URL.

1. Spara regeln.

Nu när regeln utlöses, visas en ny begäran visas i RequestBin.

## <a name="payload"></a>Nyttolast

När en regel utlöses görs en HTTP POST-begäran att Motringnings-URL som innehåller en json-nyttolast med mått, enhet, regel och programinformation. För en telemetri-regel nyttolasten som returneras liknar följande:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Kända begränsningar

För närvarande finns ingen programmässiga sätt att prenumerera/avslutar prenumerationen från dessa webhooks via ett API.

Om du har idéer om hur du kan förbättra den här funktionen kan du publicera dina förslag till våra [Uservoice-forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ställer in och använda webhooks, föreslagna nästa steg är att utforska [att skapa arbetsflöden i Microsoft Flow](howto-add-microsoft-flow.md).
