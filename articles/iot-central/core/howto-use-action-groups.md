---
title: Köra flera åtgärder från en Azure IoT Central-regel | Microsoft Docs
description: Kör flera åtgärder från en enda IoT Central regel och skapa återanvändbara grupper av åtgärder som du kan köra från flera regler.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 1992b8925d5d9ba59c36452187f5b6eb510e72dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990818"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Gruppera flera åtgärder för att köra från en eller flera regler

*Den här artikeln gäller för byggare och administratörer.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

I Azure IoT Central skapar du regler för att köra åtgärder när ett villkor uppfylls. Regler baseras på telemetri eller händelser på enheten. Du kan till exempel meddela en operatör när temperaturen i en enhet överskrider ett tröskelvärde. Den här artikeln beskriver hur du använder [Azure Monitor](../../azure-monitor/overview.md) *Åtgärds grupper* för att koppla flera åtgärder till en IoT Central-regel. Du kan koppla en åtgärds grupp till flera regler. En [Åtgärds grupp](../../azure-monitor/platform/action-groups.md) är en samling aviserings inställningar som definieras av ägaren av en Azure-prenumeration.

## <a name="prerequisites"></a>Krav

- Ett program som skapats med en standard pris plan
- Ett Azure-konto och-prenumeration för att skapa och hantera Azure Monitor åtgärds grupper

## <a name="create-action-groups"></a>Skapa åtgärdsgrupper

Du kan [skapa och hantera åtgärds grupper i Azure Portal](../../azure-monitor/platform/action-groups.md) eller med en [Azure Resource Manager mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

En åtgärds grupp kan:

- Skicka meddelanden som ett e-postmeddelande, ett SMS eller ett röst samtal.
- Kör en åtgärd som att anropa en webhook.

Följande skärm bild visar en åtgärds grupp som skickar e-post och SMS-meddelanden och anropar en webhook:

![Åtgärds grupp](media/howto-use-action-groups/actiongroup.png)

Om du vill använda en åtgärds grupp i en IoT Central regel måste åtgärds gruppen finnas i samma Azure-prenumeration som IoT Central programmet.

## <a name="use-an-action-group"></a>Använda en åtgärds grupp

Om du vill använda en åtgärds grupp i ditt IoT Central program måste du först skapa en telemetri eller händelse regel. När du lägger till en åtgärd i regeln väljer du **Azure Monitor åtgärds grupper**:

![Välj åtgärd](media/howto-use-action-groups/chooseaction.png)

Välj en åtgärds grupp från din Azure-prenumeration:

![Välj åtgärds grupp](media/howto-use-action-groups/chooseactiongroup.png)

Välj **Spara**. Åtgärds gruppen visas nu i listan med åtgärder som ska köras när regeln utlöses:

![Sparad åtgärds grupp](media/howto-use-action-groups/savedactiongroup.png)

I följande tabell sammanfattas informationen som skickas till de åtgärds typer som stöds:

| Åtgärdstyp | Utdataformat |
| ----------- | -------------- |
| E-post       | Standard IoT Central e-postmall |
| SMS         | Azure IoT Central-avisering: $ {applicationName}-"$ {ruleName}" utlöses på "$ {enhets namn}" på $ {triggerDate} $ {triggerTime} |
| Röst       | Azure I. O. T Central avisering: regel "$ {ruleName}" utlöst på enheten "$ {enhets namn}" vid $ {triggerDate} $ {triggerTime}, i programmet $ {applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[Normal webhook-nyttolast](#payload)}} |

Följande text är ett exempel på ett SMS-meddelande från en åtgärds grupp:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>Följande JSON visar ett exempel på en nytto last för webhook-åtgärd:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder åtgärds grupper med regler, är det föreslagna nästa steg att lära dig hur du [hanterar dina enheter](howto-manage-devices.md).
