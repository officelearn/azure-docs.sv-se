---
title: Köra flera åtgärder från en Central regel för Azure IoT | Microsoft-dokument
description: Kör flera åtgärder från en enda IoT Central-regel och skapa återanvändbara grupper av åtgärder som du kan köra från flera regler.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157695"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Gruppera flera åtgärder som ska köras från en eller flera regler

*Den här artikeln gäller byggare och administratörer.*

I Azure IoT Central skapar du regler för att köra åtgärder när ett villkor uppfylls. Reglerna baseras på enhetstelemetri eller händelser. Du kan till exempel meddela en operatör när temperaturen på en enhet överskrider ett tröskelvärde. I den här artikeln beskrivs hur du använder Azure *Monitor-åtgärdsgrupper* för att koppla flera åtgärder till en Central IoT-regel. [Azure Monitor](../../azure-monitor/overview.md) Du kan koppla en åtgärdsgrupp till flera regler. En [åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) är en samling meddelandeinställningar som definieras av ägaren till en Azure-prenumeration.

## <a name="prerequisites"></a>Krav

- Ett program som skapats med hjälp av en standardprisplan
- Ett Azure-konto och en prenumeration för att skapa och hantera Azure Monitor-åtgärdsgrupper

## <a name="create-action-groups"></a>Skapa åtgärdsgrupper

Du kan [skapa och hantera åtgärdsgrupper i Azure-portalen](../../azure-monitor/platform/action-groups.md) eller med en [Azure Resource Manager-mall](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

En åtgärdsgrupp kan:

- Skicka aviseringar som ett e-postmeddelande, ett SMS eller ring ett röstsamtal.
- Kör en åtgärd som att anropa en webhook.

Följande skärmbild visar en åtgärdsgrupp som skickar e-post- och SMS-meddelanden och anropar en webhook:

![Åtgärdsgrupp](media/howto-use-action-groups/actiongroup.png)

Om du vill använda en åtgärdsgrupp i en Central IoT-regel måste åtgärdsgruppen vara i samma Azure-prenumeration som IoT Central-programmet.

## <a name="use-an-action-group"></a>Använda en åtgärdsgrupp

Om du vill använda en åtgärdsgrupp i ditt IoT Central-program skapar du först en regel. När du lägger till en åtgärd i regeln väljer du **Azure Monitor Action Groups:**

![Välj åtgärd](media/howto-use-action-groups/chooseaction.png)

Välj en åtgärdsgrupp från din Azure-prenumeration:

![Välj åtgärdsgrupp](media/howto-use-action-groups/chooseactiongroup.png)

Välj **Spara**. Åtgärdsgruppen visas nu i listan över åtgärder som ska köras när regeln utlöses:

![Sparad åtgärdsgrupp](media/howto-use-action-groups/savedactiongroup.png)

I följande tabell sammanfattas informationen som skickas till åtgärdstyperna som stöds:

| Åtgärdstyp | Utdataformat |
| ----------- | -------------- |
| E-post       | Standard-IoT Central e-postmall |
| SMS         | Centralvarning för Azure IoT: ${applicationName} - "${ruleName}" som utlöses på "${deviceName}" på ${triggerDate} ${triggerTime} |
| Röst       | Azure I.O.T Central varning: regeln "${ruleName}" som utlöses på enheten "${deviceName}" på ${triggerDate} ${triggerTime}, i programmet ${applicationName} |
| Webhook     | { "schemaId": "AzureIoTCentralRuleWebhook", "data": {[vanlig webhook nyttolast](howto-create-webhooks.md#payload)}} |

Följande text är ett exempel på SMS från en åtgärdsgrupp:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder åtgärdsgrupper med regler är det föreslagna nästa steget att lära dig hur du [hanterar dina enheter](howto-manage-devices.md).
