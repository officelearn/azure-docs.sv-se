---
title: Släpp anteckningar för Application Insights | Microsoft-dokument
description: Lägg till distributions- eller byggmarkörer i dina måttutforskarens diagram i Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666521"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar om måttdiagram i Programinsikter

Anteckningar på [Statistik explorer-diagram](../../azure-monitor/app/metrics-explorer.md) visar var du har distribuerat en ny version eller andra viktiga händelser. Anteckningar gör det enkelt att se om dina ändringar har någon effekt på programmets prestanda. De kan skapas automatiskt av [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) build-systemet. Du kan också skapa anteckningar för att flagga alla händelser du vill genom att skapa dem från PowerShell.

> [!NOTE]
> Den här artikeln återspeglar den inaktuella **klassiska måttupplevelsen**. Anteckningar är för närvarande endast tillgängliga i den klassiska upplevelsen och i **[arbetsböcker](../../azure-monitor/app/usage-workbooks.md)**. Mer information om den aktuella måttupplevelsen finns i [Avancerade funktioner i Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Exempel på anteckningar](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Släpp anteckningar med Azure Pipelines build

Versionsanteckningar är en funktion i den molnbaserade Azure Pipelines-tjänsten för Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägget Anteckningar (en gång)
För att kunna skapa versionsanteckningar måste du installera ett av de många Azure DevOps-tillägg som finns på Visual Studio Marketplace.

1. Logga in på ditt [Azure DevOps-projekt.](https://azure.microsoft.com/services/devops/)
   
1. På [tilläggssidan för Utgivning av kommentarer till](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) Visual Studio Marketplace väljer du din Azure DevOps-organisation och väljer sedan **Installera** för att lägga till tillägget i din Azure DevOps-organisation.
   
   ![Välj en Azure DevOps-organisation och välj sedan Installera.](./media/annotations/1-install.png)
   
Du behöver bara installera tillägget en gång för din Azure DevOps-organisation. Du kan nu konfigurera versionsanteckningar för alla projekt i organisationen.

### <a name="configure-release-annotations"></a>Konfigurera versionsanteckningar

Skapa en separat API-nyckel för var och en av dina Azure Pipelines-versionsmallar.

1. Logga in på [Azure-portalen](https://portal.azure.com) och öppna application insights-resursen som övervakar ditt program. Eller om du inte har någon, [skapa en ny Application Insights-resurs](../../azure-monitor/app/app-insights-overview.md).
   
1. Öppna fliken **API Access** och kopiera **Application Insights ID**.
   
   ![Kopiera program-ID:t under API Access.](./media/annotations/2-app-id.png)

1. Öppna eller skapa versionsmallen som hanterar dina Azure Pipelines-distributioner i ett separat webbläsarfönster.
   
1. Välj **Lägg till uppgift**och välj sedan aktiviteten Release **Annoteation för programinsikter** på menyn.
   
   ![Välj Lägg till uppgift och välj Utgivningsannotering för programinsikter.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Aktiviteten Versionsärend anteckning stöder för närvarande endast Windows-baserade agenter. Det kommer inte att köras på Linux, macOS eller andra typer av agenter.
   
1. Under **Program-ID**klistrar du in application insights-ID som du kopierade från fliken **API Access.**
   
   ![Klistra in application insights-ID](./media/annotations/4-paste-app-id.png)
   
1. I fönstret Application Insights **API Access** väljer du **Skapa API-nyckel**. 
   
   ![Välj Skapa API-nyckel på fliken API Access.](./media/annotations/5-create-api-key.png)
   
1. Skriv en beskrivning i fönstret **Skapa API-nyckel,** välj **Skrivanteckningar**och välj sedan **Generera nyckel**. Kopiera den nya nyckeln.
   
   ![Skriv en beskrivning i fönstret Skapa API-nyckel, välj Skrivanteckningar och välj sedan Generera nyckel.](./media/annotations/6-create-api-key.png)
   
1. Välj **Lägg till** på fliken Variabler i fönstret **versionsmallar** för att skapa en variabel definition för den nya API-nyckeln.

1. Under **Namn** `ApiKey`anger du och under **Värde**klistrar du in API-nyckeln som du kopierade från fliken **API Access.**
   
   ![På fliken Azure DevOps Variabler väljer du Lägg till, namnger variabeln ApiKey och klistrar in API-nyckeln under Värde.](./media/annotations/7-paste-api-key.png)
   
1. Välj **Spara** i mallfönstret för huvudversionen om du vill spara mallen.

## <a name="view-annotations"></a>Visa anteckningar
När du nu använder versionsmallen för att distribuera en ny version skickas en anteckning till Application Insights. Anteckningarna visas i diagram i **Metrics Explorer**.

Välj en anteckningsmarkör (ljusgrå pil) om du vill öppna information om versionen, inklusive beställare, källkontrollgren, versionspipeline och miljö.

![Markera en versionsnoteringsmarkör.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa egna anteckningar från PowerShell
Du kan använda [PowerShell-skriptet CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) från GitHub för att skapa anteckningar från valfri process, utan att använda Azure DevOps. 

1. Gör en lokal kopia av [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Följ stegen i föregående procedur för att hämta ditt Application Insights-ID och skapa en API-nyckel från fliken Application Insights **API Access.**
   
1. Anropa PowerShell-skriptet med följande kod och ersätter de vinkelkonsolerade platshållarna med dina värden. De `-releaseProperties` är valfria. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Du kan ändra skriptet, till exempel för att skapa anteckningar för det förflutna.

## <a name="next-steps"></a>Nästa steg

* [Skapa arbetsuppgifter](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](../../azure-monitor/app/powershell.md)
