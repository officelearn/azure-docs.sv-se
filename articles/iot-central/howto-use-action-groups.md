---
title: Köra flera åtgärder från en regel för Azure IoT Central | Microsoft Docs
description: Kör flera åtgärder från en enda IoT Central-regel och skapa grupper av åtgärder som du kan köra från flera regler.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60517238"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Gruppera flera åtgärder som ska köras från en eller flera regler

*Den här artikeln gäller för builders och administratörer.*

I Azure IoT Central, kan du skapa regler för att köra åtgärder när ett villkor uppfylls. Regler baseras på enhetstelemetri eller händelser. Du kan till exempel meddela en operatör när temperaturen i en enhet överskrider ett tröskelvärde. Den här artikeln beskriver hur du använder [Azure Monitor](../azure-monitor/overview.md) *åtgärdsgrupper* att bifoga flera åtgärder i en regel för IoT Central. Du kan koppla en åtgärdsgrupp till flera regler. En [åtgärdsgrupp](../azure-monitor/platform/action-groups.md) är en samling felaviseringar som definieras av ägaren av en Azure-prenumeration.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett program för betala per användning
- En Azure-konto och prenumeration för att skapa och hantera Azure Monitor åtgärdsgrupper

## <a name="create-action-groups"></a>Skapa åtgärdsgrupper

Du kan [skapa och hantera åtgärdsgrupper i Azure-portalen](../azure-monitor/platform/action-groups.md) eller med en [Azure Resource Manager-mall](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

En åtgärdsgrupp kan:

- Skicka meddelanden, till exempel ett e-postmeddelande, ett SMS eller göra ett röstsamtal.
- Kör en åtgärd, till exempel anropa en webhook.

I följande skärmbild visas en åtgärdsgrupp som skickar e-post och SMS-meddelanden och anropar en webhook:

![Åtgärdsgrupp](media/howto-use-action-groups/actiongroup.png)

Om du vill använda en åtgärdsgrupp i en regel för IoT Central måste åtgärdsgruppen vara i samma Azure-prenumeration som IoT Central-programmet.

## <a name="use-an-action-group"></a>Använda en åtgärdsgrupp

För att använda en åtgärdsgrupp i ditt IoT Central-program måste du först skapa en telemetri- eller händelse regel. När du lägger till en åtgärd för regeln, Välj **Azure Monitor-åtgärdsgrupper**:

![Välj åtgärd](media/howto-use-action-groups/chooseaction.png)

Välj en åtgärdsgrupp från Azure-prenumerationen:

![Välj åtgärdsgrupp](media/howto-use-action-groups/chooseactiongroup.png)

Välj **Spara**. Åtgärdsgruppen visas nu i listan över åtgärder som ska köras när regeln utlöses:

![Sparade åtgärdsgrupp](media/howto-use-action-groups/savedactiongroup.png)

I följande tabell sammanfattas informationen som skickas till åtgärdstyperna som stöds:

| Åtgärdstyp | Utdataformat |
| ----------- | -------------- |
| E-post       | Standard IoT Central e-postmall |
| SMS         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| Röst       | Azure I.O.T Central-avisering: regeln ”${ruleName}” utlöses på enhet ”${deviceName}” på ${triggerDate} ${triggerTime}, i programmet ${applicationName} |
| Webhook     | {”schemaId”: "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

Följande text är ett exempel SMS-meddelande från en åtgärdsgrupp:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Följande JSON visar ett exempel webhook-nyttolasten för åtgärd:

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

Nu när du har lärt dig hur du använder åtgärdsgrupper med regler, föreslagna nästa steg är att lära dig hur du [hantera dina enheter](howto-manage-devices.md).
