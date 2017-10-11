---
title: "Avgränsa telemetri från utveckling, testa och släpp i Azure Application Insights | Microsoft Docs"
description: "Direkt telemetri till olika resurser för utveckling, test- och stämplar."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: bwren
ms.openlocfilehash: f51fa4639aaa60686cc349683713c6e5f9732bb9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Avgränsa telemetri från utveckling, Test och produktion

När du utvecklar nästa version av ett program måste du inte vill att blanda ihop den [Programinsikter](app-insights-overview.md) telemetri från den nya versionen och den redan utgivna versionen. Skicka telemetrin från olika development steg för att avgränsa Application Insights-resurser med separat instrumentation nycklar (ikeys) för att undvika förvirring. Om du vill göra det lättare att ändra instrumentation nyckeln som en version som flyttas från ett steg till en annan, kan det vara användbart att ange ikey i koden i stället för i konfigurationsfilen. 

(Om datorn är en Azure-molntjänst är [en annan metod för att separat ikeys](app-insights-cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Om resurser och instrumentation nycklar

När du ställer in Application Insights-övervakning för webbappen kan du skapa en Application Insights *resurs* i Microsoft Azure. Du öppnar den här resursen i Azure-portalen för att kunna se och analysera telemetri som samlats in från din app. Resursen har identifierats av en *instrumentation nyckeln* (ikey). När du installerar Application Insights-paketet för att övervaka din app kan konfigurera du den med instrumentation-nyckel så att den vet var att skicka telemetrin om.

Vanligtvis väljer att använda separata resurser eller en enda delad resurs i olika scenarier:

* Olika, oberoende program - använda en separat resurs och ikey för varje app.
* Flera komponenter eller roller från en affärsprogram - använda en [enkel delad resurs](app-insights-monitor-multi-role-apps.md) för komponent-appar. Telemetri kan filtreras eller segmenterade av egenskapen cloud_RoleName.
* Utveckling och Test Release - använda en separat resurs och ikey för versioner av systemet i 'i stämpel' eller steget i produktion.
* EN | B-testning - använda en enskild resurs. Skapa en TelemetryInitializer om du vill lägga till en egenskap telemetri som identifierar varianter.


## <a name="dynamic-ikey"></a>Dynamisk instrumentation nyckel

Om du vill göra det enklare att ändra ikey efter koden flyttar mellan stegen i produktionen genom att ange den i koden i stället för i konfigurationsfilen.

Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

I det här exemplet placeras ikeys för olika resurser i olika versioner av Webbkonfigurationsfilen. Byta Webbkonfigurationsfilen - som du kan göra som en del av frigörelseskriptet - ska växla målresursen.

### <a name="web-pages"></a>Webbsidor
IKey används också i din app webbsidor i den [skript som du har fått från Snabbstart-bladet](app-insights-javascript.md). I stället för att koda den bokstavligt till skriptet, generera den från servern. Till exempel i en ASP.NET-app:

*JavaScript i Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Skapa ytterligare Application Insights-resurser
Om du vill dela telemetri för olika programkomponenter eller annan stämplar (dev/test/produktion) av samma komponent sedan har du vill skapa en ny Application Insights-resurs.

I den [portal.azure.com](https://portal.azure.com), Lägg till Application Insights-resurs:

![Klicka på Nytt, Application Insights](./media/app-insights-separate-resources/01-new.png)

* **Programtyp** påverkar vad som visas på bladet översikt och egenskaper som är tillgängliga i [mått explorer](app-insights-metrics-explorer.md). Om du inte ser typen av app väljer du något av följande web för webbsidor.
* **Resursgruppen** är i syfte att underlätta för att hantera egenskaper som [åtkomstkontroll](app-insights-resources-roles-access-control.md). Du kan använda separata resursgrupper för utveckling, testning och produktion.
* **Prenumerationen** är din betalningskonto i Azure.
* **Plats** är där vi behåller dina data. För närvarande kan den inte ändras. 
* **Lägg till instrumentpanelen** placerar en snabb åtkomst-panelen för din resurs på Azure-startsidan. 

Det tar några sekunder för att skapa resursen. En varning visas när du är klar.

(Du kan skriva en [PowerShell-skript](app-insights-powershell-script-create-resource.md) att skapa en resurs automatiskt.)

### <a name="getting-the-instrumentation-key"></a>Hämta nyckeln instrumentation
Nyckeln instrumentation identifierar resursen som du skapade. 

![Klicka på Essentials, nyckeln Instrumentation CTRL + C](./media/app-insights-separate-resources/02-props.png)

Du måste instrumentation nycklarna för alla resurser som din app ska skicka data.

## <a name="filter-on-build-number"></a>Filtrera efter build-nummer
När du publicerar en ny version av din app vill du kunna skilja telemetrin från olika versioner.

Du kan ange egenskapen programversion så att du kan filtrera [Sök](app-insights-diagnostic-search.md) och [mått explorer](app-insights-metrics-explorer.md) resultat.

![Filtrering på en egenskap](./media/app-insights-separate-resources/050-filter.png)

Det finns flera olika metoder för att ange egenskapen programversion.

* Ange direkt:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Omsluta den raden i en [telemetri initieraren](app-insights-api-custom-events-metrics.md#defaults) att kontrollera att alla TelemetryClient instanser är konsekvent.
* [ASP.NET] Ange version `BuildInfo.config`. Webbmodulen ska hämta version från noden BuildLabel. Inkludera den här filen i projektet och Kom ihåg att ange egenskapen Kopiera alltid i Solution Explorer.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generera BuildInfo.config automatiskt i MSBuild. Gör du genom att lägga till några rader till din `.csproj` fil:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Detta genererar en fil med namnet *yourProjectName*. BuildInfo.config. Publiceringsprocessen byter namn på den till BuildInfo.config.

    Build-etiketten innehåller platshållare (AutoGen_...) när du skapar med Visual Studio. Men om skapats med MSBuild fylls den med rätt versionsnumret.

    Ange om du vill tillåta MSBuild att generera versionsnummer version som `1.0.*` i AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. I filen .csproj lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](app-insights-diagnostic-search.md) eller när du [undersöker mätvärden](app-insights-metrics-explorer.md).

Observera dock att build-versionsnumret endast genereras av Microsoft Build Engine, och inte av utvecklarversionen i Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Visual Studio Team Services, kan du [få en anteckningsmarkör](app-insights-annotations.md) tillagd i diagrammen när du släpper en ny version. Följande bild visar hur markeringen visas.

![Skärmbild av exempel på versionsanteckning i ett diagram](./media/app-insights-asp-net/release-annotation.png)
## <a name="next-steps"></a>Nästa steg

* [Delade resurser för flera roller](app-insights-monitor-multi-role-apps.md)
* [Skapa en telemetri initieraren Skilj A | B varianter](app-insights-api-filtering-sampling.md#add-properties)
