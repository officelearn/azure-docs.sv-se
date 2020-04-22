---
title: Övervaka prestanda för Azure-apptjänster | Microsoft-dokument
description: Övervakning av programmets prestanda för Azure-apptjänster. Diagraminläsning och svarstid, beroendeinformation och ange aviseringar om prestanda.
ms.topic: conceptual
ms.date: 12/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: dd0d3be6ed7e5185183618cc2bdeff5ee8d749f3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729795"
---
# <a name="monitor-azure-app-service-performance"></a>Övervaka Azure App Service-prestanda

Det är nu enklare än någonsin att aktivera övervakning på dina ASP.NET och ASP.NET kärnbaserade webbprogram som körs på [Azure App Services.](https://docs.microsoft.com/azure/app-service/) Medan du tidigare behövde installera ett webbplatstillägg manuellt, är det senaste tillägget/agenten nu inbyggt i apptjänstavbildningen som standard. Den här artikeln kommer att gå igenom aktivera Application Insights övervakning samt ge preliminär vägledning för att automatisera processen för storskaliga distributioner.

> [!NOTE]
> Manuellt lägga till ett program Insights webbplatstillägg via **Utvecklingsverktyg** > **Tillägg** är föråldrad. Den här metoden för tilläggsinstallation var beroende av manuella uppdateringar för varje ny version. Den senaste stabila versionen av tillägget är nu [förinstallerad](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) som en del av App Service-avbildningen. Filerna finns i `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` och uppdateras automatiskt med varje stabil utgåva. Om du följer agentbaserade instruktioner för att aktivera övervakning nedan, kommer det automatiskt att ta bort det inaktuella tillägget för dig.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Det finns två sätt att aktivera programövervakning för Azure App Services-värdprogram:

* **Agentbaserad programövervakning** (ApplicationInsightsAgent).  
    * Den här metoden är den enklaste att aktivera och ingen avancerad konfiguration krävs. Det kallas ofta "runtime" övervakning. För Azure App Services rekommenderar vi att du åtminstone aktiverar den här övervakningsnivån och sedan baserat på ditt specifika scenario kan du utvärdera om mer avancerad övervakning via manuell instrumentering behövs.

* **Manuellt instrumentera programmet genom kod** genom att installera Application Insights SDK.

    * Den här metoden är mycket mer anpassningsbar, men det kräver [att du lägger till ett beroende av Application Insights SDK NuGet-paketen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Den här metoden innebär också att du själv måste hantera uppdateringarna till den senaste versionen av paketen.

    * Om du behöver göra anpassade API-anrop för att spåra händelser/beroenden som inte fångas in som standard med agentbaserad övervakning, måste du använda den här metoden. Kolla in [API för anpassade händelser och mått artikel](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) om du vill veta mer. Detta är också för närvarande det enda alternativet som stöds för Linux-baserade arbetsbelastningar.

> [!NOTE]
> Om både agentbaserad övervakning och manuell SDK-baserad instrumentering upptäcks, kommer endast inställningarna för manuell instrumentering att uppfyllas. Detta för att förhindra att dubblettdata skickas. Mer information om detta finns i [felsökningsavsnittet](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) nedan.

## <a name="enable-agent-based-monitoring"></a>Aktivera agentbaserad övervakning

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Kombinationen av APPINSIGHTS_JAVASCRIPT_ENABLED och urlCompression stöds inte. Mer information finns i förklaringen i [felsökningsavsnittet](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Välj Programstatistik** på Azure-kontrollpanelen för din apptjänst.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, såvida du inte redan har ställt in en Application Insights-resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervakningsinställningar**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din apptjänst, vilket gör det utlöser även **en omstart av apptjänsten**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill att programinsikter ska samlas in data per plattform för ditt program. ASP.NET appövervakning är som standard med två olika insamlingsnivåer.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new.png)
 
 Nedan följer en sammanfattning av data som samlats in för varje rutt:
        
|  | .NET Grundläggande samling | .NET Rekommenderad samling |
| --- | --- | --- |
| Lägger till trender för processor-, minnes- och I/O-användning |Ja |Ja |
| Samlar in användningstrender och aktiverar korrelation från tillgänglighetsresultat till transaktioner | Ja |Ja |
| Samlar in undantag som hanteras av värdprocessen | Ja |Ja |
| Förbättrar precisionen för APM-mått under belastning när sampling används | Ja |Ja |
| Korrelerar mikrotjänster över begärande-/beroendegränser | Nej (endast APM-funktioner med en instans) |Ja |

3. För att konfigurera inställningar som sampling, som du tidigare kunde styra via filen applicationinsights.config kan du nu interagera med samma inställningar via programinställningar med motsvarande prefix. 

    * Om du till exempel vill ändra den första samplingsprocenten kan du skapa en programinställning för: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` och värdet `100`.

    * För listan över anpassade inställningar för anpassad samplingsmetor som stöds kan du läsa [koden](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) och [tillhörande dokumentation](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Följande versioner av .NET Core stöds: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2 ASP.NET Core 3.0

Inriktning på hela ramverket från .NET Core, fristående distribution och Linux-baserade program stöds för närvarande **inte** med agent/tilläggsbaserad övervakning. (Manuell[instrumentering](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via kod kommer att fungera i alla tidigare scenarier.)

1. **Välj Programstatistik** på Azure-kontrollpanelen för din apptjänst.

    ![Under Inställningar väljer du Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Välj att skapa en ny resurs, såvida du inte redan har ställt in en Application Insights-resurs för det här programmet. 

     > [!NOTE]
     > När du klickar på **OK** för att skapa den nya resursen uppmanas du att **tillämpa övervakningsinställningar**. Om du väljer **Fortsätt** länkas din nya Application Insights-resurs till din apptjänst, vilket gör det utlöser även **en omstart av apptjänsten**. 

     ![Instrumentera din webbapp](./media/azure-web-apps/create-resource-01.png)

2. När du har angett vilken resurs som ska användas kan du välja hur du vill att Application Insights ska samla in data per plattform för ditt program. .NET Core erbjuder **Rekommenderad samling** eller **inaktiverad** för .NET Core 2.0, 2.1, 2.2 och 3.0.

    ![Välj alternativ per plattform](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

I webbappappen i apptjänsten under **Inställningar** > **väljer du Aktivera Programinsikter** > **Enable**. Node.js-agentbaserad övervakning är för närvarande i förhandsversion.

# <a name="java"></a>[Java](#tab/java)

Java App Service-baserade webbprogram stöder för närvarande inte automatisk agent/tilläggsbaserad övervakning. Om du vill aktivera övervakning för ditt Java-program måste du [instrumentera programmet manuellt](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Python App Service-baserade webbprogram stöder för närvarande inte automatisk agent/tilläggsbaserad övervakning. Om du vill aktivera övervakning för ditt Python-program måste du [instrumentera programmet manuellt](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Aktivera övervakning på klientsidan

# <a name="net"></a>[.NET](#tab/net)

Övervakning på klientsidan är opt-in för ASP.NET. Så här aktiverar du övervakning på klientsidan:

* Välj **Inställningar** >** **Programinställningar****
   * Lägg till ett nytt namn och **värde** **för appinställning** under Programinställningar:

     Namn:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`true`

   * **Spara** inställningarna och **starta om** din app.

![Skärmbild av användargränssnittet för programinställningar](./media/azure-web-apps/appinsights-javascript-enabled.png)

Om du vill inaktivera övervakning på klientsidan tar du antingen bort det associerade nyckelvärdesparet från programinställningarna eller anger värdet till false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Övervakning på klientsidan är **aktiverat som standard** för .NET Core-appar med **rekommenderad samling,** oavsett om appinställningen "APPINSIGHTS_JAVASCRIPT_ENABLED" finns.

Om du av någon anledning vill inaktivera övervakning på klientsidan:

* Välj **Inställningar** > **Programinställningar**
   * Lägg till ett nytt namn och **värde** **för appinställning** under Programinställningar:

     Namn:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Värde:`false`

   * **Spara** inställningarna och **starta om** din app.

![Skärmbild av användargränssnittet för programinställningar](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Om du vill aktivera övervakning på klientsidan för node.js-programmet måste du [manuellt lägga till JavaScript SDK på klientsidan i ditt program](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Om du vill aktivera övervakning på klientsidan för ditt Java-program måste du [manuellt lägga till JavaScript SDK på klientsidan i ditt program](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Om du vill aktivera övervakning på klientsidan för ditt Python-program måste du [manuellt lägga till JavaScript SDK på klientsidan i ditt program](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Automatisera övervakning

För att möjliggöra telemetrisamling med Application Insights behöver endast programinställningarna ställas in:

   ![Inställningar för programinställningar för apptjänst med tillgängliga inställningar för application insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definitioner för programinställningar

|Namn på appinställning |  Definition | Värde |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Huvudtillägg, som styr körningsövervakning. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Endast i standardläge aktiveras viktiga funktioner för att säkerställa optimal prestanda. | `default` eller `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Styr om den binära `InstrumentationEngine` omskrivningsmotorn ska slås på. Den här inställningen har prestandakonsekvenser och påverkar kallstart/starttid. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Kontroller om SQL-& Azure-tabelltext kommer att fångas in tillsammans med beroendeanropen. Prestandavarning: programmets kalla starttid påverkas. Den här `InstrumentationEngine`inställningen kräver . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Inställningar för Programinställningar för AppTjänst med Azure Resource Manager

Programinställningar för App Services kan hanteras och konfigureras med [Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Den här metoden kan användas vid distribution av nya App Service-resurser med Azure Resource Manager-automatisering eller för att ändra inställningarna för befintliga resurser.

Den grundläggande strukturen för programinställningarna JSON för en apptjänst finns nedan:

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

Ett exempel på en Azure Resource Manager-mall med programinställningar konfigurerade för Application Insights kan den här [mallen](https://github.com/Andrew-MSFT/BasicImageGallery) vara användbar, särskilt avsnittet som börjar på [rad 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisera skapandet av en Application Insights-resurs och länka till din nyskapade App Service.

Om du vill skapa en Azure Resource Manager-mall med alla standardinställningar för Application Insights konfigurerade börjar du processen som om du skulle skapa en ny webbapp med Application Insights aktiverat.

Välj **alternativ för automatisering**

   ![Skapa meny för apptjänstwebbapp](./media/azure-web-apps/create-web-app.png)

Det här alternativet genererar den senaste Azure Resource Manager-mallen med alla nödvändiga inställningar konfigurerade.

  ![Webbappmall för App Service](./media/azure-web-apps/arm-template.png)

Nedan finns ett exempel, ersätta `AppMonitoredSite` alla instanser av med ditt webbplatsnamn:

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
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
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

### <a name="enabling-through-powershell"></a>Aktivera via PowerShell

För att aktivera programövervakning via PowerShell behöver endast de underliggande programinställningarna ändras. Nedan följer ett exempel, som gör det möjligt att övervaka program för en webbplats som heter "AppMonitoredSite" i resursgruppen "AppMonitoredRG", och konfigurerar data som ska skickas till instrumenteringsnyckeln "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Förlängning/agent för uppgraderingsövervakning

### <a name="upgrading-from-versions-289-and-up"></a>Uppgradera från version 2.8.9 och uppåt

Uppgradering från version 2.8.9 sker automatiskt, utan några ytterligare åtgärder. De nya övervakningsbitarna levereras i bakgrunden till målapptjänsten och vid omstart av programmet kommer de att plockas upp.

Så här kontrollerar du vilken version av tillägget du kör på`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Skärmbild av url-sökväghttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Uppgradera från versionerna 1.0.0 - 2.6.5

Från och med version 2.8.9 används det förinstallerade platstillägget. Om du är en tidigare version kan du uppdatera via ett av två sätt:

* [Uppgradera genom att aktivera via portalen](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Även om du har tillägget Application Insights för Azure App Service installerat visar användargränssnittet bara **knappen Aktivera.** Bakom kulisserna kommer det gamla privata webbplatstillägget att tas bort.)

* [Uppgradera via PowerShell:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Ställ in programinställningarna för att aktivera det förinstallerade platstillägget ApplicationInsightsAgent. Se [Aktivera via powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ta manuellt bort det privata webbplatstillägget Application Insights för Azure App Service.

Om uppgraderingen görs från en version före 2.5.1 kontrollerar du att ApplicationInsigths-dll-filer tas bort från programlagerplatsens mapp [och ser felsökningssteg](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Felsökning

Nedan visas vår steg-för-steg-felsökningsguide för tilläggs-/agentbaserad övervakning för .NET- och .NET Core-baserade program som körs på Azure App Services.

> [!NOTE]
> Java-program stöds endast på Azure App Services via manuell SDK-baserad instrumentering och därför gäller inte stegen nedan för dessa scenarier.

1. Kontrollera att programmet övervakas `ApplicationInsightsAgent`via .
    * Kontrollera `ApplicationInsightsAgent_EXTENSION_VERSION` att appinställningen är inställd på värdet "~2".
2. Se till att programmet uppfyller kraven som ska övervakas.
    * Bläddra till `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Skärmbild https://yoursitename.scm.azurewebsites/applicationinsights av resultatsida](./media/azure-web-apps/app-insights-sdk-status.png)

    * Bekräfta att `Application Insights Extension Status` är`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Om den inte körs följer du [instruktionerna för övervakning av Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Bekräfta att statuskällan finns och ser ut så här:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Om ett liknande värde inte finns betyder det att programmet inte körs eller inte stöds. Om du vill vara säkra på att programmet körs kan du prova att manuellt besöka program-url/application-slutpunkterna, vilket gör att körningsinformationen blir tillgänglig.

    * Bekräfta `IKeyExists` att det är`true`
        * Om det `false`är `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` , lägga till och med din ikey guid till dina programinställningar.

    * Bekräfta att det inte `AppAlreadyInstrumented`finns `AppContainsDiagnosticSourceAssembly`några `AppContainsAspNetTelemetryCorrelationAssembly`poster för , och .
        * Om det finns någon av dessa poster tar du `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`bort `Microsoft.AspNet.TelemetryCorrelation`följande paket från programmet: , och .

Tabellen nedan ger en mer detaljerad förklaring av vad dessa värden betyder, deras underliggande orsaker och rekommenderade korrigeringar:

|Problemvärde|Förklaring|Snabbkorrigering
|---- |----|---|
| `AppAlreadyInstrumented:true` | Det här värdet anger att tillägget har upptäckts att någon aspekt av SDK redan finns i programmet och kommer att backa. Det kan bero på `System.Diagnostics.DiagnosticSource`en `Microsoft.AspNet.TelemetryCorrelation`hänvisning till , eller`Microsoft.ApplicationInsights`  | Ta bort referenserna. Vissa av dessa referenser läggs till som standard från vissa Visual Studio-mallar, `Microsoft.ApplicationInsights`och äldre versioner av Visual Studio kan lägga till referenser till .
|`AppAlreadyInstrumented:true` | Om programmet är inriktat på .NET Core 2.1 eller 2.2 och refererar till [Microsoft.AspNetCore.All-metapaket,](https://www.nuget.org/packages/Microsoft.AspNetCore.All) kommer det att finnas i Application Insights och tillägget kommer att backa. | Kunder på .NET Core 2.1,2.2 [rekommenderas](https://github.com/aspnet/Announcements/issues/287) att använda Microsoft.AspNetCore.App-metapaketet i stället.|
|`AppAlreadyInstrumented:true` | Det här värdet kan också orsakas av närvaron av ovanstående dlls i appmappen från en tidigare distribution. | Rengör appmappen för att säkerställa att dessa dll-filer tas bort. Kontrollera både den lokala appens bin-katalog och wwwroot-katalogen på Apptjänsten. (Så här kontrollerar du wwwroot-katalogen i apptjänstens webbapp: Avancerade verktyg (Kudu) > Debug-konsolen > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Det här värdet anger att `Microsoft.AspNet.TelemetryCorrelation` tillägget har upptäckts refererar till i programmet och kommer att backa. | Ta bort referensen.
|`AppContainsDiagnosticSourceAssembly**:true`|Det här värdet anger att `System.Diagnostics.DiagnosticSource` tillägget har upptäckts refererar till i programmet och kommer att backa.| Ta bort referensen.
|`IKeyExists:false`|Det här värdet anger att instrumenteringsnyckeln inte `APPINSIGHTS_INSTRUMENTATIONKEY`finns i AppSetting. Möjliga orsaker: Värdena kan ha tagits bort av misstag, glömt att ange värden i automationsskript, etc. | Kontrollera att inställningen finns i programinställningarna för App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED och urlKomprimering stöds inte

Om du använder APPINSIGHTS_JAVASCRIPT_ENABLED=true i fall där innehåll kodas kan du få fel som: 

- 500 URL skriva fel
- 500.53 URL skriva om modulfel med meddelande Utgående skrivregler kan inte tillämpas när innehållet i HTTP-svaret kodas ("gzip"). 

Detta beror på att APPINSIGHTS_JAVASCRIPT_ENABLED programinställningen är inställd på true och innehållskodning samtidigt. Det här scenariot stöds inte ännu. Lösningen är att ta bort APPINSIGHTS_JAVASCRIPT_ENABLED från dina programinställningar. Tyvärr innebär detta att om javascript-instrumentering på klient/webbläsare fortfarande krävs behövs manuella SDK-referenser för dina webbsidor. Följ [instruktionerna](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) för manuell instrumentering med JavaScript SDK.

Den senaste informationen om agenten/tillägget Application Insights finns i [viktig information](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP och WordPress stöds inte

PHP och WordPress webbplatser stöds inte. Det finns för närvarande ingen officiellt stödd SDK/agent för övervakning på serversidan av dessa arbetsbelastningar. Men manuellt instrumentering klientsidan transaktioner på en PHP eller WordPress webbplats genom att lägga till klientsidan javascript till dina webbsidor kan åstadkommas med hjälp av [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Nästa steg
* [Kör profileraren för din live-app](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – övervaka Azure Functions med Application Insights
* [Aktivera Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) så att den skickas till Application Insights.
* [Övervaka mätvärden för tjänstens hälsotillstånd](../platform/data-platform.md) för att se till att tjänsten är tillgänglig och svarar.
* [Få aviseringar](../platform/alerts-overview.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* Använd [Application Insights för JavaScript-appar och webbsidor](javascript.md) för att hämta klienttelemetri från webbläsare som besöker en webbsida.
* [Konfigurera tillgänglighetswebbtester](monitor-web-app-availability.md) så att du aviseras om webbplatsen inte fungerar.
