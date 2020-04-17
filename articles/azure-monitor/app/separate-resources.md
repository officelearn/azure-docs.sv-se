---
title: Separera telemetri i Azure Application Insights
description: Direkt telemetri till olika resurser för utveckling, test och produktionsstämplar.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 565d51751ad50479f4e227b6855ac63b80bd949e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536785"
---
# <a name="separating-telemetry-from-development-test-and-production"></a>Separera telemetri från utveckling, test och produktion

När du utvecklar nästa version av ett webbprogram vill du inte blanda ihop [telemetrin Application Insights](../../azure-monitor/app/app-insights-overview.md) från den nya versionen och den redan släppta versionen. För att undvika förvirring skickar du telemetrin från olika utvecklingsstadier för att separera Application Insights-resurser med separata instrumenteringsnycklar (ikeys). För att göra det enklare att ändra instrumenteringsnyckeln när en version flyttas från ett stadium till ett annat, kan det vara bra att ställa in ikey i kod i stället för i konfigurationsfilen. 

(Om ditt system är en Azure Cloud Service finns det [en annan metod för att ange separata ikeys](../../azure-monitor/app/cloudservices.md).)

## <a name="about-resources-and-instrumentation-keys"></a>Om resurser och instrumenteringsnycklar

När du konfigurerar programstatistikövervakning för din webbapp skapar du en Application *Insights-resurs* i Microsoft Azure. Du öppnar den här resursen i Azure-portalen för att se och analysera telemetri som samlats in från din app. Resursen identifieras med en *instrumenteringsnyckel* (ikey). När du installerar Application Insights-paketet för att övervaka din app konfigurerar du det med instrumenteringsnyckeln, så att den vet var telemetrin ska skickas.

Du väljer vanligtvis att använda separata resurser eller en enda delad resurs i olika scenarier:

* Olika, oberoende program - Använd en separat resurs och ikey för varje app.
* Flera komponenter eller roller i ett affärsprogram – Använd en [enda delad resurs](../../azure-monitor/app/app-map.md) för alla komponentappar. Telemetri kan filtreras eller segmenteras av egenskapen cloud_RoleName.
* Utveckling, test och release - Använd en separat resurs och ikey för versioner av systemet i "stämpel" eller produktionsstadium.
* A | B-testning - Använd en enda resurs. Skapa en TelemetryInitializer för att lägga till en egenskap i telemetrin som identifierar varianterna.


## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamisk instrumenteringsnyckel

Om du vill göra det enklare att ändra ikey när koden flyttas mellan produktionsstadier anger du den i kod i stället för i konfigurationsfilen.

Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

I det här exemplet placeras ikeys för de olika resurserna i olika versioner av webbkonfigurationsfilen. Byta webbkonfigurationsfilen - som du kan göra som en del av release skriptet - kommer att byta målresursen.

### <a name="web-pages"></a>Webbsidor
Den iKey används också i appens webbsidor, i [skriptet som du fick från snabbstart blad](../../azure-monitor/app/javascript.md). Istället för att koda det bokstavligen i skriptet, generera den från servern tillstånd. Till exempel i en ASP.NET app:

*JavaScript i Rakhyvel*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      "@Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="create-additional-application-insights-resources"></a>Skapa ytterligare programinsiktsresurser
Om du vill separera telemetri för olika programkomponenter eller för olika stämplar (dev/test/production) för samma komponent måste du skapa en ny Application Insights-resurs.

Lägg till en application insights-resurs i [portal.azure.com:](https://portal.azure.com)

![Klicka på Nytt, Application Insights](./media/separate-resources/01-new.png)

* **Programtypen** påverkar vad du ser på översiktsbladet och de egenskaper som är tillgängliga i [måttutforskaren](../../azure-monitor/platform/metrics-charts.md). Om du inte ser din typ av app väljer du en av webbtyperna för webbsidor.
* **Resursgrupp** är en bekvämlighet för att hantera egenskaper som [åtkomstkontroll](../../azure-monitor/app/resources-roles-access-control.md). Du kan använda separata resursgrupper för utveckling, test och produktion.
* **Prenumeration** är ditt betalkonto i Azure.
* **Plats** är där vi för dina uppgifter. För närvarande kan det inte ändras. 
* **Lägg till instrumentpanelen** placerar en snabbåtkomstpanel för din resurs på din Startsida i Azure. 

Det tar några sekunder att skapa resursen. Du ser en avisering när det är klart.

(Du kan skriva ett [PowerShell-skript](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) för att skapa en resurs automatiskt.)

### <a name="getting-the-instrumentation-key"></a>Hämta instrumenteringsnyckeln
Instrumenteringsnyckeln identifierar den resurs som du skapade. 

![Klicka på Essentials, klicka på instrumenteringsnyckeln, CTRL+C](./media/separate-resources/02-props.png)

Du behöver instrumenteringsnycklarna för alla resurser som appen skickar data till.

## <a name="filter-on-build-number"></a>Filtrera på byggnummer
När du publicerar en ny version av appen vill du kunna separera telemetrin från olika versioner.

Du kan ange egenskapen Programversion så att du kan filtrera [sökresultat](../../azure-monitor/app/diagnostic-search.md) [och måttutforskarens](../../azure-monitor/platform/metrics-charts.md) resultat.

![Filtrering på en egenskap](./media/separate-resources/050-filter.png)

Det finns flera olika metoder för att ange egenskapen Programversion.

* Ställ in direkt:

    `telemetryClient.Context.Component.Version = typeof(MyProject.MyClass).Assembly.GetName().Version;`
* Radbryta den raden i en [telemetriinitierare](../../azure-monitor/app/api-custom-events-metrics.md#defaults) för att säkerställa att alla TelemetryClient-instanser anges konsekvent.
* - Jag vet inte vad du ASP.NET. Ställ in `BuildInfo.config`versionen i . Webbmodulen hämtar versionen från buildlabel-noden. Inkludera den här filen i projektet och kom ihåg att ange egenskapen Kopiera alltid i Solution Explorer.

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
* - Jag vet inte vad du ASP.NET. Generera BuildInfo.config automatiskt i MSBuild. Det gör du genom att `.csproj` lägga till några rader i filen:

    ```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
    ```

    Detta genererar en fil som kallas *ditt Projektnamn*. BuildInfo.config. Publiceringsprocessen byter namn till BuildInfo.config.

    Byggetiketten innehåller en platshållare (AutoGen_...) när du bygger med Visual Studio. Men när den byggs med MSBuild fylls den med rätt versionsnummer.

    Om du vill att MSBuild ska `1.0.*` kunna generera versionsnummer anger du versionen som i AssemblyReference.cs

## <a name="version-and-release-tracking"></a>Spårning av versionen och utgåva
Om du vill kunna spåra programversionen, se till att `buildinfo.config` genereras av Microsoft Build Engine-processen. Lägg `.csproj` till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup>
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar](../../azure-monitor/app/diagnostic-search.md) eller när du [undersöker mätvärden](../../azure-monitor/platform/metrics-charts.md).

Observera dock att versionsnumret för bygget genereras endast av Microsoft Build Engine, inte av utvecklarversionen från Visual Studio.

### <a name="release-annotations"></a>Versionsanteckningar
Om du använder Azure DevOps kan du [få en anteckningsmarkör](../../azure-monitor/app/annotations.md) tillagd i dina diagram när du släpper en ny version. Följande bild visar hur markeringen visas.

![Skärmbild av exempel på versionsanteckning i ett diagram](media/separate-resources/release-annotation.png)
## <a name="next-steps"></a>Nästa steg

* [Delade resurser för flera roller](../../azure-monitor/app/app-map.md)
* [Skapa en telemetriinitierare för att skilja A| B-varianter](../../azure-monitor/app/api-filtering-sampling.md#add-properties)
