---
title: "Övervaka en ASP.NET-livewebbapp med Azure Application Insights | Microsoft Docs"
description: "Övervaka prestanda för en webbplats utan att distribuera den igen. Fungerar med ASP.NET-webbappar som finns lokalt, i virtuella datorer eller på Azure."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: a0340359dff470551a08a8213f3a704f15f78794
ms.lasthandoff: 03/16/2017


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentera webbappar vid körning med Application Insights


Du kan instrumentera en live-webbapp med Azure Application Insights utan att behöva ändra eller omdistribuera din kod. Om dina appar hanteras av en lokal IIS-server installerar du	Statusövervakare. Om de är Azure-webbappar eller körs i en virtuell Azure-dator kan du aktivera Application Insights-övervakning från Azures kontrollpanel. (Det finns även olika artiklar om hur du instrumenterar [J2EE-livewebbappar](app-insights-java-live.md) och [Azure Cloud Services](app-insights-cloudservices.md).) Du behöver en [Microsoft Azure](http://azure.com)-prenumeration.

![exempeldiagram](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Du kan tillämpa Application Insights på dina .NET-webbprogram via tre vägar:

* **Byggtid:** [Lägg till Application Insights SDK][greenbrown] i webbappens kod.
* **Körtid:** Instrumentera din webbapp på servern, så som beskrivs nedan, utan att behöva bygga om och omdistribuera koden.
* **Båda:** Skapa SDK:n i din webbappskod, och tillämpa även körningstilläggen. Få ut det bästa av två världar.

Här är en sammanfattning av vad du får med respektive väg:

|  | Byggtid | Körtid |
| --- | --- | --- |
| Förfrågningar och undantag |Ja |Ja |
| [Mer detaljerade undantag](app-insights-asp-net-exceptions.md) | |Ja |
| [Beroendediagnostik](app-insights-asp-net-dependencies.md) |I .NET 4.6+, men färre detaljer |Ja, fullständiga detaljer: resultatkoder, SQL-kommandotext, HTTP verb|
| [Systemprestandaräknare](app-insights-performance-counters.md) |Ja |Ja |
| [API för anpassad telemetri][api] |Ja | |
| [Spårningsloggsintegrering](app-insights-asp-net-trace-logs.md) |Ja | |
| [Sidvy och användardata](app-insights-javascript.md) |Ja | |
| Du behöver inte återskapa kod |Nej | |


## <a name="monitor-a-live-azure-web-app"></a>Övervaka en Azure-livewebbapp

Om programmet körs som en Azure-webbtjänst kan du aktivera övervakning på följande sätt:

* Välj Application Insights på appens kontrollpanel i Azure.

    ![Konfigurera Application Insights för en Azure-webbapp](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Klicka på länken längst ned för att öppna den fullständiga Applications Insights-resursen när sammanfattningssidan öppnas.

    ![Klicka här så kommer du till Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Övervaka appar för moln och virtuella datorer](app-insights-azure.md).

## <a name="monitor-a-live-iis-web-app"></a>Övervaka en IIS-livewebbapp

Om din app finns på en IIS-server aktiverar du Application Insights med hjälp av Statusövervakaren.

1. Logga in med administratörsbehörighet på IIS-webbservern.
2. Om Application Insights Status Monitor inte redan är installerat laddar du ned och kör [installationsprogrammet för Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648) (eller kör [installationsprogrammet för webbplattform](https://www.microsoft.com/web/downloads/platform.aspx) och söker efter det i for Application Insights Status Monitor).
3. I statusövervakaren väljer du den installerade webbappen eller en webbplats som du vill övervaka. Logga in med dina Azure autentiseringsuppgifter.

    Konfigurera den resurs där du vill visa resultatet i Application Insights-portalen. (Normalt är det bäst att skapa en ny resurs. Välj en befintlig resurs om du redan har [webbtester][availability] eller [klientövervakning][client] för den här appen.) 

    ![Välj en app och en resurs.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Starta om IIS.

    ![Välj Starta om överst i dialogrutan.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Webbtjänsten avbryts en liten stund.

## <a name="customize-monitoring-options"></a>Anpassa övervakningsalternativ

När Application Insights aktiveras läggs DLL-filer och ApplicationInsights.config till i webbappen. Du kan [redigera .config-filen](app-insights-configuration-with-applicationinsights-config.md) och ändra en del av alternativen.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>När du publicerar appen igen ska du återaktivera Application Insights

Innan du publicerar din app igen bör du överväga att [lägga till Applications Insights i koden i Visual Studio][greenbrown]. Du får mer detaljerad telemetri och möjligheten att skriva anpassad telemetri.

Om du vill publicera på nytt utan att lägga till Application Insights i koden, tänk på att distributionsprocessen kan ta bort DLL-filer och ApplicationInsights.config från den publicerade webbplatsen. Därför:

1. Om du har redigerat ApplicationInsights.config tar du en kopia av den innan du publicerar appen igen.
2. Publicera om appen.
3. Återaktivera övervakning med Application Insights. (Använd lämplig metod: antingen Azure-kontrollpanelen för webbappar eller statusövervakaren på en IIS-värd.)
4. Återställa redigeringar som har utförts på .config-filen.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Felsöka körningskonfigurationen av Application Insights

### <a name="cant-connect-no-telemetry"></a>Går det inte att ansluta? Ser du ingen telemetri?

* Öppna [de nödvändiga utgående portarna](app-insights-ip-addresses.md#outgoing-ports) i serverns brandvägg för att Statusövervakare ska fungera.

* Öppna Status Monitor och välj ditt program i den vänstra rutan. Kontrollera om det finns några diagnostikmeddelanden för det här programmet i avsnittet ”Konfigurationsmeddelanden”:

  ![Öppna bladet Prestanda om du vill visa information om förfrågningar, svarstider, beroenden och andra data](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Om du ser ett meddelande om ”otillräcklig behörighet” på servern provar du följande:
  * I IIS-hanteraren väljer du programpoolen, öppnar **Avancerade inställningar** och noterar identiteten under **Processmodell**.
  * På kontrollpanelen för Datorhantering lägger du till den här identiteten i gruppen Användare av prestandaövervakning.
* Om du har MMA/SCOM (Systems Center Operations Manager) installerat på servern kan vissa versioner vara i konflikt med varandra. Avinstallera både SCOM och Status Monitor och installera om de senaste versionerna.
* Mer information finns i [Felsökning][qna].

## <a name="system-requirements"></a>Systemkrav
Operativsystemstöd för Application Insights Status Monitor på servern:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

med senaste SP och .NET Framework 4.5

Windows 7, 8, 8.1 och 10 med .NET Framework 4.5 på klientsidan

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

    Cmdleten påverkar inte en app som redan är instrumenterad. Det spelar ingen roll om appen hade instrumenterats vid byggtiden genom att SDK lades till i koden, eller under körningen av en tidigare användning av den här cmdleten.

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

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next"></a>Nästa steg

Visa telemetrin:

* [Utforska mått](app-insights-metrics-explorer.md) för att övervaka prestanda och användning
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem
* [Analys](app-insights-analytics.md) för mer avancerade frågor
* [Skapa instrumentpaneler](app-insights-dashboards.md)

Lägg till mer telemetri:

* [Skapa webbtester][availability] så att du är säker på att webbplatsen är aktiv.
* [Lägg till telemetri för webbklienten][usage] om du vill visa undantag från webbsidans kod och lägga till spårningsanrop.
* [Lägg till Application Insights SDK i koden][greenbrown] om du vill lägga till spårnings- och logganrop

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

