---
title: Övervaka Azure App Services-prestanda | Microsoft Docs
description: Övervakning av program prestanda för Azure App Services. Diagrammets inläsnings-och svars tid, beroende information och ange aviseringar för prestanda.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: mbullwin
ms.openlocfilehash: ec741c0051ccd8020b7d7ab689e15add3ad716bd
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286174"
---
# <a name="monitor-azure-app-service-performance"></a>Övervaka Azure App Service prestanda

Nu är det enklare än någonsin att aktivera övervakning i ASP.NET och ASP.NET Core baserade webb program som körs på [Azure App Services](https://docs.microsoft.com/azure/app-service/) . Tidigare var du tvungen att installera ett plats tillägg manuellt, det senaste tillägget/agenten är nu inbyggt i App Service-avbildningen som standard. Den här artikeln vägleder dig genom att aktivera Application Insights övervakning och ge preliminär vägledning för automatisering av processen för storskaliga distributioner.

> [!NOTE]
> Att manuellt lägga till ett Application Insights webbplats tillägg via **utvecklingsverktyg** > -**tillägg** är föråldrat. Den här metoden för tilläggs installation var beroende av manuella uppdateringar för varje ny version. Den senaste stabila versionen av tillägget är nu [förinstallerad](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) som en del av App Service avbildningen. Filerna finns i `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` och uppdateras automatiskt med varje stabil utgåva. Om du följer agentbaserade instruktioner för att aktivera övervakning nedan tas det inaktuella tillägget bort automatiskt.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera program övervakning för Azure App Services-värdbaserade program:

* **Agent-baserad program övervakning** (ApplicationInsightsAgent).  
    * Den här metoden är det enklaste sättet att aktivera, och ingen Avancerad konfiguration krävs. Det kallas ofta övervakning av körnings miljön. För Azure App tjänster rekommenderar vi att du minst aktiverar den här övervaknings nivån och sedan baserat på ditt speciella scenario kan du utvärdera om mer avancerad övervakning genom manuell Instrumentation krävs.

* Att **instrumentera programmet manuellt** genom att installera Application Insights SDK.

    * Den här metoden är mycket mer anpassningsbar, men kräver [att du lägger till ett beroende på Application Insights SDK NuGet-paket](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Den här metoden innebär också att du måste hantera uppdateringarna till den senaste versionen av paketen själv.

    * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte har registrerats som standard med en agent-baserad övervakning, behöver du använda den här metoden. Mer information finns i [artikeln om API för anpassade händelser och mått](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Om både agentbaserade övervakning och manuellt SDK-baserad Instrumentation identifieras, kommer endast de manuella Instrumentation-inställningarna att användas. Detta är för att förhindra att duplicerade data skickas. Mer information om detta finns i [fel söknings avsnittet](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) nedan.

## <a name="enable-agent-based-monitoring-for-net-applications"></a>Aktivera agent-baserad övervakning av .NET-program

> [!NOTE]
> Kombinationen av APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte. Mer information finns i förklaringen i [fel söknings avsnittet](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Välj Application Insights** på kontroll panelen i Azure för din app service.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, om du inte redan har konfigurerat en Application Insights resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervaknings inställningarna**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din app service. då **aktive ras även en omstart av App Service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill att Application Insights ska samla in data per plattform för ditt program. ASP.NET app Monitoring är aktiverat som standard med två olika nivåer av samlingen.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new.png)

   * .NET **Basic Collection** -nivån erbjuder grundläggande APM-funktioner med en instans.

   * Rekommenderad .NET- **samlings** nivå:
       * Lägger till trender för CPU, minne och I/O-användning.
       * Korrelerar mikrotjänster mellan begär Anden/beroende gränser.
       * Samlar in användnings trender och gör det möjligt att korrelation från tillgänglighets resultat till transaktioner.
       * Samlar in undantag som hanteras av värd processen.
       * Förbättrar precisionen för APM-värden under belastning när sampling används.

3. Om du vill konfigurera inställningar som sampling, som du tidigare kan styra via filen applicationinsights. config, kan du nu interagera med samma inställningar via program inställningar med ett motsvarande prefix. 

    * Om du till exempel vill ändra den inledande samplings procenten kan du skapa en program inställning av: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` och värdet `100`.

    * Om du vill visa en lista över stödda alternativ för adaptiva typer av telemetri för telemetri, kan du läsa igenom [koden](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) och [tillhör ande dokumentation](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-for-net-core-applications"></a>Aktivera agent-baserad övervakning av .NET Core-program

Följande versioner av .NET Core stöds: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2

Att rikta in sig på hela ramverket från .NET Core, fristående distribution och ASP.NET Core 3,0 stöds för närvarande **inte** med agent/tillägg-baserad övervakning. ([Manuell instrumentering](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via kod fungerar i alla tidigare scenarier.)

1. **Välj Application Insights** på kontroll panelen i Azure för din app service.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, om du inte redan har konfigurerat en Application Insights resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervaknings inställningarna**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din app service. då **aktive ras även en omstart av App Service**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill Application Insights Samla in data per plattform för ditt program. .NET Core erbjuder **rekommenderad samling** eller **inaktive rad** för .net Core 2,0, 2,1 och 2,2.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-for-net-applications"></a>Aktivera övervakning på klient sidan för .NET-program

Övervakning på klient sidan är valbar för ASP.NET. Aktivera övervakning på klient sidan:

* Välj **inställningar** > * * * * program inställningar * * * *
   * Under program inställningar lägger du till ett nytt namn och **värde**för **appens inställning** :

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`true`

   * **Spara** inställningarna och **starta om** din app.

![Skärm bild av användar gränssnittet för program inställningar](./media/azure-web-apps/appinsights-javascript-enabled.png)

Om du vill inaktivera övervakning på klient sidan tar du antingen bort det associerade nyckel värdes paret från program inställningarna eller anger värdet till falskt.

## <a name="enable-client-side-monitoring-for-net-core-applications"></a>Aktivera övervakning på klient sidan för .NET Core-program

Övervakning på klient sidan är **aktiverat som standard** för .net Core-appar med den **rekommenderade samlingen**, oavsett om app-inställningen "APPINSIGHTS_JAVASCRIPT_ENABLED" finns.

Om du av någon anledning vill inaktivera övervakning på klient sidan:

* Välj **inställningar** > **program inställningar**
   * Under program inställningar lägger du till ett nytt namn och **värde**för **appens inställning** :

     Namn: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`false`

   * **Spara** inställningarna och **starta om** din app.

![Skärm bild av användar gränssnittet för program inställningar](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatisera övervakning

För att kunna aktivera telemetri-samling med Application Insights, behöver du bara ange program inställningarna:

   ![App Service program inställningar med tillgängliga Application Insights inställningar](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definitioner av program inställningar

|Namn på App-inställning |  Definition | Värde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Huvud tillägg som styr körnings övervakning. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  I standard läget är de viktigaste funktionerna aktiverade för att säkerställa optimala prestanda. | `default` eller `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Kontrollerar om den binära omskrivnings motorn `InstrumentationEngine` aktive ras. Den här inställningen har prestanda konsekvenser och påverkar start-och start tid för kall. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Kontrollerar om SQL & Azure Table text kommer att samlas in tillsammans med beroende anrop. Prestanda varning: den här inställningen kräver `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service program inställningar med Azure Resource Manager

Program inställningar för App Services kan hanteras och konfigureras med [Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Den här metoden kan användas när du distribuerar nya App Service-resurser med Azure Resource Manager Automation, eller för att ändra inställningarna för befintliga resurser.

Den grundläggande strukturen i program inställnings-JSON för en app service är nedan:

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

För ett exempel på en Azure Resource Manager-mall med program inställningar som kon figurer ATS för Application Insights, kan den här [mallen](https://github.com/Andrew-MSFT/BasicImageGallery) vara till hjälp, särskilt avsnittet som börjar på [rad 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisera skapandet av en Application Insights-resurs och länka till din nyligen skapade App Service.

Om du vill skapa en Azure Resource Manager-mall med alla standardinställningar för Application Insights som kon figurer ATS startar du processen som om du skulle skapa en ny webbapp med Application Insights aktive rad.

Välj **automatiserings alternativ**

   ![App Service-menyn för att skapa webbappar](./media/azure-web-apps/create-web-app.png)

Med det här alternativet skapas den senaste Azure Resource Manager-mallen med alla obligatoriska konfigurerade inställningar.

  ![Mall för App Service-webbapp](./media/azure-web-apps/arm-template.png)

Nedan visas ett exempel som ersätter alla instanser av `AppMonitoredSite` med ditt webbplats namn:

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
> Mallen kommer att generera program inställningar i "standard läge". Det här läget är optimerad för prestanda, men du kan ändra mallen för att aktivera de funktioner som du föredrar.

### <a name="enabling-through-powershell"></a>Aktivera via PowerShell

För att kunna aktivera program övervakning via PowerShell måste bara de underliggande program inställningarna ändras. Nedan visas ett exempel som aktiverar program övervakning för en webbplats med namnet "AppMonitoredSite" i resurs gruppen "AppMonitoredRG" och konfigurerar data som ska skickas till Instrumentation-nyckeln "012345678-ABCD-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Uppgradera övervaknings tillägg/agent

### <a name="upgrading-from-versions-289-and-up"></a>Uppgradera från versioner 2.8.9 och uppåt

Uppgradering från version 2.8.9 sker automatiskt, utan ytterligare åtgärder. De nya övervaknings bitarna levereras i bakgrunden till mål program tjänsten och när programmet startas om kommer de att hämtas.

Du kan kontrol lera vilken version av tillägget som du kör på `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Skärm bild av URL-sökväg http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uppgradera från versioner 1.0.0 – 2.6.5

Från och med version 2.8.9 används det förinstallerade webbplats tillägget. Om du har en tidigare version kan du uppdatera via ett av följande två sätt:

* [Uppgradera genom att aktivera via portalen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Även om du har Application Insights tillägget för Azure App Service installerat visas bara knappen **Aktivera** i användar gränssnittet. Under bakgrunden tas det gamla privata webbplats tillägget bort.)

* [Uppgradera via PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Ange program inställningarna för att aktivera det förinstallerade ApplicationInsightsAgent för webbplats tillägget. Se [Aktivera via PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ta bort det privata webbplats tillägget manuellt med namnet Application Insights tillägget för Azure App Service.

Om uppgraderingen görs från en version före 2.5.1 kontrollerar du att DLL-filerna för ApplicationInsigths tas bort från mappen för program bin [se fel söknings steg](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Felsöka

Nedan visas vår stegvisa fel söknings guide för tillägg/agent-baserad övervakning av .NET-och .NET Core-baserade program som körs på Azure App Services.

> [!NOTE]
> Java-och Node. js-program stöds bara på Azure App tjänster via manuell SDK-baserad Instrumentation, och därför gäller inte stegen nedan för dessa scenarier.

> [!NOTE]
> ASP.NET Core 3,0-program stöds inte. Följ den [manuella instrumentering](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via kod för ASP.net Core 3,0-appar.

1. Kontrol lera att programmet övervakas via `ApplicationInsightsAgent`.
    * Kontrol lera att app-inställningen för `ApplicationInsightsAgent_EXTENSION_VERSION` har värdet "~ 2".
2. Kontrol lera att programmet uppfyller de krav som ska övervakas.
    * Bläddra till `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Skärm bild av https://yoursitename.scm.azurewebsites/applicationinsights resultat sida](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bekräfta att `Application Insights Extension Status` är `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Om den inte körs följer du anvisningarna för [att aktivera Application Insights övervakning](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Bekräfta att status källan finns och ser ut så här: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Om ett liknande värde inte finns, innebär det att programmet inte körs eller inte stöds. För att säkerställa att programmet körs kan du försöka gå till programmets URL/program slut punkter manuellt, vilket gör att körnings informationen blir tillgänglig.

    * Bekräfta att `IKeyExists` är `true`
        * Om det är falskt lägger du till "APPINSIGHTS_INSTRUMENTATIONKEY med ditt iKey-GUID i dina program inställningar.

    * Bekräfta att det inte finns några poster för `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` och `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Om någon av dessa poster finns tar du bort följande paket från programmet: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` och `Microsoft.AspNet.TelemetryCorrelation`.

Tabellen nedan innehåller en mer detaljerad förklaring av vad dessa värden innebär, deras underliggande orsaker och rekommenderade korrigeringar:

|Problem värde|Förklaring|Löser
|---- |----|---|
| `AppAlreadyInstrumented:true` | Det här värdet anger att tillägget har identifierat att en aspekt av SDK: n redan finns i programmet och kommer att stängas av. Det kan bero på en referens till `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` eller `Microsoft.ApplicationInsights`  | Ta bort referenserna. Vissa av dessa referenser läggs till som standard från vissa Visual Studio-mallar och äldre versioner av Visual Studio kan lägga till referenser till `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Om programmet är riktat mot .NET Core 2,1 eller 2,2, och refererar till [Microsoft. AspNetCore. all](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-paket, kommer det att finnas i Application Insights och tillägget kommer att återställas. | Kunder på .NET Core 2.1, 2.2 [rekommenderar](https://github.com/aspnet/Announcements/issues/287) att du använder Microsoft. AspNetCore. app-meta-paketet i stället.|
|`AppAlreadyInstrumented:true` | Det här värdet kan också orsakas av förekomsten av ovanstående DLL-filer i mappen app från en tidigare distribution. | Rensa mappen app för att se till att dessa DLL-filer tas bort. Kontrol lera både den lokala appens bin-katalog och katalogen wwwroot på App Service. (Om du vill kontrol lera katalogen wwwroot i App Service webbappen: avancerade verktyg (kudu) > fel söknings konsolen > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Det här värdet anger att tillägget har identifierat referenser till `Microsoft.AspNet.TelemetryCorrelation` i programmet och kommer att återställas. | Ta bort referensen.
|`AppContainsDiagnosticSourceAssembly**:true`|Det här värdet anger att tillägget har identifierat referenser till `System.Diagnostics.DiagnosticSource` i programmet och kommer att återställas.| Ta bort referensen.
|`IKeyExists:false`|Det här värdet anger att Instrumentation-nyckeln inte finns i AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Möjliga orsaker: värdena kan ha tagits bort av misstag och har glömt att ange värden i Automation-skriptet osv. | Kontrol lera att inställningen finns i App Service programmets inställningar.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte

Om du använder APPINSIGHTS_JAVASCRIPT_ENABLED = true i fall där innehåll är kodat kan du få fel som: 

- 500 Skriv åtgärds fel för URL
- 500,53-URL: en Skriv över modul fel med regler för utgående omskrivning av meddelanden kan inte tillämpas när innehållet i HTTP-svaret är kodat (gzip). 

Detta beror på att program inställningen APPINSIGHTS_JAVASCRIPT_ENABLED anges till true och att innehålls kodning förekommer samtidigt. Det här scenariot stöds inte ännu. Lösningen är att ta bort APPINSIGHTS_JAVASCRIPT_ENABLED från dina program inställningar. Det innebär att om det fortfarande krävs ett JavaScript-instrument för klient-och webb läsar sidan krävs manuella SDK-referenser för dina webb sidor. Följ [instruktionerna](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) för manuell Instrumentation med Java Script SDK.

Den senaste informationen om Application Insights agent/tillägg finns i [viktig](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)information.

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../platform/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../platform/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.
