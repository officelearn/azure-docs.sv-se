---
title: Skapa och köra jobb i ditt program med Azure IoT Central | Microsoft Docs
description: Azure IoT Central-jobb gör det möjligt att bulk hanteringsfunktioner för enheter, till exempel uppdaterar en enhetsegenskap, inställning eller ett kommando har körts.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 02/04/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 8667b7747a8874451e21fbf8aea19f51b6b6252f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314236"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i din Azure IoT Central-App

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i stor skala med jobb. Jobb-funktioner kan du utföra massuppdateringar till enhetsegenskaper, inställningar och kommandon. Den här artikeln visar dig hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

Det här avsnittet visar hur du skapar och kör ett jobb. Varje steg går igenom ett exempel som visar hur du kör ett jobb för kylda Varuautomat enheter som måste ha sina fläkthastighet som ökar.

1. Gå till jobb i navigeringsfönstret.

1. Välj **+ ny** för att kunna börja skapa ett nytt jobb.

    ![Skapa nytt jobb](./media/howto-run-a-job-experimental/createnewjob.png)

1. Ange ett namn och beskrivning som hjälper dig att identifiera jobbet som du skapar.

1. Markera de enheter du vill att jobbet ska tillämpas på. När du markerar enheten visas till höger fylla med enheter inom den valda enheten. Om du väljer en bruten enhetsuppsättning inga enheter visas och du ser ett meddelande som förklarar att din enhet är bruten.

1. Välj sedan typ av jobb som ska definieras (en inställning, egenskapen eller kommando). Välj **+** markerat vid vilken typ av jobb och lägga till din önskade åtgärder.

    ![Konfigurera jobb](./media/howto-run-a-job-experimental/configurejob.png)

1. Till höger, välja de enheter som du vill köra jobbet på. Genom att välja den översta kryssrutan markeras alla enheter i uppsättningen hela enheten. Genom att välja kryssrutan nära namn markeras alla enheter på den aktuella sidan.

1. När dina önskade enheter har valts, Välj **kör**. Jobbet kommer att synas på din huvud **jobb** sidan. På den här vyn kan kan du se ditt pågående jobb och historiken för alla tidigare köra jobb. Jobbet körs visas alltid överst i listan.

    ![Visa jobb](./media/howto-run-a-job-experimental/viewjob.png)

    > [!NOTE]
    > Du kommer att kunna visa historiken för dina jobb som körts tidigare för upp till 30 dagar.

1. Välj namnet på det jobb som du vill visa listan för att få en översikt över ditt jobb. Den här översikten innehåller jobbinformation, enheter och enhetsstatus.

    ![Visa enhetsstatus](./media/howto-run-a-job-experimental/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>Stoppa ett jobb som körs

Om du vill stoppa ett jobb som körs för tillfället väljer du namnet på det pågående jobbet som du vill stoppa. Välj den **stoppa** på panelen. Visas jobbets status har ändrats för att återspegla att jobbet har stoppats.

   ![Stoppa jobb](./media/howto-run-a-job-experimental/stopjob.png)

### <a name="run-a-stopped-job"></a>Kör ett stoppat jobb

Välj namnet på det stoppat jobb som du vill köra om du vill köra ett jobb som har för närvarande stoppats. Välj den **kör** på panelen. Du ser att jobbet har statusen har ändrats för att återspegla att jobbet körs nu igen.

   ![Återupptagna jobb](./media/howto-run-a-job-experimental/resumejob.png)

## <a name="view-the-job-status"></a>Visa jobbets status

När ett jobb har skapats kan den **Status** kolumn uppdateras med det senaste statusmeddelandet för jobbet. Statusmeddelanden som innebär följande:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har körts på alla enheter.              |
| Misslyckad               | Det här jobbet har misslyckades och inte helt körs på enheter.  |
| Väntande åtgärder              | Det här jobbet har ännu inte börjat köra på enheter.        |
| Körs              | Det här jobbet körs för tillfället på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Statusmeddelanden som följs av en översikt över enheter i jobbet. Dessa Enhetsstatus innebär följande:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har kört på.  |
| Misslyckad               | Antalet enheter som jobbet inte kunde köras på.      |

### <a name="view-the-device-status"></a>Visa enhetens status

Välj Jobbnamnet för att kunna visa statusen för varje enhet i jobbet. Här visas information om jobbet och alla enheter som var en del av detta jobb. Bredvid varje enhetsnamn visas något av följande statusmeddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutfört            | Jobbet har körts på den här enheten.                                     |
| Misslyckad               | Jobbet har inte kunnat köra på den här enheten. Det felmeddelande som visas visas mer information.  |
| Väntande åtgärder              | Jobbet har ännu inte körts på den här enheten.                                  |

> [!NOTE]
> Om en enhet har tagits bort, du kan inte välja enhet och visas som tas bort med enhets-ID.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att skapa jobb i Azure IoT Central programmet, är här några nästa steg:

- [Använda enhetsuppsättningar](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Hantera dina enheter](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
- [Version mallen för enhet](howto-version-devicetemplate.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
