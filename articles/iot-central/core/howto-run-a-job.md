---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft Docs
description: Med Azure IoT Central-jobb kan du hantera funktioner för hantering av enheter, till exempel uppdatera egenskaper eller köra ett kommando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609750"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i skala med hjälp av jobb. Med jobb kan du göra Mass uppdateringar av enhets egenskaper och köra kommandon. Den här artikeln visar hur du kommer igång med jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb. Det visar hur du ställer in det ljusa tröskelvärdet för en grupp logistiska gateway-enheter.

1. Navigera till **jobb** från det vänstra fönstret.

2. Välj **+ nytt** för att skapa ett nytt jobb:

    ![Skapa nytt jobb](./media/howto-run-a-job/create-new-job.png)

3. Ange ett namn och en beskrivning för att identifiera det jobb som du skapar.

4. Välj den mål enhets grupp som du vill att jobbet ska gälla för. Du kan se hur många enheter din jobb konfiguration gäller i avsnittet **Sammanfattning** .

5. Välj sedan antingen **moln egenskap**, **egenskap**eller **kommando** som den typ av jobb som ska konfigureras. Om du vill konfigurera en konfiguration av en **egenskaps** jobb väljer du en egenskap och anger dess nya värde. Om du vill konfigurera ett **kommando**väljer du kommandot som ska köras. Ett egenskaps jobb kan ange flera egenskaper:

    ![Konfigurera jobb](./media/howto-run-a-job/configure-job.png)

6. När du har skapat jobbet väljer du **Kör** eller **Spara**. Jobbet visas nu på sidan med huvud **jobb** . På den här sidan kan du se jobbet som körs och historiken för tidigare körningar eller sparade jobb. Du kan öppna ditt sparade jobb igen när som helst för att fortsätta redigera det eller köra det:

    ![Visa jobb](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Du kan visa 30 dagars historik för dina jobb som körs tidigare.

7. Om du vill få en översikt över jobbet väljer du det jobb som ska visas i listan. Den här översikten innehåller värdena för jobb information, enheter och enhets status. I den här översikten kan du också välja **Hämta jobb information** för att ladda ned en CSV-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning:

    ![Visa enhetsstatus](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Hantera jobb

Om du vill stoppa ett pågående jobb öppnar du det och väljer **stoppa**. Jobbets status ändras för att visa att jobbet stoppas. I **sammanfattnings** avsnittet visas vilka enheter som har slutförts, misslyckats eller som fortfarande väntar.

Om du vill köra ett jobb som är stoppat markerar du det och väljer sedan **Kör**. Jobbets status ändras för att återspegla jobbet körs nu igen. Avsnittet **Sammanfattning** fortsätter att uppdateras med den senaste förloppet.

![Hantera jobb](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb väljer du det på sidan **jobb** och väljer **Kopiera**. En kopia av jobb konfigurationen öppnas och du kan redigera den och **Kopiera** den till jobb namnet. Du kan spara eller köra det nya jobbet:

![Kopiera jobb](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Visa jobbstatus

När ett jobb har skapats uppdateras **status** kolumnen med det senaste status meddelandet för jobbet. I följande tabell visas möjliga status värden:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutförd            | Det här jobbet har körts på alla enheter.              |
| Misslyckades               | Det här jobbet har misslyckats och inte körts fullständigt på enheterna.  |
| Väntar              | Det här jobbet har ännu inte börjat köra på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | Det här jobbet har stoppats manuellt av en användare.           |

Status meddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga enhets status värden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckades               | Antalet enheter som jobbet inte kunde köras på.       |

### <a name="view-the-device-status-values"></a>Visa enhets status värden

Om du vill visa status för jobbet och alla berörda enheter öppnar du jobbet. Bredvid varje enhets namn ser du något av följande status meddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutförd            | Jobbet som körs på den här enheten.                                     |
| Misslyckades               | Det gick inte att köra jobbet på den här enheten. I fel meddelandet visas mer information.  |
| Väntar              | Jobbet har ännu inte körts på den här enheten.                                   |

Om du vill ladda ned en CSV-fil som innehåller jobb information och listan över enheter och deras status värden väljer du **Hämta**.

### <a name="filter-the-list-of-devices"></a>Filtrera listan över enheter

Du kan filtrera enhets listan på jobb informations sidan genom att välja filter ikonen. Du kan filtrera efter **enhets-ID** eller **status** fälten:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrera enhetslistan":::

### <a name="customize-columns-in-the-device-list"></a>Anpassa kolumner i enhets listan

Du kan välja ytterligare kolumner som ska visas i enhets listan genom att välja ikonen för kolumn alternativ:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Kolumn alternativ":::

Du ser en dialog ruta där du kan välja vilka kolumner som ska visas i enhets listan. Markera de kolumner som du vill visa, Välj högerpilen och välj **OK**. Om du vill markera alla tillgängliga kolumner markerar du **Markera alla**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Dialog rutan kolumn väljare":::

De markerade kolumnerna visas i enhets listan:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Välja kolumner":::

De markerade kolumnerna är beständiga i en användarsession eller mellan användarsessioner som har åtkomst till programmet.

## <a name="rerun-jobs"></a>Kör om jobb

Du kan köra om ett jobb som har fel enheter. Välj **Kör**om:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Kör ett jobb igen":::

Ange ett jobb namn och en beskrivning och välj sedan **Kör om jobb**. Ett nytt jobb har skickats för att försöka utföra åtgärden på felaktiga enheter:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Kör om misslyckade enheter":::

> [!NOTE]
> Du kan inte köra fler än fem jobb samtidigt från ett IoT Central-program.

> [!NOTE]
> När ett jobb är klart och du tar bort en enhet som finns i jobbets enhets lista visas enhets posten som borttagen i länken enhets namn och enhets information inte tillgänglig för den borttagna enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
