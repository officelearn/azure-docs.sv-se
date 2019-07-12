---
title: Versionsanteckningar för Application Insights | Microsoft Docs
description: Lägg till distribution eller skapa markörer i metrics explorer diagrammen i Application Insights.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mbullwin
ms.openlocfilehash: e3ec202ba6126b150fb78c76591682f163018661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604550"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar på måttdiagram i Application Insights

Anteckningar på [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) diagrammen visas där du har distribuerat en ny version eller andra viktiga händelser. Kommentarer gör det enkelt att se om ändringarna hade någon effekt på prestanda för ditt program. De kan skapas automatiskt av den [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) buildsystemet. Du kan också skapa anteckningar för att flagga en händelse om du vill genom att skapa dem från PowerShell.

> [!NOTE]
> Den här artikeln visar det inaktuella **klassiska mått uppleva**. Anteckningar är bara tillgängliga i den klassiska upplevelsen och i  **[arbetsböcker](../../azure-monitor/app/usage-workbooks.md)** . Läs mer om den aktuella mått-upplevelsen i [avancerade funktioner för Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Exempel på anteckningar](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Versionsanteckningar med Azure Pipelines build

Versionsanteckningar är en funktion i Azure Pipelines molnbaserad tjänst för Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägget anteckningar (en gång)
Om du vill kunna skapa Versionsanteckningar måste du installera en av de många tillgängliga tillägg i Azure DevOps i Visual Studio Marketplace.

1. Logga in på din [Azure DevOps](https://azure.microsoft.com/services/devops/) projekt.
   
1. I Visual Studio Marketplace [Versionsanteckningar tillägget](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) , Välj din Azure DevOps-organisation och välj sedan **installera** att lägga till tillägget till din Azure DevOps-organisation.
   
   ![Välj en Azure DevOps-organisation och därefter på installera.](./media/annotations/1-install.png)
   
Du behöver bara installera tillägget en gång för din Azure DevOps-organisation. Du kan nu konfigurera Versionsanteckningar för alla projekt i din organisation.

### <a name="configure-release-annotations"></a>Konfigurera Versionsanteckningar

Skapa en separat API-nyckel för var och en av dina Azure-Pipelines versionen mallar.

1. Logga in på den [Azure-portalen](https://portal.azure.com) och öppna den Application Insights-resurs som övervakar dina program. Eller om du inte har någon, [skapa en ny Application Insights-resurs](../../azure-monitor/app/app-insights-overview.md).
   
1. Öppna den **API-åtkomst** fliken och kopiera den **Application Insights-ID**.
   
   ![Under API-åtkomst, kopierar du programmets ID.](./media/annotations/2-app-id.png)

1. I ett separat webbläsarfönster, öppna eller skapa versionsmall som hanterar dina Pipelines för Azure-distributioner.
   
1. Välj **Lägg till aktivitet**, och välj sedan den **Application Insights på Versionsanteckning** uppgift på menyn.
   
   ![Välj Lägg till aktivitet och Versionsanteckning för Application Insights.](./media/annotations/3-add-task.png)
   
1. Under **program-ID**, klistra in Application Insights-ID som du kopierade från den **API-åtkomst** fliken.
   
   ![Klistra in Application Insights-ID](./media/annotations/4-paste-app-id.png)
   
1. Tillbaka i Application Insights **API-åtkomst** väljer **skapa API-nyckel**. 
   
   ![Välj Skapa API-nyckel på fliken API-åtkomst.](./media/annotations/5-create-api-key.png)
   
1. I den **skapa API-nyckel** fönstret anger du en beskrivning, Välj **Skriv anteckningar**, och välj sedan **skapa nycklar**. Kopiera den nya nyckeln.
   
   ![I fönstret Skapa API anger du en beskrivning och välj Skriv anteckningar väljer generera nyckeln.](./media/annotations/6-create-api-key.png)
   
1. I fönstret versionen mallen på den **variabler** fliken **Lägg till** att skapa en variabel definition för den nya API-nyckeln.

1. Under **namn**, ange `ApiKey`, och under **värdet**, klistra in API-nyckel som du kopierade från den **API-åtkomst** fliken.
   
   ![Välj Lägg till, namn på variabeln ApiKey, på fliken Azure DevOps variabler och klistra in API-nyckel under värde.](./media/annotations/7-paste-api-key.png)
   
1. Välj **spara** i fönstret huvudsakliga versionen mall för att spara mallen.

## <a name="view-annotations"></a>Visa anteckningar
Nu när du använder mallen versionen för att distribuera en ny version, skickas en anteckning till Application Insights. Anteckningarna visas i diagram i **Metrics Explorer**.

Välj alla anteckningsmarkör (ljust grå pilen) för att öppna information om versionen, inklusive begärande, kontroll källgren, releasepipeline och miljö.

![Välj en anteckningsmarkör för versionen.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade kommentarer från PowerShell
Du kan använda den [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell-skriptet från GitHub för att skapa anteckningar från en process som helst, utan att använda Azure DevOps. 

1. Skapa en lokal kopia av [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Använd stegen i föregående procedur för att hämta din Application Insights-ID och skapa en API-nyckel från Application Insights **API-åtkomst** fliken.
   
1. Anropa PowerShell-skriptet med följande kod, ersätta platshållarna vinkel-och avslutas med dina värden. Den `-releaseProperties` är valfria. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Du kan ändra skriptet, till exempel för att skapa anteckningar för tidigare.

## <a name="next-steps"></a>Nästa steg

* [Skapa arbetsobjekt](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](../../azure-monitor/app/powershell.md)
