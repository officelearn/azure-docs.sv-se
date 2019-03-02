---
title: Förstå enheten mall versionshantering för dina appar med Azure IoT Central | Microsoft Docs
description: Iterera över mallarna för din enhet genom att skapa nya versioner och utan att påverka dina live anslutna enheter
author: sandeeppujar
ms.author: sandeepu
ms.date: 01/19/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 3889aa1e8b19e203a3f6354c2d26fb82afe2b922
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214290"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny enhet mallversion

Azure IoT Central kan snabb utveckling av IoT-program. Du kan snabbt iterera över din enhet mallar som genom att lägga till, redigera eller ta bort mätning av faktisk användning, inställningar eller egenskaper. Några av de här ändringarna kan vara störande för de anslutna enheterna. Azure IoT Central identifierar dessa ändringar och gör det möjligt att distribuera uppdateringarna på ett säkert sätt till enheter.

En mall för enheten har ett versionsnummer när du skapar den. Som standard är versionsnumret 1.0.0. Om du redigerar en mall för enheten, och om den ändringen kan påverka live-anslutna enheter, Azure IoT Central uppmanas du att skapa en ny enhet mallversion.

> [!NOTE]
> Mer information om hur du skapar en mall för enheten finns [ställa in en mall för enhet](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Ändringar som uppmanar en ändring av version

I allmänhet uppmana ändringar i inställningar eller egenskaperna för din mall för enheten versionen ändras.

> [!NOTE]
> Fråga inte ändringar som gjorts i mallen för enheten för att skapa en ny version när ingen enhet eller på de flesta en enhet är ansluten.

I följande lista beskrivs de användaråtgärder som kräver att en ny version:

* Egenskaper (krävs)
    * Att lägga till eller ta bort en obligatorisk egenskap
    * Ändra namnet på en egenskap, fältnamn som används av dina enheter för att skicka meddelanden.
*  Egenskaper (valfritt)
    * Tar bort en valfri egenskap
    * Ändra namnet på en egenskap, fältnamn som används av dina enheter för att skicka meddelanden.
    * Ändra en valfri egenskap till en obligatorisk egenskap
*  Inställningar
    * Att lägga till eller ta bort en inställning
    * Ändra namnet på en inställning, fältnamn som används av dina enheter för att skicka och ta emot meddelanden.

## <a name="what-happens-on-version-change"></a>Vad händer vid ändring av version?

Vad händer med regler och enheten instrumentpaneler när versionen ändras?

**Regler** kan innehålla villkor som är beroende av egenskaper. Om du har tagit bort en eller flera av de här egenskaperna, kan de här reglerna delas i din nya mall enhetsversion. Du kan gå till dessa specifika regler och uppdatera villkor för att åtgärda reglerna. Regler för en tidigare version bör fungera utan att påverka.

**Enheten instrumentpaneler** kan innehålla flera typer av paneler. Vissa av paneler kan innehålla inställningar och egenskaper. När en egenskap eller en inställning som används i en panel tas bort, bryts panelen helt eller delvis. Du kan gå till panelen och åtgärda problemet antingen genom att ta bort panelen eller uppdaterar innehållet i panelen.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrera en enhet över mallversioner för enhet

Du kan skapa flera versioner av mallen för enheten. Med tiden har du flera anslutna enheter med hjälp av dessa mallar för enheten. Du kan migrera enheter från en version av din enhet-mall till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till den **Device Explorer** sidan.
1. Välj den enhet som du behöver migrera till en annan version.
1. Välj **migrera enheten**.
1. Välj det lägre versionsnumret som du vill migrera till enheten och välj **migrera**.

![Så här migrerar du en enhet](media/howto-version-devicetemplate/pick-version.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder enheten mallversioner i Azure IoT Central programmet, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Hur du skapar regler för telemetri](howto-create-telemetry-rules.md)