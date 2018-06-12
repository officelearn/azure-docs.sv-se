---
title: Viktiga kommentarer för Application Insights | Microsoft Docs
description: Lägg till distribution eller skapa markörer till metrics explorer-diagram i Application Insights.
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
ms.openlocfilehash: fb4bcd57062017c0d7ee802ba1f46660476f6af2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293443"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anteckningar i mått diagram i Application Insights
Anteckningar på [Metrics Explorer](app-insights-metrics-explorer.md) diagram visas där du har distribuerat en ny version eller betydande händelse. De gör det enkelt att se om ändringarna hade någon inverkan på programmets prestanda. De kan skapas automatiskt av den [Visual Studio Team Services bygg system](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs). Du kan också skapa anteckningar om du vill flagga alla händelser som du vill genom att [skapa dem från PowerShell](#create-annotations-from-powershell).

![Exempel på anteckningar med synliga korrelation med serversvarstid](./media/app-insights-annotations/00.png)



## <a name="release-annotations-with-vsts-build"></a>Släpp anteckningar med VSTS build

Versionen anteckningar är en funktion i bygga molnbaserade och utgåva tjänst av Visual Studio Team Services. 

### <a name="install-the-annotations-extension-one-time"></a>Installera tillägg för anteckningar (en gång)
För att kunna skapa versionen anteckningar, måste du installera en av de många Team Service tillägg som är tillgängliga i Visual Studio Marketplace.

1. Logga in på ditt [Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/setup/sign-up-for-visual-studio-online) projekt.
2. I Visual Studio Marketplace [hämta tillägget versionen anteckningar](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations), och lägga till den i ditt Team Services-konto.

![AT övre högra av Team Services webbsida, öppna Marketplace. Välj Visual Team Services och under version och utgåva, Välj finns mer.](./media/app-insights-annotations/10.png)

Du behöver bara göra detta en gång för Visual Studio Team Services-konto. Versionen anteckningar kan nu konfigureras för alla projekt i ditt konto. 

### <a name="configure-release-annotations"></a>Konfigurera versionen anteckningar

Du måste hämta en separat API-nyckel för varje VSTS versionsmall.

1. Logga in på den [Microsoft Azure Portal](https://portal.azure.com) och öppna Application Insights-resurs som övervakar dina program. (Eller [skapa en nu](app-insights-overview.md), om du inte gjort det ännu.)
2. Öppna **API-åtkomst**, **Application Insights Id**.
   
    ![Öppna Application Insights-resurs i portal.azure.com, och välja inställningar. Öppna API-åtkomst. Kopiera program-ID](./media/app-insights-annotations/20.png)

4. Öppna (eller skapa) på versionsmall som hanterar din distribution från Visual Studio Team Services i ett nytt webbläsarfönster. 
   
    Lägga till en aktivitet och välj Application Insights versionen anteckningen uppgiften på menyn.
   
    Klistra in den **program-Id** som du kopierade från bladet API-åtkomst.
   
    ![Öppna versionen i Visual Studio Team Services, Välj en definition av versionen och väljer Redigera. Klicka på Lägg till aktivitet och välj Application Insights versionen anteckningen. Klistra in Application Insights-Id.](./media/app-insights-annotations/30.png)
4. Ange den **APIKey** till en variabel `$(ApiKey)`.

5. Skapa en ny API-nyckel och ta en kopia av den i Azure-fönstret.
   
    ![I bladet API-åtkomst i Azure-fönstret klickar du på Skapa API-nyckel. Ange en kommentar, skriva anteckningar och klickar på Generera nyckel. Kopiera den nya nyckeln.](./media/app-insights-annotations/40.png)

6. Öppna fliken Konfiguration av mallen versionen.
   
    Skapa en variabel definition för `ApiKey`.
   
    Klistra in din API-nyckeln som ApiKey variabeldefinitionen.
   
    ![I fönstret Team Services väljer du fliken Konfiguration och klicka på Lägg till variabel. Ange namnet till ApiKey och till värdet och klistra in den nyckel som du just genererade och på låsikonen.](./media/app-insights-annotations/50.png)
7. Slutligen **spara** release-definition.


## <a name="view-annotations"></a>Visa anteckningar
Nu när du använder mallen versionen för att distribuera en ny version, kommer en anteckning att skickas till Application Insights. Anteckningarna visas i diagram i Metrics Explorer.

Klicka på valfri anteckning markör öppna information om versionen, inklusive begärande källa kontrollen gren, släpper definition och miljö.

![Klicka på valfri utgåva anteckningen markör.](./media/app-insights-annotations/60.png)

## <a name="create-custom-annotations-from-powershell"></a>Skapa anpassade anteckningar från PowerShell
Du kan också skapa anteckningar från någon annan process som du vill (utan att använda VS Team System). 


1. Skapa en lokal kopia av den [Powershell-skript från GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Hämta program-ID och skapa en API-nyckeln från bladet API-åtkomst.

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
