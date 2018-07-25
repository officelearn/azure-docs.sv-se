---
title: Skapa webhooks i reglerna i Azure IoT Central | Microsoft Docs
description: Skapa webhooks i Azure IoT Central som automatiskt underrättar andra program när regler utlöses.
author: viv-liu
ms.author: viviali
ms.date: 07/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1e21076cafe21e6c0efcdf5a8146278eabd9ebc4
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228109"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder på regler i Azure IoT Central

Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och aviseringar. Webhookarna meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i din IoT Central-app. Din IoT Central-app skickar en POST-begäran till det andra programmet HTTP-slutpunkt när en regel utlöses. Nyttolasten innehåller information om enheten och Regelinformation för utlösaren. 

## <a name="how-to-set-up-the-webhook"></a>Hur du konfigurerar webhooken
I det här exemplet ansluter du till RequestBin att bli meddelad när regler utlöses med webhookar. 

1. Öppna [RequestBin](http://requestbin.net/). 
1. Skapa en ny RequestBin och kopiera den **lagerplatsens URL**. 
1. Skapa en [telemetri regeln](howto-create-telemetry-rules.md) eller en [händelse regeln](howto-create-event-rules.md). Spara regeln och Lägg till en ny åtgärd.
![Webhooken skapas skärmen](media/howto-create-webhooks/webhookcreate.png)
1. Välj webhook-åtgärd och ange ett visningsnamn och klistra in lagerplatsens URL som Motringnings-URL. 
1. Spara regeln

Du bör nu se en ny begäran visas i RequestBin när regeln utlöses.

## <a name="payload"></a>Nyttolast
När en regel utlöses görs en HTTP POST-begäran att Motringnings-URL som innehåller en json-nyttolast med mått, enhet, regel och programinformation. För en telemetri-regel nyttolasten kan se ut som följande:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
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
