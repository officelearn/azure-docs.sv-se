<properties
    pageTitle="Diagnostisera prestandaproblem på en webbplats som kör IIS | Microsoft Azure"
    description="Övervaka prestanda för en webbplats utan att distribuera den igen. Använd fristående eller med Application Insights SDK för att hämta telemetri om beroenden."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/09/2016"
    ms.author="awills"/>


# Installera Application Insights Status Monitor och övervaka prestanda på webbplatser

*Application Insights finns endast som förhandsversion.*

Med Status Monitor i Visual Studio Application Insights kan du diagnostisera undantag och prestandaproblem i ASP.NET-program. 

![exempeldiagram](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

> [AZURE.TIP] Det finns olika artiklar om hur du instrumenterar [J2EE-livewebbappar](app-insights-java-live.md) och [Azure Cloud Services](app-insights-cloudservices.md).


Du kan tillämpa Application Insights på IIS-webbprogram på tre sätt:

* **Under utvecklingen:** [Lägg till Application Insights SDK][greenbrown] i webbappens kod. Detta ger tillgång till:
 * En uppsättning diagnostik- och användningstelemetri.
 * Med [Application Insights API][api] kan du skriva egen telemetri för att spåra detaljerade användnings- och diagnostikproblem.
* **Under körningen:** Använd Status Monitor för att instrumentera webbappen på servern.
 * Övervaka webbprogram som redan körs: du behöver inte återskapa eller publicera dem igen.
 * En uppsättning diagnostik- och användningstelemetri.
 * Beroendediagnostik – identifiera fel eller låga prestanda där din app använder andra komponenter, t.ex databaser, REST-API:er eller andra tjänster.
 * Felsök eventuella problem med telemetri.
* **Under utvecklingen och körningen:** Kompilera SDK i webbappens kod och kör Status Monitor på webbservern.  Det bästa av två världar:
 * Enkel diagnostik- och användningstelemetri.
 * Beroendediagnostik.
 * Med API:et kan du skriva anpassad telemetri.
 * Felsök eventuella problem med SDK och telemetri.


## Installera Application Insights Status Monitor

Du behöver en [Microsoft Azure](http://azure.com)-prenumeration.

### Om appen körs på din IIS-server

1. Logga in med administratörsbehörighet på IIS-webbservern.
2. Ladda ned och kör [installationsprogrammet för Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
4. I installationsguiden loggar du in i Microsoft Azure.

    ![Logga in i Azure med autentiseringsuppgifterna för ditt Microsoft-konto.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Har du problem med att ansluta? Mer information finns i [Felsökning](#troubleshooting).*

5. Välj den installerade webbappen eller webbplatsen som du vill övervaka och konfigurera sedan den resurs där du vill visa resultatet på Application Insights-portalen.

    ![Välj en app och en resurs.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalt kan du välja att konfigurera en ny resurs och [resursgrupp][roles].

    Annars kan du använda en befintlig resurs om du redan har konfigurerat [webbtester][availability] för webbplatsen eller [webbklientövervakning][client].

6. Starta om IIS.

    ![Välj Starta om överst i dialogrutan.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Webbtjänsten avbryts en liten stund.

6. Observera att ApplicationInsights.config har lagts till för de webbappar som du vill övervaka.

    ![Leta upp webbappens .config-fil och kodfiler.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Det finns även vissa ändringar i web.config.

#### Vill du ändra konfigurationen senare?

När du har slutfört guiden kan du konfigurera agenten igen när du vill. Du kan också använda det här alternativet om du installerade agenten och det uppstod problem med den ursprungliga installationen.

![Klicka på ikonen för Application Insights i Aktivitetsfältet](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)


### Om din app körs som en Azure-webbapp

Lägg till Application Insights-tillägget på kontrollpanelen för din Azure-webbapp.

![I webbappen går du till Inställningar, Tillägg, Lägg till, Application Insights](./media/app-insights-monitor-performance-live-website-now/05-extend.png)


### Om det är ett Azure-molntjänstprojekt

[Lägg till skript till webb- och arbetarroller](app-insights-cloudservices.md).


## Visa telemetri om prestanda

Logga in på [Azure-portalen](https://portal.azure.com), bläddra till Application Insights och öppna den resurs som du skapade.

![Välj Bläddra, Application Insights och välj din app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Öppna bladet Prestanda om du vill visa information om förfrågningar, svarstider, beroenden och andra data.

![Prestanda](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Klicka om du vill justera detaljerna för det som visas, eller om du vill lägga till ett nytt diagram.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

## Beroenden

Diagrammet Beroendevaraktighet visar hur lång tid anropen tar från din app till externa komponenter, t.ex databaser, REST-API: er eller Azure Blob Storage.

Om du vill dela in diagrammet efter anrop till olika beroenden markerar du diagrammet, aktiverar Gruppering och väljer sedan Beroende, Beroendetyp eller Beroendeprestanda.

Du kan också filtrera diagrammet om du vill titta på en specifik beroende-, typ- eller prestandaenhet. Klicka på Filter.

## Prestandaräknare

(Inte för Azure-webbappar.) Klicka på Servrar på översiktsbladet om du vill visa diagram över serverprestandaräknare, till exempel processor- och minnesanvändning.

Lägg till ett nytt schema eller klicka på ett diagram om du vill ändra vad som visas. 

Du kan också [ändra uppsättningen prestandaräknare som rapporteras av SDK](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3). 

## Undantag

![Klicka dig igenom diagrammet med serverundantag](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Du kan undersöka specifika undantag (från de senaste sju dagarna) och visa stackspårning och kontextdata.

## Samling

Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)


## Felsökning

### Anslutningsfel

Du måste öppna vissa utgående portar i serverns brandvägg för att Status Monitor ska fungera:

+ Telemetri – behövs hela tiden:
 +  `dc.services.visualstudio.com:80`
 +  `dc.services.visualstudio.com:443`
 +  `dc.applicationinsights.microsoft.com`
+ Konfiguration – behövs bara när du gör ändringar:
 -  `management.core.windows.net:443`
 -  `management.azure.com:443`
 -  `login.windows.net:443`
 -  `login.microsoftonline.com:443`
 -  `secure.aadcdn.microsoftonline-p.com:443`
 -  `auth.gfx.ms:443`
 -  `login.live.com:443`
+ Installation:
 +  `packages.nuget.org:443`

Den här listan kan ändras då och då.

### Ser du ingen telemetri?

  * Generera lite data genom att använda din webbplats.
  * Vänta några minuter så att data hinner tas emot och klicka sedan på **Uppdatera**.
  * Öppna Diagnostiksökning (panelen Sök) om du vill visa enskilda händelser. Händelser visas ofta i Diagnostiksökning innan sammanställda data visas i diagrammen.
  * Öppna Status Monitor och välj ditt program i den vänstra rutan. Kontrollera om det finns några diagnostikmeddelanden för det här programmet i avsnittet ”Konfigurationsmeddelanden”:

  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Kontrollera att serverbrandväggen tillåter utgående trafik på de portar som anges ovan.
  * Om du ser ett meddelande om ”otillräcklig behörighet” på servern provar du följande:
    * I IIS-hanteraren väljer du programpoolen, öppnar **Avancerade inställningar** och noterar identiteten under **Processmodell**.
    * På kontrollpanelen för Datorhantering lägger du till den här identiteten i gruppen Användare av prestandaövervakning.
  * Om du MMA/SCOM är installerat på servern kan vissa versioner vara i konflikt med varandra. Avinstallera både SCOM och Status Monitor och installera om de senaste versionerna.
  * Mer information finns i [Felsökning][qna].

## Systemkrav

Operativsystemstöd för Application Insights Status Monitor på servern:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows server 2012 R2

med senaste SP och .NET Framework 4.0 eller 4.5

Windows 7, 8 och 8.1 med .NET Framework 4.0 eller 4.5 på klientsidan

IIS-stöd: IIS 7, 7.5, 8, 8.5 (IIS krävs)

## Automatisering med PowerShell

Du kan starta och stoppa övervakningen med hjälp av PowerShell.

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Valfritt) Namnet på en webbapp.
* Visar Application Insights-övervakningsstatusen för varje webbapp (eller den namngivna appen) på den här IIS-servern.

* Returnerar `ApplicationInsightsApplication` för varje app:
 * `SdkState==EnabledAfterDeployment`: Appen övervakas och instrumenterades vid körningstillfället, antingen av verktyget Status Monitor eller med `Start-ApplicationInsightsMonitoring`.
 * `SdkState==Disabled`: Appen har inte instrumenterats för Application Insights. Antingen instrumenterades den aldrig eller så inaktiverades övervakning under körning av verktyget Status Monitor eller med `Stop-ApplicationInsightsMonitoring`.
 * `SdkState==EnabledByCodeInstrumentation`: Appen instrumenterades genom att SDK lades till i källkoden. Appens SDK kan inte uppdateras eller stoppas.
 * `SdkVersion` visar versionen som används för att övervaka den här appen.
 * `LatestAvailableSdkVersion`visar versionen som för närvarande är tillgänglig i NuGet-galleriet. Om du vill uppgradera appen till den här versionen använder du `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Namnet på appen i IIS
* `-InstrumentationKey` Application Insights-resursens ikey (instrumenteringsnyckel) där du vill att resultatet ska visas.

* Den här cmdleten påverkar endast appar som inte redan har instrumenterats, dvs. SdkState==NotInstrumented.

    Cmdleten påverkar inte en app som redan instrumenterats, antingen under utvecklingen genom att SDK lades till i koden, eller under körningen av en tidigare användning av den här cmdleten.

    Den SDK-version som används för att instrumentera appen är den version som senast laddades ned till servern.

    Använd Update-ApplicationInsightsVersion för att hämta den senaste versionen.

* Returnerar `ApplicationInsightsApplication` om åtgärden lyckades. Om åtgärden misslyckas loggas en spårning till stderr.

    
          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Namnet på en app i IIS
* `-All` Stoppar övervakningen av alla appar på IIS-servern där `SdkState==EnabledAfterDeployment`

* Stoppar övervakningen av de angivna apparna och tar bort instrumentering. Det fungerar bara för appar som har instrumenterats vid körning med statusövervakningsverktyget eller Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)

* Returnerar ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: Namnet på en webbapp i IIS.
* `-InstrumentationKey` (Valfritt.) Använd det här om du vill ändra resursen som appens telemetri skickas till.
* Den här cmdleten:
 * Uppgraderar den namngivna appen till den version av SDK som senast laddades ned till datorn. (Fungerar bara om `SdkState==EnabledAfterDeployment`)
 * Om du anger en instrumenteringsnyckel konfigureras den namngivna appen så att den skickar telemetri till resursen med den nyckeln. (Fungerar om `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Laddar ned senaste Application Insights SDK till servern.

## Azure-mall

Om webbappen finns i Azure och du skapar dina resurser med hjälp av en Azure Resource Manager-mall kan du konfigurera Application Insights genom att lägga till följande till resursnoden:

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - ett namn för Application Insights-resursen
* `myWebAppName` - webbappens ID

## <a name="next"></a>Nästa steg

* [Skapa webbtester][availability] så att du är säker på att webbplatsen är aktiv.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.
* [Lägg till telemetri för webklienten][usage] om du vill visa undantag från webbsidans kod och lägga till spårningsanrop.
* [Lägg till Application Insights SDK i koden för din webbtjänst][greenbrown] om du vill lägga till spårnings- och logganrop i serverkoden.

## Video

#### Prestandaövervakning

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=jun16_HO2-->


