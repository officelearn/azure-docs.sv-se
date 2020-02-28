---
title: Åtskilj telemetri i Azure Application Insights
description: Dirigera telemetri till olika resurser för utveckling, testning och produktions märken.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 3580d162f4b3955a04ffcd0f13933221bfef3b65
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671468"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Åtskilj telemetri från utveckling, testning och produktion

När du utvecklar nästa version av ett webb program vill du inte blanda [Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetri från den nya versionen och den version som redan har släppts. Undvik förvirring genom att skicka Telemetrin från olika utvecklings steg till att separera Application Insights-resurser med separata instrumentande nycklar (ikeys). För att göra det enklare att ändra Instrumentation-nyckeln när en version flyttas från ett steg till en annan, kan det vara praktiskt att ange iKey i kod i stället för i konfigurations filen. 

(Om systemet är en Azure-moln tjänst finns det [en metod för att ställa in separata ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Om resurser och instrumentande nycklar

När du konfigurerar Application Insights övervakning för din webbapp skapar du en Application Insights *resurs* i Microsoft Azure. Du öppnar den här resursen i Azure Portal för att se och analysera telemetri som samlats in från din app. Resursen identifieras av en *Instrumentation-nyckel* (iKey). När du installerar Application Insights-paketet för att övervaka din app konfigurerar du den med Instrumentation-nyckeln, så att den vet var du vill skicka telemetrit.

Du väljer vanligt vis att använda separata resurser eller en enda delad resurs i olika scenarier:

* Olika, oberoende program – Använd en separat resurs-och iKey för varje app.
* Flera komponenter eller roller för ett företags program – Använd en [enda delad resurs](../../azure-monitor/app/app-map.md) för alla komponent program. Telemetri kan filtreras eller segmenteras av cloud_RoleName-egenskapen.
* Utveckling, testning och lansering – Använd en separat resurs-och iKey för versioner av systemet i "Stamp" eller produktions stadiet.
* En | B-testning – Använd en enda resurs. Skapa en TelemetryInitializer för att lägga till en egenskap i Telemetrin som identifierar varianterna.


## <a name="dynamic-ikey"></a>Dynamisk Instrumentation-nyckel

För att göra det enklare att ändra iKey när koden flyttas mellan produktions faser, ställer du in den i kod i stället för i konfigurations filen.

Ange nyckeln i en initierings metod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

I det här exemplet placeras ikeys för de olika resurserna i olika versioner av webb konfigurations filen. Växling av webb konfigurations filen – som du kan göra som en del av versions skriptet – byter mål resurs.

### <a name="web-pages"></a>Webbsidor
IKey används också i appens webb sidor, i [skriptet som du fick från snabb starts bladet](../../azure-monitor/app/javascript.md). I stället för att koda det bokstavligen i skriptet kan du generera det från Server tillstånd. Till exempel i en ASP.NET-app:

*Java Script i kniv*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Skapa ytterligare Application Insights resurser
För att separera telemetri för olika program komponenter, eller för olika stämplar (utveckling/testning/produktion) av samma komponent, måste du skapa en ny Application Insights-resurs.

Lägg till en Application Insights-resurs i [Portal.Azure.com](https://portal.azure.com):

![Klicka på Nytt, Application Insights](./media/separate-resources/01-new.png)

* **Program typen** påverkar det du ser i översikts bladet och de egenskaper som är tillgängliga i [Metric Explorer](../../azure-monitor/app/metrics-explorer.md). Om du inte ser din typ av app väljer du någon av webb typerna för webb sidor.
* **Resurs gruppen** är en bekvämlighet för att hantera egenskaper som [åtkomst kontroll](../../azure-monitor/app/resources-roles-access-control.md). Du kan använda separata resurs grupper för utveckling, testning och produktion.
* **Prenumeration** är ditt betalnings konto i Azure.
* **Platsen** är den plats där vi behåller dina data. För närvarande går det inte att ändra. 
* **Lägg till på instrument panelen** placerar en snabb åtkomst panel för din resurs på din Azures start sida. 

Det tar några sekunder att skapa resursen. Du ser en avisering när den är färdig.

(Du kan skriva ett [PowerShell-skript](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) för att skapa en resurs automatiskt.)

### <a name="getting-the-instrumentation-key"></a>Hämtar instrumentande nyckel
Instrumentation-nyckeln identifierar den resurs som du har skapat. 

![Klicka på Essentials, klicka på Instrumentation-tangenten, CTRL + C](./media/separate-resources/02-props.png)

Du behöver Instrumentation-nycklarna för alla resurser som din app ska skicka data till.

## <a name="filter-on-build-number"></a>Filter för build-nummer
När du publicerar en ny version av din app vill du kunna separera Telemetrin från olika versioner.

Du kan ställa in egenskapen för program version så att du kan filtrera [Sök](../../azure-monitor/app/diagnostic-search.md) -och [Metric Explorer](../../azure-monitor/app/metrics-explorer.md) -resultat.

![Filtrera efter en egenskap](./media/separate-resources/050-filter.png)

Det finns flera olika metoder för att ange program versions egenskapen.

* Ange direkt:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Radbryt raden i en [telemetri initierare](../../azure-monitor/app/api-custom-events-metrics.md#defaults) för att säkerställa att alla TelemetryClient-instanser anges konsekvent.
* [ASP.NET] Ange versionen i `BuildInfo.config`. Webbmodulen hämtar versionen från BuildLabel-noden. Ta med den här filen i projektet och kom ihåg att ange egenskapen kopiera alltid i Solution Explorer.

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
* [ASP.NET] Generera BuildInfo. config automatiskt i MSBuild. Det gör du genom att lägga till några rader i `.csproj`-filen:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Detta genererar en fil med namnet *yourProjectName*. BuildInfo. config. publicerings processen byter namn till BuildInfo. config.

    Versions etiketten innehåller en plats hållare (AutoGen_...) när du skapar med Visual Studio. Men när det har skapats med MSBuild fylls det i med rätt versions nummer.

    Om du vill tillåta att MSBuild genererar versions nummer ställer du in versionen som `1.0.*` i AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. I `.csproj`-filen lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md) eller när du [undersöker mätvärden](../../azure-monitor/app/metrics-explorer.md).

Observera dock att versions numret för versionen endast genereras av Microsoft build-motorn, inte av utvecklaren från Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Azure DevOps kan du [få en antecknings markör](../../azure-monitor/app/annotations.md) tillagd i dina diagram när du släpper en ny version. Följande bild visar hur markeringen visas.

![Skärmbild av exempel på versionsanteckning i ett diagram](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Nästa steg

* [Delade resurser för flera roller](../../azure-monitor/app/app-map.md)
* [Skapa en telemetri-initierare för att särskilja en | B-varianter](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
