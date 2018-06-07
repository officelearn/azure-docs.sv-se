---
title: Förstå enheten mallen versionshantering för dina Azure IoT centrala appar | Microsoft Docs
description: Iterera över mallarna enheten genom att skapa nya versioner och utan att påverka dina live anslutna enheter
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b125d822596675b138560c14c76f9a3120ce3424
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628835"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny enhet mallversion

Microsoft Azure IoT Central tillåter snabb utveckling av IoT-appar. Du kan snabbt iterera över din enhet mallar som genom att lägga till, redigera eller ta bort mått, inställningar eller egenskaper. Några av de här ändringarna kan vara störande för de anslutna enheterna. Azure IoT Central identifierar dessa viktiga förändringar för och ger ett sätt att distribuera uppdateringarna på ett säkert sätt till enheter.

En mall för enheten har ett versionsnummer när du skapar den. Som standard är versionsnumret 1.0.0. Om du redigerar en mall för enheten och som kan påverka live anslutna enheter, Azure IoT centrala uppmanas du att skapa en ny enhet mallversion.

> [!NOTE]
> Mer information om hur du skapar en mall för enheten finns [ställa in en mall för enhet](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Ändringar som begär en ändringsbegäran för version

Ändringar av inställningar eller egenskaper för enhet mallen fråga i allmänhet en ändring av version.

> [!NOTE]
> Fråga inte ändringar av mallen för enheten för att skapa en ny version när ingen enhet eller på de flesta en enhet är ansluten.

I följande lista beskrivs användaråtgärder som kräver att en ny version:

* Egenskaper (krävs)
    * Lägga till eller ta bort en obligatorisk egenskap
    * Ändra namnet på en egenskap, fältnamn som används av dina enheter för att skicka meddelanden.
*  Egenskaper (valfritt)
    * Om du tar bort en valfri egenskap
    * Ändra namnet på en egenskap, fältnamn som används av dina enheter för att skicka meddelanden.
    * Ändra en valfri egenskap till en obligatorisk egenskap
*  Inställningar
    * Lägga till eller ta bort en inställning
    * Ändra namnet på en inställning fältnamn som används av dina enheter för att skicka och ta emot meddelanden.

## <a name="what-happens-on-version-change"></a>Vad som händer på version ändringen?

Vad händer med regler och enheten instrumentpaneler när en ändring har gjorts version?

**Regler** kan innehålla villkor som är beroende av egenskaper. Om du har tagit bort en eller flera av de här egenskaperna kan reglerna delas i den nya enhet mallen versionen. Du kan gå till dessa specifika regler och uppdatera villkoren för att åtgärda reglerna. Regler för en tidigare version bör fungera utan att påverka.

**Enheten instrumentpaneler** kan innehålla flera typer av paneler. Vissa av panelerna kan innehålla inställningar och egenskaper. När en egenskap eller inställningen som används i en panel tas bort bryts panelen helt eller delvis. Du kan gå till panelen och åtgärda problemet genom att ta bort panelen eller uppdaterar innehållet i panelen.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrera en enhet över mallversioner för enhet

Du kan skapa flera versioner av mallen för enheten. Över tiden har du flera anslutna enheter med hjälp av dessa mallar för enheten. Du kan migrera enheter från en version av mallen för enheten till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till den **Explorer** sidan.
1. Välj den enhet som du måste migrera till en annan version.
1. Välj **migrera enheten**.
1. Välj versionsnumret som du vill migrera enheten och välj **migrera**.

![Så här migrerar du en enhet](media\howto-version-devicetemplate\pick-version.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enheten mallversioner i dina Azure IoT centralt program, är här det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Hur du skapar regler för telemetri](howto-create-telemetry-rules.md)