---
title: Kör bakgrundsuppgifter med WebJobs – Azure App Service
description: Lär dig hur du använder WebJobs för att köra bakgrundsåtgärder i Azure App Service web apps, API-appar eller mobila appar.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga;msangapu;david.ebbo;suwatch;pbatum;naren.soni;
ms.custom: seodec18
ms.openlocfilehash: 0f2053e978b7c890f4e175515ed54f69694950c6
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749927"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Kör bakgrundsuppgifter med WebJobs i Azure App Service

## <a name="overview"></a>Översikt
WebJobs är en funktion i [Azure App Service](https://docs.microsoft.com/azure/app-service/) som gör att du kan köra ett program eller skript i samma kontext som en webbapp, API-app eller mobila appar. Det kostar ingenting extra att använda WebJobs.

> [!IMPORTANT]
> WebJobs stöds inte ännu för App Service i Linux.

Den här artikeln visar hur du distribuerar WebJobs genom att använda den [Azure-portalen](https://portal.azure.com) att ladda upp en körbara filer eller skript. Information om hur du utvecklar och distribuerar WebJobs med hjälp av Visual Studio finns i [distribuera WebJobs med hjälp av Visual Studio](webjobs-dotnet-deploy-vs.md).

Azure WebJobs SDK kan användas med WebJobs till att förenkla många programmeringsspråk. Mer information finns i [vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions erbjuder ett annat sätt att köra program och skript. En jämförelse mellan WebJobs och funktioner i [Välj mellan Flow, Logic Apps, Functions och WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>WebJob-typer

I följande tabell beskrivs skillnaderna mellan *kontinuerlig* och *utlöses* WebJobs.


|Kontinuerlig igenkänning  |Utlöst  |
|---------|---------|
| Startar omedelbart när Webbjobbet har skapats. Om du vill behålla jobbet från slut, fungerar programmet eller skriptet vanligtvis på sitt arbete i en oändlig loop. Om jobbet slutar, kan du starta om den. | Startar endast när det utlöses manuellt eller enligt ett schema. |
| Körs på alla instanser som webbappen körs på. Du kan också begränsa Webbjobbet till en enda instans. |Körs på en enda instans väljer Azure för belastningsutjämning.|
| Stöd för fjärrfelsökning. | Inte stöd för fjärrfelsökning.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Filtyper som stöds för skript eller program

Följande filtyper stöds:

* .cmd, .bat, .exe (med Windows cmd)
* .ps1 (med PowerShell)
* .SH (med Bash)
* .php (med PHP)
* .PY (med hjälp av Python)
* .js (med Node.js)
* .JAR (med Java)

## <a name="CreateContinuous"></a> Skapa ett kontinuerligt Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com)går du till den **Apptjänst** sidor i din App Service-webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

    ![WebJob-sidan](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb sida](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myContinuousWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”. |
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt eventuella stödfiler som krävs för att köra program eller skript. Körbara filer eller skript filen typer som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnittet. |
   | **typ** | Kontinuerlig igenkänning | Den [WebJob typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **Skalning** | Multi-instans | Endast tillgängligt för kontinuerliga WebJobs. Anger om programmet eller skriptet körs på alla instanser eller bara en instans. Alternativet för att köras på flera instanser gäller inte för den kostnadsfria eller delade [prisnivåer](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Klicka på **OK**.

   Ny Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Om du vill stoppa eller starta om ett kontinuerligt Webbjobb, högerklicka på Webbjobbet i listan och klicka på **stoppa** eller **starta**.

    ![Stoppa ett kontinuerligt Webbjobb](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Skapa ett manuellt utlösta Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com)går du till den **Apptjänst** sidor i din App Service-webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

    ![WebJob-sidan](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb sida](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myTriggeredWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”.|
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt eventuella stödfiler som krävs för att köra program eller skript. Körbara filer eller skript filen typer som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnittet. |
   | **typ** | Utlöst | Den [WebJob typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **utlösare** | Manuell | |

4. Klicka på **OK**.

   Ny Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. För att köra Webbjobbet, högerklickar du på namnet i listan och klicka på **kör**.
   
    ![Kör webbjobb](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Skapa ett schemalagt Webbjobb

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. I den [Azure-portalen](https://portal.azure.com)går du till den **Apptjänst** sidor i din App Service-webbapp, API-app eller mobila appar.

2. Välj **WebJobs**.

   ![Välj WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. I den **WebJobs** väljer **Lägg till**.

   ![WebJob-sidan](./media/web-sites-create-web-jobs/wjblade.png)

3. Använd den **lägger till Webbjobb** inställningar som anges i tabellen.

   ![Lägg till Webbjobb sida](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Inställning      | Exempelvärde   | Beskrivning  |
   | ------------ | ----------------- | ------------ |
   | **Namn** | myScheduledWebJob | Ett namn som är unikt i en App Service-app. Måste börja med en bokstav eller en siffra och får inte innehålla specialtecken än ”-” och ”_”. |
   | **Ladda upp filen** | ConsoleApp.zip | En *.zip* -fil som innehåller filen körbara filer eller skript samt eventuella stödfiler som krävs för att köra program eller skript. Körbara filer eller skript filen typer som stöds finns i den [filtyper som stöds](#acceptablefiles) avsnittet. |
   | **typ** | Utlöst | Den [WebJob typer](#webjob-types) beskrivs tidigare i den här artikeln. |
   | **utlösare** | Har schemalagts | Aktivera funktionen alltid på för att schemalägga att arbeta på ett tillförlitligt sätt. Always On finns bara i den Basic, Standard och Premium-prisnivåerna.|
   | **CRON-uttryck** | 0 0/20 * * * * | [CRON-uttryck](#cron-expressions) beskrivs i följande avsnitt. |

4. Klicka på **OK**.

   Ny Webbjobbet visas på den **WebJobs** sidan.

   ![Lista över WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>CRON-uttryck

Du kan ange en [CRON-uttryck](../azure-functions/functions-bindings-timer.md#cron-expressions) i portalen eller inkludera en `settings.job` i roten av ditt WebJob *.zip* fil, som i följande exempel:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Mer information finns i [schemalägga ett utlösta Webbjobb](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a> Visa jobbets historik

1. Välj WebJob som du vill se historiken för och välj sedan den **loggar** knappen.
   
   ![Loggar knappen](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. I den **WebJob information** väljer du en tid att visa detaljer för körs.
   
   ![WebJob-information](./media/web-sites-create-web-jobs/webjobdetails.png)

3. I den **WebJob körningsinformation** väljer **växla utdata** att se texten i logginnehållet.
   
    ![Webbjobb körningsinformation](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Om du vill se utdata texten i ett separat webbläsarfönster, **hämta**. Om du vill hämta själva texten, högerklickar du på **hämta** och använda webbläsaren för att spara filinnehållet.
   
5. Välj den **WebJobs** dynamiska länkar på länken längst upp på sidan för att gå till en lista över WebJobs.

    ![WebJob brödsmula](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista över WebJobs i instrumentpanelen för körningshistorik](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a>Nästa steg

Azure WebJobs SDK kan användas med WebJobs till att förenkla många programmeringsspråk. Mer information finns i [vad är WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
