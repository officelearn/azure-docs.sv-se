---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft-dokument
description: Azure IoT Central jobb möjliggör massenhetshanteringsfunktioner, till exempel uppdatera egenskaper eller köra ett kommando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157763"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i stor skala med hjälp av jobb. Med jobb kan du göra massuppdateringar av enhetsegenskaper och köra kommandon. Den här artikeln visar hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb. Den visar hur du ställer in ljuströskeln för en grupp logistiska gatewayenheter.

1. Navigera till **Jobb** från den vänstra rutan.

2. Välj **+ Nytt** om du vill skapa ett nytt jobb:

    ![Skapa nytt jobb](./media/howto-run-a-job/createnewjob.png)

3. Ange ett namn och en beskrivning för att identifiera det jobb du skapar.

4. Välj den målgrupp som du vill att jobbet ska gälla för. Du kan se hur många enheter jobbkonfigurationen gäller för i avsnittet **Sammanfattning.**

5. Välj sedan antingen **Molnegenskap**, **Egenskap** eller **Kommando** som typ av jobb som ska konfigureras. Om du vill ställa in en **egenskapsjobbkonfiguration** väljer du en egenskap och anger dess nya värde. Om du vill konfigurera ett **kommando**väljer du kommandot som ska köras. Ett egenskapsjobb kan ange flera egenskaper:

    ![Konfigurera jobb](./media/howto-run-a-job/configurejob.png)

6. När du har skapat jobbet väljer du **Kör** eller **Spara**. Jobbet visas nu på huvudsidan **för jobb.** På den här sidan kan du se ditt jobb som körs och historiken för tidigare körnings- eller sparade jobb. Det sparade jobbet kan när som helst öppnas igen för att fortsätta redigera det eller köra det:

    ![Visa jobb](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Du kan visa upp 30 dagars historik för dina tidigare körningsjobb.

7. Om du vill få en översikt över ditt jobb väljer du det jobb som ska visas i listan. Den här översikten innehåller jobbinformation, enheter och enhetsstatusvärden. Från den här översikten kan du också välja **Hämta jobbinformation** för att hämta en CSV-fil med dina jobbuppgifter, inklusive enheterna och deras statusvärden. Den här informationen kan vara användbar vid felsökning:

    ![Visa enhetsstatus](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Hantera ett jobb

Om du vill stoppa ett av dina jobb öppnar du det och väljer **Stoppa**. Jobbstatusändringarna för att återspegla jobbet stoppas. Avsnittet **Sammanfattning** visar vilka enheter som har slutfört, misslyckats eller fortfarande väntar.

Om du vill köra ett jobb som för närvarande har stoppats markerar du det och väljer sedan **Kör**. Jobbstatusändringarna för att återspegla jobbet körs nu igen. **Avsnittet Sammanfattning** fortsätter att uppdateras med de senaste framstegen.

![Hantera jobb](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett av dina befintliga jobb markerar du det på sidan **Jobb** och väljer **Kopiera**. En kopia av jobbkonfigurationen öppnas så att du kan redigera och **Kopiera** läggs till i projektnamnet. Du kan spara eller köra det nya jobbet:

![Kopiera jobb](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visa jobbstatus

När ett jobb har skapats uppdateras kolumnen **Status** med det senaste statusmeddelandet för jobbet. I följande tabell visas möjliga statusvärden:

| Statusmeddelande       | Betydelse för status                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har utförts på alla enheter.              |
| Misslyckades               | Det här jobbet har misslyckats och inte helt utförts på enheter.  |
| Väntande åtgärder              | Det här jobbet har ännu inte börjat köras på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Statusmeddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga enhetsstatusvärden:

| Statusmeddelande       | Betydelse för status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckades               | Antalet enheter som jobbet inte har kunnat köras på.       |

### <a name="view-the-device-status"></a>Visa enhetsstatus

Om du vill visa status för jobbet och alla berörda enheter öppnar du jobbet. Om du vill hämta en CSV-fil som innehåller jobbinformationen, inklusive listan över enheter och deras statusvärden, väljer du **Hämta jobbinformation**. Bredvid varje enhetsnamn visas något av följande statusmeddelanden:

| Statusmeddelande       | Betydelse för status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutfört            | Jobbet har utförts på den här enheten.                                     |
| Misslyckades               | Jobbet misslyckades med att köras på den här enheten. Felmeddelandet visar mer information.  |
| Väntande åtgärder              | Jobbet har ännu inte utförts på den här enheten.                                   |

> [!NOTE]
> Om en enhet har tagits bort kan du inte välja enheten. Den visas som borttagen med enhets-ID.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program, här är några nästa steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version av enhetsmallen](howto-version-device-template.md)
