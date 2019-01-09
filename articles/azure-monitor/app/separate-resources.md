---
title: Att separera telemetri från utveckling, testa och släppa i Azure Application Insights | Microsoft Docs
description: Direct telemetri till olika resurser för utveckling, testning och produktion stämplar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 578e30f0-31ed-4f39-baa8-01b4c2f310c9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2e9c599c12ed10327d352baee02500d2284d98d8
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121489"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Att separera telemetri från utveckling, testning och produktion

När du skapar i nästa version av ett program kan du inte vill blanda ihop den [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetri från den nya versionen och den redan utgivna versionen. Skicka telemetri från olika utveckling faser för att separera Application Insights-resurser med separat instrumenteringsnycklar (ikeys) för att undvika förvirring. Om du vill göra det lättare att ändra instrumenteringsnyckeln som en version som flyttar från ett stadium till en annan, kan det vara praktiskt att ange nyckeln i koden i stället för i konfigurationsfilen. 

(Om datorn är en Azure-molntjänst är det [ett annat sätt för att ange olika ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Om resurser och instrumenteringsnycklar

När du har konfigurerat Application Insights-övervakning för din webbapp kan du skapa en Application Insights *resource* i Microsoft Azure. Du öppnar den här resursen i Azure-portalen för att kunna se och analysera telemetri som samlas in från din app. Resursen har identifierats av en *instrumenteringsnyckeln* (ikey). När du installerar Application Insights-paketet för att övervaka din app kan konfigurera du den med instrumenteringsnyckeln, så att den vet att skicka telemetri.

Du väljer normalt att använda separata resurser eller en enda delad resurs i olika scenarier:

* Olika oberoende program - använda en separat resurs och ikey för varje app.
* Flera komponenter eller roller i ett affärsprogram – Använd en [enkel delad resurs](../../azure-monitor/app/app-map.md) för alla appar som komponenten. Telemetri kan filtreras eller uppdelat efter egenskapen cloud_RoleName.
* Utveckling, testning och version – Använd en separat resurs och ikey för versioner av systemet i ”stämpel” eller steget i produktion.
* A | B-testning - använda en enskild resurs. Skapa en TelemetryInitializer om du vill lägga till en egenskap i telemetrin som identifierar varianterna.


## <a name="dynamic-ikey"></a> Dynamisk instrumenteringsnyckeln

Om du vill göra det lättare att ändra nyckeln när koden flyttas mellan faser i produktion, anger du det i kod i stället för i konfigurationsfilen.

Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

I det här exemplet placeras ikeys för olika resurser i olika versioner av Webbkonfigurationsfilen. Växlar Webbkonfigurationsfilen – som du kan göra som en del av skriptet release - kommer växla målresursen.

### <a name="web-pages"></a>Webbsidor
Nyckeln används också i din app för webbsidor, i den [skript som du fick från Snabbstart-bladet](../../azure-monitor/app/javascript.md). I stället för att koda den bokstavligen till skriptet, generera du den från servern. Till exempel i en ASP.NET-app:

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
För att avgränsa telemetri för olika programkomponenter eller för olika stämplar (dev/test/produktion) av samma komponent du kommer att behöva skapa en ny Application Insights-resurs.

I den [portal.azure.com](https://portal.azure.com), lägga till en Application Insights-resurs:

![Klicka på Nytt, Application Insights](./media/separate-resources/01-new.png)

* **Programtyp** påverkar vad som visas på översiktsbladet och egenskaper som är tillgängliga i [metric explorer](../../azure-monitor/app/metrics-explorer.md). Om du inte ser din typ av app, väljer du en av de web-typerna för webbsidor.
* **Resursgrupp** är att underlätta för att hantera egenskaper som [åtkomstkontroll](../../azure-monitor/app/resources-roles-access-control.md). Du kan använda separata resursgrupper för utveckling, testning och produktion.
* **Prenumeration** är ditt betalningskonto i Azure.
* **Plats** är där vi sparar dina data. För närvarande kan inte ändras. 
* **Lägga till instrumentpanelen** placerar en panel för snabb åtkomst för din resurs på startsidan för Azure. 

Det tar några sekunder att skapa resursen. Du ser en avisering när den är klar.

(Du kan skriva en [PowerShell-skript](../../azure-monitor/app/powershell-script-create-resource.md) att skapa en resurs automatiskt.)

### <a name="getting-the-instrumentation-key"></a>Hämta instrumenteringsnyckeln
Instrumenteringsnyckeln som identifierar den resurs som du skapade. 

![Klicka på Essentials, Instrumenteringsnyckeln, CTRL + C](./media/separate-resources/02-props.png)

Du behöver instrumenteringsnycklar över alla resurser som din app skickar data.

## <a name="filter-on-build-number"></a>Filtrera på build-nummer
När du publicerar en ny version av din app kommer du att kunna skilja telemetri från olika versioner.

Du kan ange egenskapen programversion så att du kan filtrera [search](../../azure-monitor/app/diagnostic-search.md) och [metric explorer](../../azure-monitor/app/metrics-explorer.md) resultat.

![Filtrera efter en egenskap](./media/separate-resources/050-filter.png)

Det finns flera olika metoder för att ange egenskapen programversion.

* Ange direkt:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Omsluter den raden i en [telemetriinitieraren](../../azure-monitor/app/api-custom-events-metrics.md#defaults) att kontrollera att alla TelemetryClient-instanser är konsekvent.
* [ASP.NET] Ange version `BuildInfo.config`. Webbmodulen tar över versionen från noden BuildLabel. Inkludera den här filen i projektet och Kom ihåg att ange egenskapen Kopiera alltid i Solution Explorer.

    ```XML

    <?xml version="1.0" encoding="utf-8"?>
    <DeploymentEvent xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/VisualStudio/DeploymentEvent/2013/06">
      <ProjectName>AppVersionExpt</ProjectName>
      <Build type="MSBuild">
        <MSBuild>
          <BuildLabel kind="label">1.0.0.2</BuildLabel>
        </MSBuild>
      </Build>
    </DeploymentEvent>

    ```
* [ASP.NET] Generera BuildInfo.config automatiskt i MSBuild. Gör detta genom att lägga till några rader till din `.csproj` fil:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Detta genererar en fil med namnet *yourProjectName*. BuildInfo.config. Publiceringsprocessen byter namn på den till BuildInfo.config.

    Build-etiketten innehåller platshållare (AutoGen_...) när du skapar med Visual Studio. Men när skapats med MSBuild, fylls den med rätt versionsnumret.

    Om du vill tillåta MSBuild att generera versionsnummer, anger du versionen som `1.0.*` i AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. I filen .csproj lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md) eller när du [undersöker mätvärden](../../azure-monitor/app/metrics-explorer.md).

Observera dock att build-versionsnumret endast genereras av Microsoft Build Engine, och inte av utvecklarversionen i Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Azure DevOps kan du [få en anteckningsmarkör](../../azure-monitor/app/annotations.md) tillagd i diagrammen när du släpper en ny version. Följande bild visar hur markeringen visas.

![Skärmbild av exempel på versionsanteckning i ett diagram](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Nästa steg

* [Delade resurser för flera roller](../../azure-monitor/app/app-map.md)
* [Skapa en telemetri-initierare för att skilja mellan en | B-varianter](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
