---
title: Kontinuerlig övervakning av din DevOps-utgivningspipeline med Azure Pipelines och Azure Application Insights | Microsoft-dokument
description: Innehåller instruktioner för att snabbt konfigurera kontinuerlig övervakning med Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655403"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Lägg till kontinuerlig övervakning till din releasepipeline

Azure Pipelines integreras med Azure Application Insights för att möjliggöra kontinuerlig övervakning av din DevOps-versionspipeline under hela livscykeln för programvaruutveckling. 

Med kontinuerlig övervakning kan utgivningspipelor innehålla övervakningsdata från Application Insights och andra Azure-resurser. När utgivningspipelinen upptäcker en Application Insights-avisering kan pipelinen gate eller återställa distributionen tills aviseringen har åtgärdats. Om alla kontroller passerar kan distributioner automatiskt fortsätta från test hela vägen till produktion, utan att behöva göra manuella åtgärder. 

## <a name="configure-continuous-monitoring"></a>Konfigurera kontinuerlig övervakning

1. Välj en organisation och ett projekt i [Azure DevOps.](https://dev.azure.com)
   
1. Välj **Pipelines** > **Releases**till vänster på projektsidan. 
   
1. Släpp ned pilen bredvid **Ny** och välj **Ny versionspipeline**. Om du inte har någon pipeline ännu väljer du **Ny pipeline** på sidan som visas.
   
1. Sök efter och välj **Azure App Service-distribution i**fönstret Välj en **mall** med kontinuerlig övervakning och välj sedan **Använd**. 

   ![Ny Azure Pipelines-version pipeline](media/continuous-monitoring/001.png)

1. Markera hyperlänken till **Visa stegaktiviteter** i rutan Steg **1.**

   ![Visa fasaktiviteter](media/continuous-monitoring/002.png)

1. Fyll i följande fält i konfigurationsfönstret **steg 1:** 

    | Parameter        | Värde |
   | ------------- |:-----|
   | **Namn på fas**      | Ange ett artistnamn eller lämna det på **steg 1**. |
   | **Azure-prenumeration** | Listruta och välj den länkade Azure-prenumeration som du vill använda.|
   | **Apptyp** | Rullgardinsmenyn och välj apptyp. |
   | **Namn på App-tjänst** | Ange namnet på din Azure App-tjänst. |
   | **Resursgruppsnamn för programinsikter**    | Listruta och välj den resursgrupp som du vill använda. |
   | **Resursnamn för Application Insights** | Listrutan och välj application insights-resursen för den resursgrupp du valde.

1. Om du vill spara pipelinen med standardinställningsinställningar för varningsregel väljer du **Spara** längst upp till höger i Azure DevOps-fönstret. Ange en beskrivande kommentar och välj sedan **OK**.

## <a name="modify-alert-rules"></a>Ändra varningsregler

Azure **App Service-distributionen med mall för kontinuerlig övervakning** har fyra varningsregler: **Tillgänglighet**, **Misslyckade begäranden,** **Serversvarstid**och **Serverundantag**. Du kan lägga till fler regler eller ändra regelinställningarna för att uppfylla dina servicenivåbehov. 

Så här ändrar du inställningar för varningsregel:

1. I den vänstra rutan på utgivningspipelinen väljer du **Konfigurera aviseringar om programstatistik**.

1. I fönstret **Azure Monitor Alerts** väljer du ellipsen **...** **bredvid Varningsregler**.
   
1. I dialogrutan **Varningsregler** väljer du listrutan bredvid en varningsregel, till exempel **Tillgänglighet**. 
   
1. Ändra **tröskelvärdet** och andra inställningar för att uppfylla dina krav.
   
   ![Ändra avisering](media/continuous-monitoring/003.png)
   
1. Välj **OK**och välj sedan **Spara** längst upp till höger i Azure DevOps-fönstret. Ange en beskrivande kommentar och välj sedan **OK**.

## <a name="add-deployment-conditions"></a>Lägg till distributionsvillkor

När du lägger till distributionsgrindar i versionspipelinen förhindrar en avisering som överskrider de tröskelvärden du anger oönskad publiceringskampanj. När du har löst aviseringen kan distributionen fortsätta automatiskt.

Så här lägger du till distributionsgrindar:

1. På huvudpipellinesidan under **Faser**väljer du symbolen **För distributionsvillkor** eller efter **distributionsvillkor,** beroende på vilket stadium som behöver en kontinuerlig övervakningsgrind.
   
   ![Villkor för distribution före distribution](media/continuous-monitoring/004.png)
   
1. Ange **Gates** till **Aktiverad**i konfigurationsfönstret **För distributionsvillkor** .
   
1. Välj Lägg till **bredvid Distributionsgrindar** **.**
   
1. Välj **Frågevarningar för Azure Monitor** på den nedrullningsbara menyn. Med det här alternativet kan du komma åt aviseringar om både Azure Monitor och Application Insights.
   
   ![Fråga Azure Monitor-aviseringar](media/continuous-monitoring/005.png)
   
1. Under **Utvärderingsalternativ**anger du de värden du vill ha för inställningar som **Tiden mellan omvärdering av grindar** och **Timeout efter vilken grindar misslyckas**. 

## <a name="view-release-logs"></a>Visa utgivningsloggar

Du kan se distributionsgrindsbeteende och andra versionssteg i versionsloggarna. Så här öppnar du loggarna:

1. Välj **Utgåvor** på den vänstra menyn på pipeline-sidan. 
   
1. Välj en eventuell utgåva. 
   
1. Under **Steg**väljer du ett steg för att visa en versionssammanfattning. 
   
1. Om du vill visa loggar väljer du **Visa loggar** i versionssammanfattningen, väljer hyperlänken Efterföljande eller **Misslyckad** i något skede eller hovrar över en fas och väljer **Loggar**. **Failed** 
   
   ![Visa utgivningsloggar](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Pipelines finns i [Dokumentationen till Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
