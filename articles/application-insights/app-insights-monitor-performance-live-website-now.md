---
title: "Diagnostisera prestandaproblem på en webbplats som kör IIS | Microsoft Docs"
description: "Övervaka prestanda för en webbplats utan att distribuera den igen. Använd fristående eller med Application Insights SDK för att hämta telemetri om beroenden."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: b70c8baab03703bc00b75c2c611f69e3b71d6cd7
ms.openlocfilehash: 5159e7fc47d320d52eb7b94b5775158a3f09c769


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentera webbappar vid körning med Application Insights
*Application Insights finns endast som förhandsversion.*

Du kan instrumentera en live-webbapp med Visual Studio Application Insights utan att behöva ändra eller omdistribuera din kod. Om dina appar finns på en lokal IIS-server installerar du Statusövervakare. Om de är Azure-webbappar eller körs i en virtuell Azure-dator kan du installera Application Insights-tillägget. (Det finns även olika artiklar om hur du instrumenterar [J2EE-livewebbappar](app-insights-java-live.md) och [Azure Cloud Services](app-insights-cloudservices.md).)

![exempeldiagram](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Du kan tillämpa Application Insights på dina .NET-webbprogram via tre vägar:

* **Under utvecklingen:** [Lägg till Application Insights SDK][greenbrown] i webbappens kod.
* **Körtid:** Instrumentera din webbapp på servern, så som beskrivs nedan, utan att behöva bygga om och omdistribuera koden.
* **Båda:** Skapa SDK:n i din webbappskod, och tillämpa även körningstilläggen. Få ut det bästa av två världar.

Här är en sammanfattning av vad du får med respektive väg:

|  | Byggtid | Körtid |
| --- | --- | --- |
| Förfrågningar och undantag |Ja |Ja |
| [Mer detaljerade undantag](app-insights-asp-net-exceptions.md) | |Ja |
| [Beroendediagnostik](app-insights-asp-net-dependencies.md) |Om .NET 4.6 + |Ja |
| [Systemprestandaräknare](app-insights-performance-counters.md) | |IIS- eller Azure-molntjänster, inte Azure webbapp |
| [API för anpassad telemetri-][api] |Ja | |
| [Spårningsloggsintegrering](app-insights-asp-net-trace-logs.md) |Ja | |
| [Sidvy och användardata](app-insights-javascript.md) |Ja | |
| Du behöver inte återskapa kod |Nej | |

## <a name="instrument-your-web-app-at-run-time"></a>Instrumentera din webbapp under körning
Du behöver en [Microsoft Azure](http://azure.com)-prenumeration.

### <a name="if-your-app-is-an-azure-web-app-or-cloud-service"></a>Om din app är en Azure-webbapp eller Cloud Service-app
* Välj Application Insights på appens kontrollpanel i Azure.

    [Läs mer](app-insights-azure.md).

### <a name="if-your-app-is-hosted-on-your-iis-server"></a>Om appen körs på din IIS-server
1. Logga in med administratörsbehörighet på IIS-webbservern.
2. Ladda ned och kör [installationsprogrammet för Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
3. I installationsguiden loggar du in i Microsoft Azure.

    ![Logga in i Azure med autentiseringsuppgifterna för ditt Microsoft-konto.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Anslutningsfel? Mer information finns i [Felsökning](#troubleshooting).*
4. Välj den installerade webbappen eller webbplatsen som du vill övervaka och konfigurera sedan den resurs där du vill visa resultatet på Application Insights-portalen.

    ![Välj en app och en resurs.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalt kan du välja att konfigurera en ny resurs och [resursgrupp][roles].

    Annars kan du använda en befintlig resurs om du redan har konfigurerat [webbtester][availability] för webbplatsen eller [webbklientövervakning][client].
5. Starta om IIS.

    ![Välj Starta om överst i dialogrutan.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Webbtjänsten avbryts en liten stund.
6. Observera att ApplicationInsights.config har lagts till för de webbappar som du vill övervaka.

    ![Leta upp webbappens .config-fil och kodfiler.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Det finns även vissa ändringar i web.config.

#### <a name="want-to-reconfigure-later"></a>Vill du ändra konfigurationen senare?
När du har slutfört guiden kan du konfigurera agenten igen när du vill. Du kan också använda det här alternativet om du installerade agenten och det uppstod problem med den ursprungliga installationen.

![Klicka på ikonen för Application Insights i Aktivitetsfältet](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## <a name="view-performance-telemetry"></a>Visa telemetri om prestanda
Logga in på [Azure-portalen](https://portal.azure.com), bläddra till Application Insights och öppna den resurs som du skapade.

![Välj Bläddra, Application Insights och välj din app](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Öppna bladet Prestanda om du vill visa information om förfrågningar, svarstider, beroenden och andra data.

![Prestanda](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Klicka på ett valfritt diagram om du vill öppna en mer detaljerad vy.

Du kan [redigera, ändra, spara](app-insights-metrics-explorer.md) och fästa diagram eller hela bladet på en [instrumentpanel](app-insights-dashboards.md).

## <a name="dependencies"></a>Beroenden
Diagrammet Beroendevaraktighet visar hur lång tid anropen tar från din app till externa komponenter, t.ex databaser, REST-API: er eller Azure Blob Storage.

Så här delar du in diagrammet efter anrop till olika beroenden: Redigera diagrammet, aktivera Gruppering och gruppera sedan efter beroende, beroendetyp eller beroendeprestanda.

![Beroende](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## <a name="performance-counters"></a>Prestandaräknare
(Inte för Azure-webbappar.) Klicka på Servrar på översiktsbladet om du vill visa diagram över serverprestandaräknare, till exempel processor- och minnesanvändning.

Om du har flera serverinstanser vill du kanske redigera de diagram som ska grupperas efter rollinstans.

![Servrar](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

Du kan också ändra uppsättningen prestandaräknare som rapporteras av SDK. 

## <a name="exceptions"></a>Undantag
![Klicka dig igenom diagrammet med serverundantag](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Du kan undersöka specifika undantag (från de senaste sju dagarna) och visa stackspårning och kontextdata.

## <a name="sampling"></a>Samling
Om programmet skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare kan den anpassade samplingsfunktionen skicka bara en del av din telemetri. [Läs mer om sampling.](app-insights-sampling.md)

## <a name="troubleshooting"></a>Felsökning
### <a name="connection-errors"></a>Anslutningsfel
Du måste öppna [vissa utgående portar](app-insights-ip-addresses.md#outgoing-ports) i serverns brandvägg för att Statusövervakare ska fungera.

### <a name="no-telemetry"></a>Ser du ingen telemetri?
* Generera lite data genom att använda din webbplats.
* Vänta några minuter så att data hinner tas emot och klicka sedan på **Uppdatera**.
* Öppna Diagnostiksökning (panelen Sök) om du vill visa enskilda händelser. Händelser visas ofta i Diagnostiksökning innan sammanställda data visas i diagrammen.
* Öppna Status Monitor och välj ditt program i den vänstra rutan. Kontrollera om det finns några diagnostikmeddelanden för det här programmet i avsnittet ”Konfigurationsmeddelanden”:

  ![Öppna bladet Prestanda om du vill visa information om förfrågningar, svarstider, beroenden och andra data](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Kontrollera att serverbrandväggen tillåter utgående trafik på de portar som anges ovan.
* Om du ser ett meddelande om ”otillräcklig behörighet” på servern provar du följande:
  * I IIS-hanteraren väljer du programpoolen, öppnar **Avancerade inställningar** och noterar identiteten under **Processmodell**.
  * På kontrollpanelen för Datorhantering lägger du till den här identiteten i gruppen Användare av prestandaövervakning.
* Om du MMA/SCOM är installerat på servern kan vissa versioner vara i konflikt med varandra. Avinstallera både SCOM och Status Monitor och installera om de senaste versionerna.
* Mer information finns i [Felsökning][qna].

## <a name="system-requirements"></a>Systemkrav
Operativsystemstöd för Application Insights Status Monitor på servern:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2

med senaste SP och .NET Framework 4.0 eller 4.5

Windows 7, 8 och 8.1 med .NET Framework 4.0 eller 4.5 på klientsidan

IIS-stöd: IIS 7, 7.5, 8, 8.5 (IIS krävs)

## <a name="automation-with-powershell"></a>Automatisering med PowerShell
Du kan starta och stoppa övervakningen med hjälp av PowerShell i din IIS-server.

Importera först Application Insights-modulen:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Ta reda på vilka appar som övervakas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Valfritt) Namnet på en webbapp.
* Visar Application Insights-övervakningsstatusen för varje webbapp (eller den namngivna appen) på den här IIS-servern.
* Returnerar `ApplicationInsightsApplication` för varje app:

  * `SdkState==EnabledAfterDeployment`: Appen övervakas, och instrumenterades vid körningstillfället, antingen av verktyget Status Monitor eller med `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: Appen har inte instrumenterats för Application Insights. Antingen instrumenterades den aldrig eller så inaktiverades övervakning under körning av verktyget Status Monitor eller med `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: Appen instrumenterades genom att SDK lades till i källkoden. Appens SDK kan inte uppdateras eller stoppas.
  * `SdkVersion` visar versionen som används för att övervaka den här appen.
  * `LatestAvailableSdkVersion` visar versionen som för närvarande är tillgänglig i NuGet-galleriet. Om du vill uppgradera appen till den här versionen använder du `Update-ApplicationInsightsMonitoring`.

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

## <a name="a-namenextanext-steps"></a><a name="next"></a>Nästa steg
* [Skapa webbtester][availability] så att du är säker på att webbplatsen är aktiv.
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem.
* [Lägg till telemetri för webklienten][användning] om du vill visa undantag från webbsidans kod och lägga till spårningsanrop.
* [Lägg till Application Insights SDK i koden för din webbtjänst][greenbrown] om du vill lägga till spårnings- och logganrop i serverkoden.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[användning]: app-insights-web-track-usage.md



<!--HONumber=Nov16_HO2-->


