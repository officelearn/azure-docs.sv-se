---
title: Köra bakgrundsuppgifter med WebJobs
description: Lär dig hur du använder WebJobs för att köra bakgrundsuppgifter i Azure App Service. Välj bland en mängd olika skriptformat och kör dem med CRON-uttryck.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 4c568c95a5dbc1799a765c95a2b224de53dfbe9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279148"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Köra bakgrundsuppgifter med WebJobs i Azure App Service

Den här artikeln visar hur du distribuerar WebJobs med hjälp av [Azure-portalen](https://portal.azure.com) för att ladda upp en körbar eller skript. Information om hur du utvecklar och distribuerar WebJobs med Visual Studio finns i [Distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Översikt
WebJobs är en funktion i [Azure App Service](index.yml) som gör att du kan köra ett program eller skript i samma sammanhang som en webbapp, API-app eller mobilapp. Det finns ingen extra kostnad för att använda WebJobs.

> [!IMPORTANT]
> WebJobs stöds ännu inte för App Service på Linux.

Azure WebJobs SDK kan användas med WebJobs för att förenkla många programmeringsuppgifter. Mer information finns i [Vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions är ett annat sätt att köra program och skript. En jämförelse mellan WebJobs och Functions finns i [Välj mellan flöde, logikappar, funktioner och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Webbjob-typer

I följande tabell beskrivs skillnaderna mellan *kontinuerliga* och *utlösta* WebJobs.


|Kontinuerlig igenkänning  |Utlöste  |
|---------|---------|
| Startar omedelbart när WebJob skapas. För att förhindra att jobbet upphör, gör programmet eller skriptet vanligtvis sitt arbete i en oändlig loop. Om jobbet avslutas kan du starta om det. | Startar endast när utlöses manuellt eller enligt ett schema. |
| Körs på alla instanser som webbappen körs på. Du kan också begränsa WebJob till en enda instans. |Körs på en enda instans som Azure väljer för belastningsutjämning.|
| Stöder fjärrfelsökning. | Stöder inte fjärrfelsökning.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Filtyper som stöds för skript eller program

Följande filtyper stöds:

* .cmd, .bat, .exe (med Windows cmd)
* .ps1 (med PowerShell)
* .sh (med Bash)
* .php (med PHP)
* .py (med Python)
* .js (med Node.js)
* .jar (med Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>Skapa ett kontinuerligt WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Gå till sidan App **Service** i apptjänsten i apptjänstens webbapp, API-app eller mobilapp i Azure-portalen. [Azure portal](https://portal.azure.com)

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

    ![WebJob-sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd inställningarna **för Lägg till Webbjob** enligt tabellen.

   ![Lägg till webjob-sida](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myContinuousWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller ett tal och får inte innehålla andra specialtecken än "-" och "_". |
   | **Ladda upp filer** | ConsoleApp.zip | En *ZIP-fil* som innehåller din körbara fil eller skriptfil samt alla stödfiler som behövs för att köra programmet eller skriptet. De körbara eller skriptfilstyper som stöds visas i avsnittet [Filtyper som stöds.](#acceptablefiles) |
   | **Typ** | Kontinuerlig igenkänning | [Typerna webjob](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Skala** | Flera instanser | Endast tillgängligt för kontinuerliga webbjobs. Avgör om programmet eller skriptet körs på alla instanser eller bara en instans. Alternativet att köras på flera instanser gäller inte för nivåerna För fri eller delad [prissättning](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Klicka på **OK**.

   Det nya Webbjobbet visas på **webjobs-sidan.**

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Om du vill stoppa eller starta om ett kontinuerligt WebJob högerklickar du på WebJob i listan och klickar på **Stopp** eller **Start**.

    ![Stoppa ett kontinuerligt WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>Skapa ett manuellt utlöst WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Gå till sidan App **Service** i apptjänsten i apptjänstens webbapp, API-app eller mobilapp i Azure-portalen. [Azure portal](https://portal.azure.com)

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

    ![WebJob-sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd inställningarna **för Lägg till Webbjob** enligt tabellen.

   ![Lägg till webjob-sida](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myTriggeredWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller ett tal och får inte innehålla andra specialtecken än "-" och "_".|
   | **Ladda upp filer** | ConsoleApp.zip | En *ZIP-fil* som innehåller din körbara fil eller skriptfil samt alla stödfiler som behövs för att köra programmet eller skriptet. De körbara eller skriptfilstyper som stöds visas i avsnittet [Filtyper som stöds.](#acceptablefiles) |
   | **Typ** | Utlöste | [Typerna webjob](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Manuell | |

4. Klicka på **OK**.

   Det nya Webbjobbet visas på **webjobs-sidan.**

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Om du vill köra WebJob högerklickar du på dess namn i listan och klickar på **Kör**.
   
    ![Kör WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>Skapa ett schemalagt WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Gå till sidan App **Service** i apptjänsten i apptjänstens webbapp, API-app eller mobilapp i Azure-portalen. [Azure portal](https://portal.azure.com)

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. På sidan **WebJobs** väljer du **Lägg till**.

   ![WebJob-sida](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd inställningarna **för Lägg till Webbjob** enligt tabellen.

   ![Lägg till webjob-sida](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myScheduledWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller ett tal och får inte innehålla andra specialtecken än "-" och "_". |
   | **Ladda upp filer** | ConsoleApp.zip | En *ZIP-fil* som innehåller din körbara fil eller skriptfil samt alla stödfiler som behövs för att köra programmet eller skriptet. De körbara eller skriptfilstyper som stöds visas i avsnittet [Filtyper som stöds.](#acceptablefiles) |
   | **Typ** | Utlöste | [Typerna webjob](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Utlösare** | Schemalagd | Aktivera funktionen Alltid på för att schemaläggningen ska fungera tillförlitligt. Always On är endast tillgängligt på prisnivåerna Basic, Standard och Premium.|
   | **CRON-uttryck** | 0 0/20 * * * * | [CRON-uttryck beskrivs](#ncrontab-expressions) i följande avsnitt. |

4. Klicka på **OK**.

   Det nya Webbjobbet visas på **webjobs-sidan.**

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB-uttryck

Du kan ange ett [NCRONTAB-uttryck](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) `settings.job` i portalen eller inkludera en fil i roten på filen WebJob *.zip,* som i följande exempel:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Mer information finns i [Schemalägga ett utlöst WebJob](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>Visa jobbhistoriken

1. Markera det WebJob som du vill visa historik för och välj sedan knappen **Loggar.**
   
   ![Knappen Loggar](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. På sidan **WebJob-information** väljer du en tid för att se information för en körning.
   
   ![Information om WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. På sidan **Webbjob kör detaljer** väljer du **Växla utdata** för att se texten i logginnehållet.
   
    ![Information om webbjobbkörning](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Om du vill visa utdatatexten i ett separat webbläsarfönster väljer du **hämta**. Om du vill hämta själva texten högerklickar du på **hämtningen** och använder webbläsaralternativen för att spara filinnehållet.
   
5. Markera länken **WebJobs-brödsmulor** högst upp på sidan för att gå till en lista över WebJobs.

    ![WebJob ströbröd](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista över WebJobs i historikinstrumentpanelen](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Nästa steg

Azure WebJobs SDK kan användas med WebJobs för att förenkla många programmeringsuppgifter. Mer information finns i [Vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
