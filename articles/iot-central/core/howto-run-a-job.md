---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft Docs
description: Azure IoT Central-jobb möjliggör hantering av enhets hanterings funktioner, till exempel uppdatering av enhets egenskaper, inställningar eller körning av ett kommando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 68d4dbff364f8d3fda72fc2377722031e9cccc3d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018898"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i skala med hjälp av jobb. Med jobb kan du göra Mass uppdateringar av enhets egenskaper och-kommandon. Den här artikeln vägleder dig genom hur du kommer igång med jobb i ditt eget program.


## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb. Det visar hur du ökar fläkt hastigheten för flera kylda Vending-datorer.

1. Navigera till jobb från navigerings fönstret.

2. Välj **+ nytt** för att skapa ett nytt jobb.

    ![Skapa nytt jobb](./media/howto-run-a-job/createnewjob.png)

3. Ange ett namn och en beskrivning för att identifiera det jobb som du skapar.

4. Välj den enhets grupp som du vill att jobbet ska gälla för. Du kan se hur många enheter din jobb konfiguration ska tillämpas på i avsnittet Sammanfattning. 

5. Välj sedan den typ av jobb som du vill definiera (egenskap eller kommando). Konfigurera jobb konfigurationen genom att välja egenskapen och ange nya värden eller Välj ett kommando. Det går att lägga till flera egenskaper i taget.

    ![Konfigurera jobb](./media/howto-run-a-job/configurejob.png)

6. På den högra sidan väljer du de enheter som du vill köra jobbet på. Genom att markera kryss rutan överst markeras alla enheter i hela enhets uppsättningen. Genom att markera kryss rutan nära **namn**, markeras alla enheter på den aktuella sidan.

7. När du har valt enheter väljer du **Kör** eller **Spara**. Jobbet visas nu på sidan med huvud **jobb** . I den här vyn kan du se jobbet som körs och historiken för tidigare körnings jobb. Jobbet som körs visas alltid överst i listan. Du kan öppna ditt sparade jobb igen när som helst för att fortsätta redigera eller köra.

    ![Visa jobb](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Du kan visa historiken för dina tidigare körnings jobb i upp till 30 dagar.

7. Om du vill få en översikt över jobbet väljer du det jobb som ska visas i listan. Den här översikten innehåller värdena för jobb information, enheter och enhets status. I den här översikten kan du också välja **Hämta jobb information** för att ladda ned en. csv-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning.

    ![Visa enhetsstatus](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Stoppa ett pågående jobb

Om du vill stoppa ett pågående jobb markerar du det och väljer **stoppa**. Jobbets status ändras för att visa att jobbet stoppas.

   ![Stoppa jobb](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Köra ett stoppat jobb

Om du vill köra ett jobb som är stoppat väljer du det stoppade jobbet. Välj **Kör** i panelen. Jobbets status ändras för att återspegla jobbet körs nu igen.

   ![Återupptog jobb](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb som du har skapat öppnar du ett jobb som har skapats och väljer **Kopiera**. En ny kopia av jobb konfigurationen öppnas så att du kan redigera den. Du kan spara eller köra det nya jobbet. 

   ![Kopiera jobb](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visa jobb status

När ett jobb har skapats uppdateras **status** kolumnen med det senaste status meddelandet för jobbet. I följande tabell visas möjliga status värden:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har körts på alla enheter.              |
| Misslyckad               | Det här jobbet har misslyckats och inte körts fullständigt på enheterna.  |
| Väntande åtgärder              | Det här jobbet har ännu inte börjat köra på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Status meddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga enhets status värden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckad               | Antalet enheter som jobbet inte kunde köras på.       |

### <a name="view-the-device-status"></a>Visa enhetens status

Om du vill visa status för jobbet och alla berörda enheter väljer du jobbet. Om du vill hämta en. csv-fil som innehåller jobb information, inklusive listan över enheter och deras status värden, väljer du **Hämta jobb information**. Bredvid varje enhets namn ser du något av följande status meddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutfört            | Jobbet har körts på den här enheten.                                     |
| Misslyckad               | Det gick inte att köra jobbet på den här enheten. I fel meddelandet visas mer information.  |
| Väntande åtgärder              | Jobbet har ännu inte körts på den här enheten.                                   |

> [!NOTE]
> Om en enhet har tagits bort kan du inte välja enheten och den visas som borttagen med enhets-ID: t.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
