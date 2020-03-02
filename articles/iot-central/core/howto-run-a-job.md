---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft Docs
description: Med Azure IoT Central-jobb kan du hantera funktioner för hantering av enheter, till exempel uppdatera egenskaper eller köra ett kommando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206844"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i skala med hjälp av jobb. Med jobb kan du göra Mass uppdateringar av enhets egenskaper och köra kommandon. Den här artikeln visar hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb. Det visar hur du ställer in det ljusa tröskelvärdet för en grupp logistiska gateway-enheter.

1. Navigera till **jobb** från det vänstra fönstret.

2. Välj **+ nytt** för att skapa ett nytt jobb:

    > [!div class="mx-imgBorder"]
    > ![skapa nytt jobb](./media/howto-run-a-job/createnewjob.png)

3. Ange ett namn och en beskrivning för att identifiera det jobb som du skapar.

4. Välj den mål enhets grupp som du vill att jobbet ska gälla för. Du kan se hur många enheter din jobb konfiguration gäller i avsnittet **Sammanfattning** .

5. Välj sedan antingen **Property** eller **Command** som den typ av jobb som ska konfigureras. Om du vill konfigurera en konfiguration av en **egenskaps** jobb väljer du en egenskap och anger dess nya värde. Om du vill konfigurera ett **kommando**eller väljer du kommandot som ska köras. Ett egenskaps jobb kan ange flera egenskaper:

    > [!div class="mx-imgBorder"]
    > ![konfigurera jobb](./media/howto-run-a-job/configurejob.png)

6. När du har valt enheter väljer du **Kör** eller **Spara**. Jobbet visas nu på sidan med huvud **jobb** . På den här sidan kan du se jobbet som körs och historiken för tidigare körningar eller sparade jobb. Du kan öppna ditt sparade jobb igen när som helst för att fortsätta redigera det eller köra det:

    > [!div class="mx-imgBorder"]
    > ![Visa jobb](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Du kan visa 30 dagars historik för dina jobb som körs tidigare.

7. Om du vill få en översikt över jobbet väljer du det jobb som ska visas i listan. Den här översikten innehåller värdena för jobb information, enheter och enhets status. I den här översikten kan du också välja **Hämta jobb information** för att ladda ned en CSV-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning:

    > [!div class="mx-imgBorder"]
    > ![Visa enhets status](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Stoppa ett pågående jobb

Om du vill stoppa ett pågående jobb öppnar du det och väljer **stoppa**. Jobbets status ändras för att visa att jobbet stoppas. I **sammanfattnings** avsnittet visas vilka enheter som har slutförts, misslyckats eller som fortfarande väntar:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Köra ett stoppat jobb

Om du vill köra ett jobb som är stoppat markerar du det och väljer sedan **Kör**. Jobbets status ändras för att återspegla jobbet körs nu igen. Avsnittet **Sammanfattning** fortsätter att uppdateras med den senaste förloppet:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Kopiera ett av dina befintliga jobb genom att öppna det och välja **Kopiera**. En kopia av jobb konfigurationen öppnas så att du kan redigera den, och **kopian** läggs till i slutet av jobb namnet. Du kan spara eller köra det nya jobbet:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visa jobb status

När ett jobb har skapats uppdateras **status** kolumnen med det senaste status meddelandet för jobbet. I följande tabell visas möjliga status värden:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har körts på alla enheter.              |
| Misslyckades               | Det här jobbet har misslyckats och inte körts fullständigt på enheterna.  |
| Väntande åtgärder              | Det här jobbet har ännu inte börjat köra på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Status meddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga enhets status värden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckades               | Antalet enheter som jobbet inte kunde köras på.       |

### <a name="view-the-device-status"></a>Visa enhetens status

Om du vill visa status för jobbet och alla berörda enheter väljer du jobbet. Om du vill ladda ned en CSV-fil som innehåller jobb information, inklusive listan över enheter och deras status värden, väljer du **Hämta jobb information**. Bredvid varje enhets namn ser du något av följande status meddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutfört            | Jobbet har körts på den här enheten.                                     |
| Misslyckades               | Det gick inte att köra jobbet på den här enheten. I fel meddelandet visas mer information.  |
| Väntande åtgärder              | Jobbet har ännu inte körts på den här enheten.                                   |

> [!NOTE]
> Om en enhet har tagits bort kan du inte välja enheten. Den visas som borttagen med enhets-ID: t.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
