---
title: Köra flera åtgärder från en Azure IoT Central-regel | Microsoft Docs
description: Kör flera åtgärder från en enda IoT Central regel och skapa återanvändbara grupper av åtgärder som du kan köra från flera regler.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: e51a2fc7e3af9a56b90a66935ef01ac907ebce3b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929902"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules-preview-features"></a>Gruppera flera åtgärder för att köra från en eller flera regler (för hands versions funktioner)

*Den här artikeln gäller för byggare och administratörer.*

I Azure IoT Central skapar du regler för att köra åtgärder när ett villkor uppfylls. Regler baseras på telemetri eller händelser på enheten. Du kan till exempel meddela en operatör när temperaturen på en enhet överskrider ett tröskelvärde. Den här artikeln beskriver hur du använder [Azure Monitor](../../azure-monitor/overview.md) *Åtgärds grupper* för att koppla flera åtgärder till en IoT Central-regel. Du kan koppla en åtgärds grupp till flera regler. En [Åtgärds grupp](../../azure-monitor/platform/action-groups.md) är en samling aviserings inställningar som definieras av ägaren av en Azure-prenumeration.

## <a name="prerequisites"></a>Krav

- Ett program som du betalar per användning
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

Om du vill använda en åtgärds grupp i ditt IoT Central program skapar du först en regel. När du lägger till en åtgärd i regeln väljer du **Azure Monitor åtgärds grupper**:

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
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "data": {[Normal webhook-nyttolast](howto-create-webhooks.md#payload)}} |

Följande text är ett exempel på ett SMS-meddelande från en åtgärds grupp:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder åtgärds grupper med regler, är det föreslagna nästa steg att lära dig hur du [hanterar dina enheter](howto-manage-devices.md).
