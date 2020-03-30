---
title: Skapa webhooks på regler i Azure IoT Central | Microsoft-dokument
description: Skapa webhooks i Azure IoT Central för att automatiskt meddela andra program när regler aktiveras.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158103"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder för regler i Azure IoT Central

*Det här avsnittet gäller byggare och administratörer.*

Med Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och meddelanden. Webhooks meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i din IoT Central-app. Din IoT Central-app skickar en POST-begäran till det andra programmets HTTP-slutpunkt när en regel utlöses. Nyttolasten innehåller enhetsinformation och information om regelutlösare.

## <a name="set-up-the-webhook"></a>Konfigurera webhook

I det här exemplet ansluter du till RequestBin för att få ett meddelande när regler avfyras med webhooks.

1. Öppna [RequestBin](https://requestbin.net/).

1. Skapa ett nytt RequestBin och kopiera **lagerplats-URL:en**.

1. Skapa en [telemetriregel](tutorial-create-telemetry-rules.md). Spara regeln och lägg till en ny åtgärd.

    ![Skärm för att skapa webhook](media/howto-create-webhooks/webhookcreate.png)

1. Välj webhook-åtgärden och ange ett visningsnamn och klistra in bin-URL:en som motringnings-URL.

1. Spara regeln.

Nu när regeln utlöses visas en ny begäran i RequestBin.

## <a name="payload"></a>Nyttolast

När en regel utlöses görs en HTTP POST-begäran till motringnings-URL:en som innehåller en json-nyttolast med telemetri, enhet, regel och programinformation. Nyttolasten kan se ut så här:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Kända begränsningar

För närvarande finns det inget programmatiskt sätt att prenumerera / avregistrera sig från dessa webhooks via ett API.

Om du har idéer för hur du kan förbättra den här funktionen, skicka dina förslag till vår [användare röst forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar och använder webhooks är det föreslagna nästa steget att utforska [konfigurera Azure Monitor Action Groups](howto-use-action-groups.md).
