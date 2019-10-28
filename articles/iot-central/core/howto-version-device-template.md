---
title: Förstå versions hantering av enhets mallar för dina Azure IoT Central-appar | Microsoft Docs
description: Iterera över dina enhetsspecifika mallar genom att skapa nya versioner och utan att påverka dina Live-anslutna enheter
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8d4e3b304000113e7ecbf748767780a3fcf17bb3
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952607"
---
# <a name="create-a-new-device-template-version"></a>Skapa en ny version av enhets mal len

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Med Azure IoT Central kan du snabbt utveckla IoT-program. Du kan snabbt iterera över dina mallar för enhets mal len genom att lägga till, redigera eller ta bort mätningar, inställningar eller egenskaper. Några av de här ändringarna kan vara påträngande för de enheter som är anslutna för tillfället. Azure IoT Central identifierar dessa ändringar och gör det möjligt att distribuera uppdateringarna till enheterna på ett säkert sätt.

En enhets mall har ett versions nummer när du skapar den. Versions numret är som standard 1.0.0. Om du redigerar en enhets mall och om denna ändring kan påverka Live-anslutna enheter, så blir det möjligt för Azure IoT Central att skapa en ny version av enhets mal len.

> [!NOTE]
> Mer information om hur du skapar en enhets mall finns i [Konfigurera en enhets mall](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>Ändringar som efterfrågar en versions ändring

I allmänna ändringar av inställningar eller egenskaper för enhets mal len visas en versions ändring.

> [!NOTE]
> Ändringar som görs i enhets mal len kommer inte att uppmanas att skapa en ny version när enheten eller en enhet är ansluten.

I följande lista beskrivs de användar åtgärder som kan kräva en ny version:

* Egenskaper (obligatoriskt)
    * Lägga till eller ta bort en obligatorisk egenskap
    * Ändra fält namnet för en egenskap, fält namn som används av enheterna för att skicka meddelanden.
*  Egenskaper (valfritt)
    * Tar bort en valfri egenskap
    * Ändra fält namnet för en egenskap, fält namn som används av enheterna för att skicka meddelanden.
    * Ändra en valfri egenskap till en obligatorisk egenskap
*  Inställningar
    * Lägga till eller ta bort en inställning
    * Ändra fält namnet för en inställning, fält namn som används av dina enheter för att skicka och ta emot meddelanden.

## <a name="what-happens-on-version-change"></a>Vad händer om versionen ändras?

Vad händer med regler och enhets instrument paneler när det finns en versions ändring?

**Reglerna** i den tidigare versionen av enhets mal len fortsätter att fungera oförändrade. Regler migreras inte automatiskt till den nya versionen av enhets mal len. Du kan skapa regler på den nya versionen av mallen som vanligt. Mer information finns i [skapa en telemetri-regel och konfigurera meddelanden i Azure IoT Central Application](howto-create-telemetry-rules.md) instruktion-artikeln.

**Enhets instrument paneler** kan innehålla flera typer av paneler. Några av panelerna kan innehålla inställningar och egenskaper. När en egenskap eller inställning som används i en panel tas bort, är panelen helt eller delvis bruten. Du kan gå till panelen och åtgärda problemet antingen genom att ta bort panelen eller uppdatera innehållet i panelen.

## <a name="migrate-a-device-across-device-template-versions"></a>Migrera en enhet över enhets mal len versioner

Du kan skapa flera versioner av enhets mal len. Med tiden kommer du att ha flera anslutna enheter som använder dessa enhets mallar. Du kan migrera enheter från en version av din enhets mall till en annan. Följande steg beskriver hur du migrerar en enhet:

1. Gå till sidan **Device Explorer** .
1. Välj den enhet som du vill migrera till en annan version.
1. Välj **migrera enhet**.
1. Välj det versions nummer som du vill migrera enheten till och välj **migrera**.

![Så här migrerar du en enhet](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder versioner av enhets mallar i ditt Azure IoT Central-program är du det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Skapa regler för telemetri](howto-create-telemetry-rules.md)