---
title: Skapa och köra jobb i ditt program med Azure IoT Central | Microsoft Docs
description: Azure IoT Central-jobb gör det möjligt att bulk hanteringsfunktioner för enheter, till exempel uppdaterar en enhetsegenskap, inställning eller ett kommando har körts.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199859"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i din Azure IoT Central-App

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i stor skala med jobb. Jobb låter dig massuppdateringar till enhetsegenskaper, inställningar och kommandon. Den här artikeln vägleder dig igenom hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

Det här avsnittet visar hur du skapar och kör ett jobb. Den visar hur du ökar fläkthastighet för flera kylda varuautomater.

1. Gå till jobb i navigeringsfönstret.

1. Välj **+ ny** att skapa ett nytt jobb.

    ![Skapa nytt jobb](./media/howto-run-a-job/createnewjob.png)

1. Ange ett namn och beskrivning för att identifiera jobbet som du skapar.

1. Markera de enheter du vill att jobbet ska tillämpas på. När du markerar enheten visas till höger fylla med enheter i enhetsuppsättningen. Om du väljer en bruten enhetsuppsättning inga enheter visas och du ser ett meddelande om att din enhet är bruten.

1. Välj sedan typ av jobb för att definiera (en inställning, egenskapen eller kommandot). Välj **+** markerat vid vilken typ av jobb och lägger till din verksamhet.

    ![Konfigurera jobb](./media/howto-run-a-job/configurejob.png)

1. Markera de enheter som du vill köra jobbet på till höger. Genom att välja den översta kryssrutan markeras alla enheter i uppsättningen hela enheten. Genom att välja kryssrutan nära **namn**, alla enheter på den aktuella sidan har valts.

1. När du har valt dina enheter, Välj **kör** eller **spara**. Jobbet nu visas på din huvud **jobb** sidan. På den här vyn visas ditt pågående jobb och historik över eventuella tidigare köra jobb. Jobbet körs visas alltid överst i listan. Sparade jobbet kan öppnas igen när som helst att fortsätta att redigera eller köra.

    ![Visa jobb](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Du kan visa historiken för dina jobb som körts tidigare i upp till 30 dagar.

1. Välj jobbet att visa i listan om du vill få en översikt över ditt jobb. Den här översikten innehåller jobbinformation, enheter och statusvärden för enheten. Den här översikten, du kan också välja **hämta jobbinformation** att hämta en CSV-fil av Jobbdetaljer om, inklusive enheterna och deras statusvärden. Den här informationen kan vara användbar vid felsökning.

    ![Visa enhetsstatus](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Stoppa ett jobb som körs

Stoppa ett jobb som körs genom att markera den och välj **stoppa** på panelen. Jobbet status ändras så att jobbet har stoppats.

   ![Stoppa jobb](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Kör ett stoppat jobb

Välj det stoppat jobbet för att köra ett jobb som har för närvarande stoppats. Välj **kör** på panelen. Ändras statusen för jobbet att återspegla jobbet körs nu igen.

   ![Återupptagna jobb](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb som du har skapat, markerar du det från sidan huvudsakliga jobb och välj **kopiera**. En ny kopia av jobbkonfigurationen öppnas där du kan redigera. Du kan spara eller köra det nya projektet. Om några ändringar har gjorts till din valda enhetsuppsättning, är de visas i kopierade jobbet som du kan redigera.

   ![Kopieringsjobb](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visa jobbets status

När ett jobb har skapats kan den **Status** kolumnen uppdateringar med det senaste statusmeddelandet för jobbet. I följande tabell visas möjliga statusvärden:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har körts på alla enheter.              |
| Misslyckad               | Det här jobbet har misslyckades och inte helt körs på enheter.  |
| Väntande åtgärder              | Det här jobbet har inte ännu har startat körs på enheter.         |
| Körs              | Det här jobbet körs för tillfället på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Statusmeddelanden som följs av en översikt över enheter i jobbet. I följande tabell visas möjliga enheten statusvärden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har gjorts på.       |
| Misslyckad               | Antalet enheter som misslyckats jobbet ska köras på.       |

### <a name="view-the-device-status"></a>Visa enhetens status

Välj jobbet för att visa status för jobbet och alla de berörda enheterna. Om du vill hämta en CSV-fil som innehåller jobbinformation, inklusive listan över enheter och deras statusvärden, Välj **hämta jobbinformation**. Du ser något av följande status visas bredvid varje enhetsnamn:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutfört            | Jobbet har körts på den här enheten.                                     |
| Misslyckad               | Jobbet har inte kunnat köra på den här enheten. Ett felmeddelande visas mer information.  |
| Väntande åtgärder              | Jobbet har inte ännu körs på den här enheten.                                   |

> [!NOTE]
> Om en enhet har tagits bort, du kan inte välja enheten och visar tagits bort med enhets-ID.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i Azure IoT Central programmet, är här några nästa steg:

- [Använda enhetsuppsättningar](howto-use-device-sets.md)
- [Hantera dina enheter](howto-manage-devices.md)
- [Version mallen för enhet](howto-version-devicetemplate.md)
