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
ms.openlocfilehash: 298770b1b2da816ddef9154fafb20d7c6cb82df3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849044"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i skala med hjälp av jobb. Med jobb kan du göra Mass uppdateringar av enhets egenskaper, inställningar och kommandon. Den här artikeln vägleder dig genom hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb. Det visar hur du ökar fläkt hastigheten för flera kylda Vending-datorer.

1. Navigera till jobb från navigerings fönstret.

1. Välj **+ nytt** för att skapa ett nytt jobb.

    ![Skapa nytt jobb](./media/howto-run-a-job/createnewjob.png)

1. Ange ett namn och en beskrivning för att identifiera det jobb som du skapar.

1. Välj den enhets uppsättning som du vill att jobbet ska tillämpas på. När du har valt enhets uppsättningen ser du den högra sidan som du fyller i med enheterna i enhets uppsättningen. Om du väljer en bruten enhets uppsättning visas inga enheter och du ser ett meddelande om att din enhets uppsättning är bruten.

1. Välj sedan den typ av jobb som du vill definiera (en inställning, egenskap eller kommando). Välj **+** bredvid den typ av jobb som du har valt och Lägg till dina åtgärder.

    ![Konfigurera jobb](./media/howto-run-a-job/configurejob.png)

1. På den högra sidan väljer du de enheter som du vill köra jobbet på. Genom att markera kryss rutan överst markeras alla enheter i hela enhets uppsättningen. Genom att markera kryss rutan nära **namn**, markeras alla enheter på den aktuella sidan.

1. När du har valt enheter väljer du **Kör** eller **Spara**. Jobbet visas nu på sidan med huvud **jobb** . I den här vyn kan du se jobbet som körs och historiken för tidigare körnings jobb. Jobbet som körs visas alltid överst i listan. Du kan öppna ditt sparade jobb igen när som helst för att fortsätta redigera eller köra.

    ![Visa jobb](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Du kan visa historiken för dina tidigare körnings jobb i upp till 30 dagar.

1. Om du vill få en översikt över jobbet väljer du det jobb som ska visas i listan. Den här översikten innehåller värdena för jobb information, enheter och enhets status. I den här översikten kan du också välja **Hämta jobb information** för att ladda ned en. csv-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning.

    ![Visa enhetsstatus](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Stoppa ett pågående jobb

Om du vill stoppa ett pågående jobb markerar du det och väljer **stoppa** i panelen. Jobbets status ändras för att visa att jobbet stoppas.

   ![Stoppa jobb](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Köra ett stoppat jobb

Om du vill köra ett jobb som är stoppat väljer du det stoppade jobbet. Välj **Kör** i panelen. Jobbets status ändras för att återspegla jobbet körs nu igen.

   ![Återupptog jobb](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb som du har skapat väljer du det på sidan huvud jobb och väljer **Kopiera**. En ny kopia av jobb konfigurationen öppnas så att du kan redigera den. Du kan spara eller köra det nya jobbet. Om du har gjort ändringar i den valda enhets uppsättningen, återspeglas de i det kopierade jobbet så att du kan redigera det.

   ![Kopiera jobb](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Visa jobb status

När ett jobb har skapats uppdateras **status** kolumnen med det senaste status meddelandet för jobbet. I följande tabell visas möjliga status värden:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutfört            | Det här jobbet har körts på alla enheter.              |
| Misslyckad               | Det här jobbet har misslyckats och inte körts fullständigt på enheterna.  |
| Väntande              | Det här jobbet har ännu inte börjat köra på enheter.         |
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
| Väntande              | Jobbet har ännu inte körts på den här enheten.                                   |

> [!NOTE]
> Om en enhet har tagits bort kan du inte välja enheten och den visas som borttagen med enhets-ID: t.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Använda enhetsuppsättningar](howto-use-device-sets.md)
- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
