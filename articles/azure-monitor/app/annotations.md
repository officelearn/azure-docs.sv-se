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
ms.date: 11/08/2018
ms.author: mbullwin
ms.openlocfilehash: 652591fc4539e6f19c0606c1502609a823327f2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794812"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar på måttdiagram i Application Insights

Anteckningar på [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) diagrammen visas där du har distribuerat en ny version eller betydande händelse. De gör det enkelt att se om ändringarna hade någon effekt på prestanda för ditt program. De kan skapas automatiskt av den [Azure DevOps-tjänsterna buildsystemet](https://docs.microsoft.com/azure/devops/pipelines/tasks/). Du kan också skapa anteckningar för att flagga en händelse om du vill genom att skapa dem från PowerShell.

> [!NOTE]
> Den här artikeln visar det inaktuella **klassiska mått uppleva**. Anteckningar är bara tillgängliga i den klassiska upplevelsen och i  **[arbetsböcker](../../azure-monitor/app/usage-workbooks.md)**. Mer information om aktuellt mått upplevelse, finns [i den här artikeln](../../azure-monitor/platform/metrics-charts.md).

![Exempel på anteckningar med synliga korrelation med svarstid för servern](./media/annotations/00.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Versionsanteckningar med Azure DevOps-Services-version

Versionsanteckningar är en funktion i Azure Pipelines molnbaserad tjänst för Azure DevOps-tjänsterna. 

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägget anteckningar (en gång)
Om du vill kunna skapa Versionsanteckningar måste du installera en av de många tillgängliga tillägg i Azure DevOps-tjänsterna i Visual Studio Marketplace.

1. Logga in på din [Azure DevOps-tjänsterna](https://visualstudio.microsoft.com/vso/) projekt.
2. I Visual Studio Marketplace [hämta tillägg för Versionsanteckningar](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), och lägga till den i din organisation med Azure DevOps-tjänsterna.

![AT överst till höger i Azure DevOps-tjänsterna webbsida, öppna Marketplace. Välj Azure DevOps-tjänsterna och under Azure Pipelines, välj sedan se mer.](./media/annotations/10.png)

Du behöver bara göra detta en gång för din organisation med Azure DevOps-tjänsterna. Versionsanteckningar kan nu konfigureras för alla projekt i din organisation. 

### <a name="configure-release-annotations"></a>Konfigurera Versionsanteckningar

Du behöver en separat API-nyckel för varje mall för versionen av Azure DevOps-tjänsterna.

1. Logga in på den [Microsoft Azure Portal](https://portal.azure.com) och öppna den Application Insights-resurs som övervakar dina program. (Eller [skapa en nu](../../azure-monitor/app/app-insights-overview.md), om du inte gjort det ännu.)
2. Öppna **API-åtkomst**, **Application Insights-Id**.
   
    ![Öppna din Application Insights-resurs på portal.azure.com och välj inställningar. Öppna API-åtkomst. Kopiera program-ID](./media/annotations/20.png)

4. Öppna (eller skapa) på versionsmall som hanterar dina distributioner från Azure DevOps-tjänsterna i ett separat webbläsarfönster. 
   
    Lägg till en aktivitet och välj Application Insights på Versionsanteckning uppgiften på menyn.
   
    Klistra in den **program-Id** som du kopierade från bladet API-åtkomst.
   
    ![Öppna versionen, Välj en releasepipeline i Azure DevOps-tjänster, och välj Redigera. Klicka på Lägg till aktivitet och välj Application Insights på Versionsanteckning. Klistra in Application Insights-Id.](./media/annotations/30.png)
4. Ange den **APIKey** fältet till en variabel `$(ApiKey)`.

5. I fönstret Azure att skapa en ny API-nyckel och ta en kopia av den.
   
    ![I bladet API-åtkomst i Azure-fönstret klickar du på Skapa API-nyckel. Ange en kommentar, skriva kommentarer och klicka på Generera nyckel. Kopiera den nya nyckeln.](./media/annotations/40.png)

6. Öppna fliken Konfiguration för versionsmall för.
   
    Skapa en variabel definition för `ApiKey`.
   
    Klistra in din API-nyckel till ApiKey variabeldefinitionen.
   
    ![Välj fliken Konfiguration och klicka på Lägg till variabel i fönstret Azure DevOps-tjänsterna. Ange namnet till ApiKey och i värdet och klistra in den nyckel som du just genererade, klicka på låsikonen.](./media/annotations/50.png)
7. Slutligen **spara** versionspipelinen.


## <a name="view-annotations"></a>Visa anteckningar
När du använder mallen versionen för att distribuera en ny version, nu skickas en anteckning till Application Insights. Anteckningarna visas i diagrammen i Metrics Explorer.

Klicka på alla anteckningsmarkör att öppna information om versionen, inklusive begärande, kontroll källgren, viktig pipeline och miljö.

![Klicka på någon version anteckningsmarkör.](./media/annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade kommentarer från PowerShell
Du kan också skapa anteckningar från en process som du vill (utan att använda Azure DevOps-tjänster). 


1. Skapa en lokal kopia av den [Powershell-skriptet från GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Hämta program-ID och skapa en API-nyckel från bladet API-åtkomst.

3. Anropa skriptet så här:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Det är enkelt att ändra skriptet, till exempel för att skapa anteckningar för tidigare.

## <a name="next-steps"></a>Nästa steg

* [Skapa arbetsobjekt](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](../../azure-monitor/app/powershell.md)
