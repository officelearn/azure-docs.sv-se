---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft Docs
description: Azure IoT Central-jobb tillåter hantering av enhets hanterings funktioner, till exempel uppdaterings egenskaper eller körning av ett kommando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797844"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Microsoft Azure IoT Central för att hantera dina anslutna enheter i stor skala genom att använda jobb. Med jobb kan du göra Mass uppdateringar av enhets egenskaper och köra kommandon. Den här artikeln visar hur du kommer igång med att använda jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I det här avsnittet visas hur du skapar och kör ett jobb i form av inställning av ett ljus tröskelvärde för en grupp logistiska gateway-enheter.

1. I det vänstra fönstret väljer du **jobb**.

2. Välj **+ Ny**.

   ![Skärm bild som visar alternativ för att skapa ett jobb.](./media/howto-run-a-job/create-new-job.png)

3. Ange ett namn och en beskrivning för att identifiera det jobb som du skapar.

4. Välj den mål enhets grupp som du vill att jobbet ska gälla för. Du kan se hur många enheter din jobb konfiguration gäller i avsnittet **Sammanfattning** .

5. Välj **moln egenskap**, **egenskap**eller **kommando** som den typ av jobb som ska konfigureras. 

   Om du vill konfigurera en konfiguration av en **egenskaps** jobb väljer du en egenskap och anger dess nya värde. Om du vill konfigurera en **kommando** jobbs konfiguration väljer du kommandot som ska köras. Ett egenskaps jobb kan ange flera egenskaper.

   ![Skärm bild som visar val för att skapa ett egenskaps jobb med namnet set Light Threshold.](./media/howto-run-a-job/configure-job.png)

6. Välj **Kör** eller **Spara**. Jobbet visas nu på sidan med huvud **jobb** . På den här sidan kan du se jobbet som körs och historiken för tidigare körningar eller sparade jobb. Du kan öppna det sparade jobbet när som helst och fortsätta redigera det eller köra det.

   ![Skärm bild som visar namn, status och beskrivning för ett skapat jobb.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Du kan visa 30 dagars historik för dina jobb som körs tidigare.

7. Välj det sparade jobbet och kör det genom att klicka på knappen **Kör** . 

   Dialog rutan **Kör ditt jobb?** visas. Bekräfta genom att välja knappen **Kör jobb** . 

   ![Skärm bild av dialog rutan som bekräftar att du vill köra ett jobb.](./media/howto-run-a-job/run-job.png)

8. Ett jobb genomgår fasen väntar, körs och slutförs. Informationen om jobb körningen innehåller resultat statistik, varaktighets information och ett rutnät i enhets listan. 

   I den här översikten kan du också välja **resultat logg** för att ladda ned en CSV-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning.

   ![Skärm bild som visar enhets status.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Hantera jobb

Om du vill stoppa ett pågående jobb öppnar du det och väljer **stoppa**. Jobbets status ändras för att visa att jobbet har stoppats. I **sammanfattnings** avsnittet visas vilka enheter som har slutförts, som har misslyckats eller som fortfarande väntar.

![Skärm bild som visar ett jobb som körs och knappen för att stoppa ett jobb.](./media/howto-run-a-job/manage-job.png)

När ett jobb har stoppats kan du välja **Fortsätt** för att återuppta körningen av jobbet. Jobbets status ändras för att visa att jobbet nu körs igen. **Sammanfattnings** avsnittet fortsätter att uppdateras med den senaste förloppet.

![Skärm bild som visar ett stoppat jobb och en knapp för att fortsätta ett jobb.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb väljer du det på sidan **jobb** och väljer **jobb information**. Sidan **jobb information** visas. 

![Skärm bild som visar sidan för jobb information.](./media/howto-run-a-job/job-details.png)

Välj **Kopiera**.

![Skärm bild som visar knappen Kopiera.](./media/howto-run-a-job/job-details-copy.png)

En kopia av jobb konfigurationen öppnas och du kan redigera den och **Kopiera** den till jobb namnet. Du kan spara eller köra det nya jobbet.

![Skärm bild som visar en kopia av jobb konfigurationen.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Visa jobbstatus

När ett jobb har skapats uppdateras kolumnen **status** med det senaste status meddelandet för jobbet. I följande tabell visas de möjliga jobb status värdena:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutförd            | Det här jobbet kördes på alla enheter.              |
| Misslyckad               | Jobbet kunde inte köras och kunde inte köras fullständigt på enheterna.  |
| Väntar              | Det här jobbet har ännu inte börjat köras på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | En användare har stoppat det här jobbet manuellt.           |

Status meddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga enhets status värden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckad               | Antalet enheter som jobbet inte kunde köras på.       |

Om du vill visa status för jobbet och alla berörda enheter öppnar du jobbet. Bredvid varje enhets namn ser du något av följande status meddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutförd            | Jobbet kördes på den här enheten.                                     |
| Misslyckad               | Det gick inte att köra jobbet på den här enheten. I fel meddelandet visas mer information.  |
| Väntar              | Jobbet har ännu inte körts på den här enheten.                                   |

Om du vill ladda ned en CSV-fil som innehåller jobb information och listan över enheter och deras status värden väljer du **Hämta**.

## <a name="filter-the-device-list"></a>Filtrera enhetslistan

Du kan filtrera enhets listan på **jobb informations** sidan genom att välja filter ikonen. Du kan filtrera i fältet **enhets-ID** eller **status** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Skärm bild som visar val för att filtrera en enhets lista.":::

## <a name="customize-columns-in-the-device-list"></a>Anpassa kolumner i enhets listan

Du kan välja ytterligare kolumner som ska visas i enhets listan genom att välja ikonen för kolumn alternativ.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Skärm bild som visar ikonen för kolumn alternativ.":::

I en dialog ruta kan du välja vilka kolumner som ska visas i enhets listan. Markera de kolumner som du vill visa, Välj högerpilen och välj sedan **OK**. Om du vill markera alla tillgängliga kolumner markerar du **Markera alla**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Skärm bild som visar dialog rutan för att välja kolumner som ska visas.":::

De markerade kolumnerna visas i enhets listan.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Skärm bild som visar markerade kolumner i enhets listan.":::

De markerade kolumnerna är beständiga i en användarsession eller mellan användarsessioner som har åtkomst till programmet.

## <a name="rerun-jobs"></a>Kör om jobb

Du kan köra om ett jobb som har fel enheter. Select **Kör igen misslyckades**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Skärm bild som visar knappen för att köra om ett jobb på misslyckade enheter.":::

Ange ett jobb namn och en beskrivning och välj sedan **Kör om jobb**. Ett nytt jobb skickas för att försöka utföra åtgärden på misslyckade enheter igen.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Skärm bild som visar dialog rutan för återkörning av misslyckade enheter.":::

> [!NOTE]
> Du kan inte köra fler än fem jobb samtidigt från ett Azure IoT Central-program.
>
> När ett jobb är klart och du tar bort en enhet som finns i jobbets enhets lista, visas enhets posten som borttagen i enhetens namn. Informations länken är inte tillgänglig för den borttagna enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
