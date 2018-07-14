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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/16/2016
ms.author: mbullwin
ms.openlocfilehash: 056716e243980f0a8aadc1ff7e9b8776809ad88e
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036405"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar på måttdiagram i Application Insights
Anteckningar på [Metrics Explorer](app-insights-metrics-explorer.md) diagrammen visas där du har distribuerat en ny version eller betydande händelse. De gör det enkelt att se om ändringarna hade någon effekt på prestanda för ditt program. De kan skapas automatiskt av den [Visual Studio Team Services-buildsystemet](https://docs.microsoft.com/vsts/pipelines/tasks/). Du kan också skapa anteckningar för att flagga en händelse om du vill genom att [skapa dem från PowerShell](#create-annotations-from-powershell).

![Exempel på anteckningar med synliga korrelation med svarstid för servern](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Versionsanteckningar med VSTS-versionen

Versionsanteckningar är en funktion i den molnbaserade versionen och release-tjänsten för Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägget anteckningar (en gång)
Om du vill kunna skapa Versionsanteckningar måste du installera en av de många Team Service-tillägg som är tillgängliga i Visual Studio Marketplace.

1. Logga in på din [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projekt.
2. I Visual Studio Marketplace [hämta tillägg för Versionsanteckningar](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), och lägga till den i ditt Team Services-konto.

![AT överst till höger i Team Services-webbsidan, öppna Marketplace. Välj Visual Team Services och sedan välja se mer under Skapa och släpp.](./media/app-insights-annotations/10.png)

Du behöver bara göra detta en gång för ditt Visual Studio Team Services-konto. Versionsanteckningar kan nu konfigureras för alla projekt i ditt konto. 

### <a name="configure-release-annotations"></a>Konfigurera Versionsanteckningar

Du behöver en separat API-nyckel för varje mall för VSTS-versionen.

1. Logga in på den [Microsoft Azure Portal](https://portal.azure.com) och öppna den Application Insights-resurs som övervakar dina program. (Eller [skapa en nu](app-insights-overview.md), om du inte gjort det ännu.)
2. Öppna **API-åtkomst**, **Application Insights-Id**.
   
    ![Öppna din Application Insights-resurs på portal.azure.com och välj inställningar. Öppna API-åtkomst. Kopiera program-ID](./media/app-insights-annotations/20.png)

4. Öppna (eller skapa) på versionsmall som hanterar dina distributioner från Visual Studio Team Services i ett separat webbläsarfönster. 
   
    Lägg till en aktivitet och välj Application Insights på Versionsanteckning uppgiften på menyn.
   
    Klistra in den **program-Id** som du kopierade från bladet API-åtkomst.
   
    ![Öppna versionen, Välj en versionsdefinition i Visual Studio Team Services och välj Redigera. Klicka på Lägg till aktivitet och välj Application Insights på Versionsanteckning. Klistra in Application Insights-Id.](./media/app-insights-annotations/30.png)
4. Ange den **APIKey** fältet till en variabel `$(ApiKey)`.

5. I fönstret Azure att skapa en ny API-nyckel och ta en kopia av den.
   
    ![I bladet API-åtkomst i Azure-fönstret klickar du på Skapa API-nyckel. Ange en kommentar, skriva kommentarer och klicka på Generera nyckel. Kopiera den nya nyckeln.](./media/app-insights-annotations/40.png)

6. Öppna fliken Konfiguration för versionsmall för.
   
    Skapa en variabel definition för `ApiKey`.
   
    Klistra in din API-nyckel till ApiKey variabeldefinitionen.
   
    ![Välj fliken Konfiguration i Team Services-fönstret och klicka på Lägg till variabel. Ange namnet till ApiKey och i värdet och klistra in den nyckel som du just genererade, klicka på låsikonen.](./media/app-insights-annotations/50.png)
7. Slutligen **spara** versionsdefinitionen.


## <a name="view-annotations"></a>Visa anteckningar
När du använder mallen versionen för att distribuera en ny version, nu skickas en anteckning till Application Insights. Anteckningarna visas i diagrammen i Metrics Explorer.

Klicka på alla anteckningsmarkör att öppna information om versionen, inklusive begärande, kontroll källgren, viktig definition och miljö.

![Klicka på någon version anteckningsmarkör.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade kommentarer från PowerShell
Du kan också skapa anteckningar från en process som du vill (utan att använda VS Team System). 


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

* [Skapa arbetsobjekt](app-insights-diagnostic-search.md#create-work-item)
* [Automatisering med PowerShell](app-insights-powershell.md)
