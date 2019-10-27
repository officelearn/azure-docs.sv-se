---
title: Skapa webhookar för regler i Azure IoT Central | Microsoft Docs
description: Skapa Webhooks i Azure IoT Central för att automatiskt meddela andra program när regler utlöses.
author: viv-liu
ms.author: viviali
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 77c57b8c20b3fd181cdf6ade58e478845304da8d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950553"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Skapa webhook-åtgärder på regler i Azure IoT Central

*Det här avsnittet gäller för byggare och administratörer.*

Med Webhooks kan du ansluta din IoT Central-app till andra program och tjänster för fjärrövervakning och-aviseringar. Webhooks meddelar automatiskt andra program och tjänster som du ansluter när en regel utlöses i IoT Central-appen. Din IoT Central-App skickar en POST-begäran till det andra programmets HTTP-slutpunkt när en regel utlöses. Nytto lasten innehåller information om enhets information och regel utlösare.

## <a name="set-up-the-webhook"></a>Konfigurera webhooken

I det här exemplet ansluter du till RequestBin för att få meddelanden när regler som aktive ras med Webhooks.

1. Öppna [RequestBin](https://requestbin.net/).

1. Skapa en ny RequestBin och kopiera **bin-URL: en**.

1. Skapa en [telemetri-regel](tutorial-create-telemetry-rules-pnp.md). Spara regeln och Lägg till en ny åtgärd.

    ![Skärm bild för skapande av webhook](media/howto-create-webhooks-pnp/webhookcreate.png)

1. Välj åtgärden webhook och ange ett visnings namn och klistra in URL-adressen som återanrops-URL.

1. Spara regeln.

Nu när regeln utlöses visas en ny begäran i RequestBin.

## <a name="payload"></a>Innehållet

När en regel utlöses görs en HTTP POST-begäran till återanrops-URL: en med en JSON-nyttolast med telemetri-, enhets-, regel-och program information. Nytto lasten kan se ut så här:

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

För närvarande finns det inget programmerings sätt för att prenumerera/avslutning från dessa webhookar via ett API.

Om du har idéer om hur du kan förbättra den här funktionen kan du publicera dina förslag till vårt [UserVoice-forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar och använder Webhooks är det föreslagna nästa steg att utforska [konfigurationen av Azure Monitor åtgärds grupper](howto-use-action-groups-pnp.md).
