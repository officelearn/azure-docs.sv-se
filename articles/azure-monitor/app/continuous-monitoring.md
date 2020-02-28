---
title: Kontinuerlig övervakning av din DevOps release-pipeline med Azure-pipeline och Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera kontinuerlig övervakning med Application Insights
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: e565101218b975ef2bd29b8a32a4aa1bf4300b6d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655403"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Lägg till kontinuerlig övervakning i din versions pipeline

Azure-pipeliner integreras med Azure Application insikter för att tillåta kontinuerlig övervakning av din DevOps release-pipeline under hela livs cykeln för program utveckling. 

Med kontinuerlig övervakning kan lanserings pipelines omfatta övervaknings data från Application Insights och andra Azure-resurser. När en release-pipeline identifierar en Application Insights avisering, kan pipelinen eller återställa distributionen tills aviseringen har lösts. Om alla kontroller godkänns kan distributionerna fortsätta automatiskt från testa hela vägen till produktion, utan att behöva vidta några manuella åtgärder. 

## <a name="configure-continuous-monitoring"></a>Konfigurera kontinuerlig övervakning

1. I [Azure DevOps](https://dev.azure.com)väljer du en organisation och ett projekt.
   
1. På den vänstra menyn på sidan projekt väljer du **pipelines** > - **versioner**. 
   
1. Släpp pilen bredvid **ny** och välj **ny versions pipeline**. Eller, om du inte har en pipeline ännu, väljer du **ny pipeline** på sidan som visas.
   
1. I fönstret **Välj en mall** söker du efter och väljer **Azure App service distribution med kontinuerlig övervakning**och väljer sedan **Använd**. 

   ![Ny pipeline för Azure pipeline-utgåvor](media/continuous-monitoring/001.png)

1. I rutan **steg 1** väljer du hyperlänken för att **Visa steg uppgifter.**

   ![Visa steg uppgifter](media/continuous-monitoring/002.png)

1. I konfigurations fönstret **steg 1** fyller du i följande fält: 

    | Parameter        | Värde |
   | ------------- |:-----|
   | **Namn på fas**      | Ange ett stadium namn eller lämna det i **steg 1**. |
   | **Azure-prenumeration** | List rutan och välj den länkade Azure-prenumeration som du vill använda.|
   | **Apptyp** | List rutan och välj din typ av app. |
   | **App Service namn** | Ange namnet på Azure App Service. |
   | **Resurs grupps namn för Application Insights**    | I list rutan och väljer du den resurs grupp som du vill använda. |
   | **Application Insights resurs namn** | List rutan och välj Application Insights resursen för den resurs grupp som du har valt.

1. Om du vill spara pipelinen med standardinställningar för varnings regler väljer du **Spara** längst upp till höger i fönstret Azure-DevOps. Ange en beskrivande kommentar och välj sedan **OK**.

## <a name="modify-alert-rules"></a>Ändra aviserings regler

I rutan har **Azure App service distribution med kontinuerlig övervakning** fyra aviserings regler: **tillgänglighet**, **misslyckade förfrågningar**, **Server svars tid**och **Server undantag**. Du kan lägga till fler regler eller ändra regel inställningarna så att de uppfyller dina service nivå behov. 

Ändra inställningar för varnings regler:

1. I det vänstra fönstret på sidan Frisläpp pipelines väljer du **konfigurera Application Insights aviseringar**.

1. I fönstret **Azure Monitor aviseringar** väljer du ellipsen **...** bredvid **varnings regler**.
   
1. I dialog rutan **aviserings regler** väljer du den nedrullningsbara symbolen bredvid en varnings regel, till exempel **tillgänglighet**. 
   
1. Ändra **tröskelvärdet** och andra inställningar så att de uppfyller dina krav.
   
   ![Ändra avisering](media/continuous-monitoring/003.png)
   
1. Välj **OK**och välj sedan **Spara** längst upp till höger i fönstret Azure-DevOps. Ange en beskrivande kommentar och välj sedan **OK**.

## <a name="add-deployment-conditions"></a>Lägg till distributions villkor

När du lägger till distributions portar till din versions pipeline förhindrar du oönskade frisläppnings åtgärder. När du har löst aviseringen kan distributionen fortsätta automatiskt.

Lägga till distributions portar:

1. På sidan för huvud pipelinen, under **steg**, väljer du **villkor för för distribution** eller **efter distribution** , beroende på vilket stadium som kräver en kontinuerlig övervaknings grind.
   
   ![Villkor för distribution](media/continuous-monitoring/004.png)
   
1. I konfigurations fönstret **villkor för distribution** anger du att **portarna** ska **aktive ras**.
   
1. Bredvid **distributions portar**väljer du **Lägg till**.
   
1. Välj **fråga Azure Monitor aviseringar** från List menyn. Med det här alternativet kan du komma åt både Azure Monitor-och Application Insights-aviseringar.
   
   ![Fråga Azure Monitor aviseringar](media/continuous-monitoring/005.png)
   
1. Under **utvärderings alternativ**anger du de värden som du vill använda för inställningar som **tiden mellan omutvärderingen av grindarna** och **den tids gräns efter vilken grinden har misslyckats**. 

## <a name="view-release-logs"></a>Visa versions loggar

Du kan se beteendet för distributions porten och andra versions steg i versions loggarna. Så här öppnar du loggarna:

1. Välj **versioner** på den vänstra menyn på sidan pipelines. 
   
1. Välj en version. 
   
1. Under **steg**väljer du ett steg för att visa en versions Sammanfattning. 
   
1. Om du vill visa loggar väljer du **Visa loggar** i versions sammanfattningen, väljer länken **slutförd** eller **misslyckad** i något Stadium eller hovrar över alla steg och väljer **loggar**. 
   
   ![Visa versions loggar](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure-pipeliner finns i [dokumentationen för Azure pipeline](https://docs.microsoft.com/azure/devops/pipelines).
