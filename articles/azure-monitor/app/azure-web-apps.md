---
title: Övervaka prestanda för Azure-tjänster | Microsoft Docs
description: Övervakning av programprestanda för Azure app services. Skapa diagram över belastningen och svarstid, beroendeinformation och Ställ in aviseringar på prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: ec5b3572cbf74bad9b82eb93a45d7a4664023b95
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408223"
---
# <a name="monitor-azure-app-service-performance"></a>Övervaka prestanda i Azure App Service

Aktivera övervakning på .NET och .NET Core baserade webbprogram som körs [Azure App Services](https://docs.microsoft.com/azure/app-service/) är nu enklare än någonsin. Tidigare nödvändigt att manuellt installera ett webbplatstillägg, är den senaste versionen tillägg/agenten inbyggt i app service-avbildning som standard. Den här artikeln kommer vägleder dig genom att aktivera Application Insights-övervakning samt ge preliminär vägledning för att automatisera processen för storskaliga distributioner.

> [!NOTE]
> Att manuellt lägga till en Application Insights-webbplatstillägg via **utvecklingsverktyg** > **tillägg** är inaktuell. Den här metoden för installation av tillägg var beroende av manuella uppdateringar för varje ny version. Den senaste stabila versionen av tillägget finns nu [förinstallerad](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) som en del av App Service-avbildning. Filerna finns i `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` och uppdateras automatiskt med varje stabila versionen. Om du följer anvisningarna för agenten baserat att aktivera övervakning nedan, raderas föråldrad tillägget automatiskt åt dig.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera av program för Azure App Services som värd för program:

* **Agentbaserad programövervakning** (ApplicationInsightsAgent).  
    * Den här metoden är den enklaste att aktivera och inga avancerad konfiguration krävs. Det är ofta kallas ”” realtidsövervakning. Vi rekommenderar minst att aktivera den här nivån för övervakning för Azure App Services och sedan baserat på ditt specifika scenario du kan utvärdera om mer avancerad övervakning via manuella instrumentation krävs.

* **Manuellt instrumentera programmet med hjälp av kod** genom att installera Application Insights SDK.

    * Den här metoden är mycket mer anpassningsbar, men det krävs [att lägga till ett beroende på Application Insights SDK NuGet-paket](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Den här metoden innebär också att du behöver hantera uppdateringar till den senaste versionen av paket som dig själv.

    * Om du vill göra anpassade API-anrop till spåra händelser/beroenden inte fångas in som standard med agent-baserad övervakning, skulle du behöva använda den här metoden. Kolla in den [API för anpassade händelser och mått artikeln](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) vill veta mer.

> [!NOTE]
> Om både agenten baserat övervakning och manuella SDK baserad instrumentation identifieras endast manuell instrumentation inställningar kommer att användas. Det här är att förhindra att duplicerade data som skickas från. Mer information om den här Kolla in den [avsnittet om felsökning](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) nedan.

## <a name="enable-agent-based-monitoring-net"></a>Aktivera agentbaserad övervakning .NET

> [!NOTE]
> kombinationen av APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte. Mer information finns i förklaring i den [avsnittet om felsökning](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Välj Application Insights** i Azure-Kontrollpanelen för app service.

    ![Välj Application Insights under inställningar](./media/azure-web-apps/settings-app-insights-01.png)

   * Välja att skapa en ny resurs, såvida inte du redan har konfigurerat en Application Insights-resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** att skapa den nya resursen som du kommer att uppmanas att **tillämpa övervakningsinställningar**. Att välja **Fortsätt** länkar nya Application Insights-resursen till app service, gör du så kommer också **utlösa en omstart av app service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. Du kan välja hur du vill att application insights för att samla in data per plattform för ditt program när du har angett vilken resurs som ska användas. Övervaka ASP.NET-app är på som standard med två olika nivåer för insamling av.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new.png)

   * .NET **grundläggande samling** nivå erbjuder grundläggande enkelinstanser APM-funktioner.

   * .NET **rekommenderas samling** nivå:
       * Lägger till trender för processor, minne och i/o-användning.
       * Korrelerar micro-tjänster över begäran/beroende gränser.
       * Samlar in användningstrender och gör det möjligt för korrelation från tillgänglighetsresultat med transaktioner.
       * Samlar in undantag som ohanterat av värdprocessen.
       * Förbättrar precisionen för APM-måtten under belastning, när sampling används.

3. Om du vill konfigurera inställningar som exempelvis sampling, som du kan kontrollera tidigare via filen applicationinsights.config kan du nu interagera med samma inställningar via programinställningar med ett motsvarande prefix. 

    * Om du vill ändra inledande samplingsprocenten du till exempel skapa en programinställning av: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` och värdet `100`.

    * Listan över stöds Adaptiv sampling telemetriinställningar för processor, finns det [kod](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) och [associerade dokumentation](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Aktivera agentbaserad övervakning .NET Core

Följande versioner av .NET Core stöds: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Riktar in sig på fullständiga framework från .NET Core, fristående distribution och ASP.NET Core 3.0 finns för närvarande **stöds inte** med /-agenttillägg baserad övervakning. ([Manuell instrumentation](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via kod fungerar i alla scenarier som tidigare.)

1. **Välj Application Insights** i Azure-Kontrollpanelen för app service.

    ![Välj Application Insights under inställningar](./media/azure-web-apps/settings-app-insights-01.png)

   * Välja att skapa en ny resurs, såvida inte du redan har konfigurerat en Application Insights-resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** att skapa den nya resursen som du kommer att uppmanas att **tillämpa övervakningsinställningar**. Att välja **Fortsätt** länkar nya Application Insights-resursen till app service, gör du så kommer också **utlösa en omstart av app service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. Du kan välja hur du vill att Application Insights för att samla in data per plattform för ditt program när du har angett vilken resurs som ska användas. .NET core erbjuder **rekommenderas samling** eller **inaktiverad** för .NET Core 2.0, 2.1 och 2.2.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Aktivera övervakning på klientsidan-.NET

Övervakning på klientsidan är Anmäl dig till ASP.NET. Aktivera övervakning på klientsidan:

* Välj **inställningar** > ** ** program inställningar ***
   * Lägga till en ny under inställningar **app inställningsnamn** och **värdet**:

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`true`

   * **Spara** inställningarna och **starta om** din app.

![Skärmdump av programmet inställningar UI](./media/azure-web-apps/appinsights-javascript-enabled.png)

Inaktivera klientsidan övervakningsparet antingen ta bort den associerade nyckelvärdet från programinställningarna eller ange värdet till false.

## <a name="enable-client-side-monitoring-net-core"></a>Aktivera klientsidan övervaka .NET Core

Övervakning på klientsidan är **aktiverad som standard** för .NET Core-appar med **rekommenderas samling**, oavsett om appinställningen 'APPINSIGHTS_JAVASCRIPT_ENABLED' är närvarande.

Om du av någon anledning skulle du vilja inaktivera övervakning på klientsidan:

* Välj **inställningar** > **programinställningar**
   * Lägga till en ny under inställningar **app inställningsnamn** och **värdet**:

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`false`

   * **Spara** inställningarna och **starta om** din app.

![Skärmdump av programmet inställningar UI](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatisera övervakning

Endast programinställningarna måste anges för att aktivera telemetriinsamling av med Application Insights:

   ![App Service-programinställningar med tillgängliga Application Insights-inställningar](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Inställningar för programdefinitioner

|App inställningsnamn |  Definition | Värde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Viktigaste tillägg som styr realtidsövervakning. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  I är läge, men viktiga funktioner aktiverade för att säkerställa optimala prestanda. | `default` eller `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Styr om motorn för binary-omskrivning `InstrumentationEngine` aktiveras. Den här inställningen har prestanda och påverkar kalla start/starttiden. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Kontrollerar om SQL och Azure table text ska hämtas tillsammans med beroendeanrop. Prestandavarning: den här inställningen kräver det `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Programinställningar för App Service med Azure Resource Manager

Programinställningar för App Services kan hanteras och konfigurerad med [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Den här metoden kan användas när du distribuerar nya App Service-resurser med Azure Resource Manager automation eller för att ändra inställningarna för befintliga resurser.

Den grundläggande strukturen i programinställningarna JSON för en app service är nedan:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Ett exempel på en Azure Resource Manager-mall med inställningar för program som konfigurerats för Application Insights, detta [mall](https://github.com/Andrew-MSFT/BasicImageGallery) kan vara användbart, särskilt avsnittet från och med [rad 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisera skapandet av en Application Insights-resurs och en länk till din nya App Service.

Starta processen för att skapa en Azure Resource Manager-mall med alla Application Insights standardinställningarna, som om du tänker skapa en ny Webbapp med Application Insights som aktiveras.

Välj **automatiseringsalternativ**

   ![App Service web app skapas menyn](./media/azure-web-apps/create-web-app.png)

Det här alternativet genererar den senaste Azure Resource Manager-mallen med alla nödvändiga inställningar som konfigurerats.

  ![App Service web app-mall](./media/azure-web-apps/arm-template.png)

Nedan visas ett exempel, Ersätt alla förekomster av `AppMonitoredSite` med platsnamnet på din:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Mallen genererar programinställningar i ”standard”-läge. Det här läget är prestandaoptimerad, men du kan ändra mallen för att aktivera de funktioner som du föredrar.

### <a name="enabling-through-powershell"></a>Aktivera via PowerShell

Endast de underliggande programinställningarna behöver ändras för att aktivera programövervakning via PowerShell. Nedan visas ett exempel som gör att övervakningen för en webbplats som kallas ”AppMonitoredSite” i resursgruppen ”AppMonitoredRG”, och konfigurerar data som ska skickas till ”012345678-abcd-ef01-2345-6789abcd” instrumenteringsnyckeln.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Uppgradera tillägget/övervakningsagent

### <a name="upgrading-from-versions-289-and-up"></a>Uppgradering från versioner 2.8.9 och uppåt

Uppgradering från version 2.8.9 sker automatiskt, utan några ytterligare åtgärder. Ny övervakning bits levereras i bakgrunden till target app service och program startar om de hämtas.

Om du vill kontrollera vilken version av tillägget kör du besök `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Skärmbild av url-sökväg http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uppgradera från versionerna 1.0.0 - 2.6.5

Från och med version 2.8.9 används förinstallerade site-tillägget. Om du är en tidigare version, kan du uppdatera via ett av två sätt:

* [Uppgradera genom att aktivera via portalen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Även om du har Application Insights-tillägget för Azure App Service är installerat Användargränssnittet visas bara **aktivera** knappen. I bakgrunden tas gamla privata webbplatstillägg bort.)

* [Uppgradera via PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Ange programinställningar aktivera förinstallerade webbplatstillägg ApplicationInsightsAgent. Se [aktivering via powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ta manuellt bort tillägget privat plats med namnet Application Insights-tillägget för Azure App Service.

Om uppgraderingen görs från en tidigare version än 2.5.1, kontrollerar du att ApplicationInsigths DLL-filer har tagits bort från Papperskorgen programmet [Se felsökningssteg](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Felsökning

Visas nedan vår stegvisa felsökningsguiden för tillägget/agenten baserat övervakning för .NET och .NET Core-baserade program som körs på Azure App Services.

> [!NOTE]
> Java och Node.js-program kan bara användas på Azure App Services via manuella SDK baserat instrumentation och därför stegen nedan gäller inte för dessa scenarier.

1. Kontrollera att programmet övervakas via `ApplicationInsightsAgent`.
    * Kontrollera att `ApplicationInsightsAgent_EXTENSION_VERSION` appinställningen anges till värdet ”~ 2”.
2. Kontrollera att programmet uppfyller kraven som ska övervakas.
    * Bläddra till `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Skärmbild av https://yoursitename.scm.azurewebsites/applicationinsights resultatsidan](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bekräfta att den `Application Insights Extension Status` är `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Om den inte körs, följer du de [aktivera Application Insights-övervakning instruktioner](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Bekräfta att status källan finns och att den ser ut som: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Om ett liknande värde inte är tillgänglig, innebär det programmet körs inte eller stöds inte. För att säkerställa att programmet körs, gå till manuellt url/application programslutpunkter, som gör att runtime-informationen ska bli tillgänglig.

    * Bekräfta att `IKeyExists` är `true`
        * Om det är FALSKT, lägger du till ' APPINSIGHTS_INSTRUMENTATIONKEY med din ikey guid i dina programinställningar.

    * Bekräfta att det inte finns några poster med `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, och `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Om någon av dessa poster finns, ta bort följande paket från ditt program: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, och `Microsoft.AspNet.TelemetryCorrelation`.

Tabellen nedan innehåller en mer detaljerad förklaring av vad de här värdena betyder, deras underliggande orsakar och rekommenderade korrigeringar:

|Värdet för problem|Förklaring|Korrigera
|---- |----|---|
| `AppAlreadyInstrumented:true` | Det här värdet anger att tillägget upptäckte att någon aspekt av SDK: N finns redan i programmet och kommer backoff. Det kan bero på en referens till `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, eller `Microsoft.ApplicationInsights`  | Ta bort referenserna. Vissa av dessa referenser har lagts till som standard från vissa Visual Studio-mallar och äldre versioner av Visual Studio kan lägga till referenser till `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Om programmet är inställd på .NET Core 2.1 eller 2.2 och refererar till [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-paketet, sedan in i Application Insights och tillägget kommer backoff. | Kunder på .NET Core 2.1,2.2 [rekommenderas](https://github.com/aspnet/Announcements/issues/287) kan använda Microsoft.AspNetCore.App meta-paketet i stället.|
|`AppAlreadyInstrumented:true` | Det här värdet kan också orsakas av förekomsten av ovanstående DLL-filer i mappen från en tidigare distribution. | Rensa mappen för att säkerställa att dessa DLL-filer har tagits bort.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Det här värdet anger att tillägget identifierat referenser till `Microsoft.AspNet.TelemetryCorrelation` i programmet, och kommer backoff. | Ta bort referensen.
|`AppContainsDiagnosticSourceAssembly**:true`|Det här värdet anger att tillägget identifierat referenser till `System.Diagnostics.DiagnosticSource` i programmet, och kommer backoff.| Ta bort referensen.
|`IKeyExists:false`|Det här värdet anger att instrumentationsnyckeln saknas i AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY`. Möjliga orsaker: Värdena kan ha tagits bort av misstag, har glömt att ange värden i automationsskript osv. | Kontrollera att inställningen finns i programinställningarna App Service.

### <a name="appinsightsjavascriptenabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte

Om du använder APPINSIGHTS_JAVASCRIPT_ENABLED = true i fall där innehållet är kodat, kan du få fel som: 

- 500 URL-omskrivningsregler fel
- 500.53 URL rewrite modulfel med meddelandet utgående omarbetning regler kan inte användas när innehållet i HTTP-svaret är kodade (”gzip-). 

Detta anges på grund av APPINSIGHTS_JAVASCRIPT_ENABLED inställningen till true och Innehållskodning som finns på samma gång. Det här scenariot stöds inte ännu. Lösningen är att ta bort APPINSIGHTS_JAVASCRIPT_ENABLED från programinställningarna. Tyvärr innebär det att om klientens/webbläsaren JavaScript instrumentation fortfarande krävs, behövs manuell SDK-referenser för dina webbsidor. Följ den [instruktioner](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) för manuell instrumentering med JavaScript SDK.

Den senaste informationen om Application Insights-agenten/tillägget finns i [viktig](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../platform/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../platform/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.
