---
title: Övervaka en ASP.NET-livewebbapp med Azure Application Insights | Microsoft Docs
description: Övervaka prestanda för en webbplats utan att distribuera den igen. Fungerar med ASP.NET web appar som finns lokalt eller i virtuella datorer.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: mbullwin
ms.openlocfilehash: 0587782cbfa31f7b397b950a752040cc678cf7d7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085821"
---
# <a name="instrument-web-apps-at-runtime-with-application-insights-status-monitor"></a>Instrumentera webbappar vid körning med Application Insights Status Monitor

Du kan instrumentera en live-webbapp med Azure Application Insights utan att behöva ändra eller omdistribuera din kod. Du behöver en [Microsoft Azure](https://azure.com)-prenumeration.

Övervakaren används för att instrumentera ett .NET-program i IIS antingen lokalt eller i en virtuell dator.

- Om din app distribueras till Azure app services, följer du [instruktionerna](azure-web-apps.md).
- Om din app distribueras i en Azure VM, kan du växla på Application Insights-övervakning från Azures kontrollpanel.
- (Det finns även olika artiklar om att arrangera [Azure Cloud Services](../../azure-monitor/app/cloudservices.md).)


![Skärmbild av App Insights översikt diagram som innehåller information om misslyckade förfrågningar, svarstid för servern och servern för serverbegäranden](./media/monitor-performance-live-website-now/overview-graphs.png)

Du kan välja mellan två vägar kan tillämpa Application Insights på dina .NET-webbprogram:

* **Byggtid:** [Lägg till Application Insights SDK] [ greenbrown] till webbappens kod.
* **Körtid:** Instrumentera webbappen på servern, som beskrivs nedan, utan att bygga om och omdistribuera koden.

> [!NOTE]
> Om du använder version tid instrumentation körningstiden instrumention inte kommer att fungera även om den är påslagen.

Här är en sammanfattning av vad du får med respektive väg:

|  | Byggtid | Körtid |
| --- | --- | --- |
| Förfrågningar och undantag |Ja |Ja |
| [Mer detaljerade undantag](../../azure-monitor/app/asp-net-exceptions.md) | |Ja |
| [Beroendediagnostik](../../azure-monitor/app/asp-net-dependencies.md) |I .NET 4.6+, men färre detaljer |Ja, fullständiga detaljer: resultatkoder, SQL-kommandotext, HTTP verb|
| [Systemprestandaräknare](../../azure-monitor/app/performance-counters.md) |Ja |Ja |
| [API för anpassad telemetri][api] |Ja |Nej |
| [Spårningsloggsintegrering](../../azure-monitor/app/asp-net-trace-logs.md) |Ja |Nej |
| [Sidvy och användardata](../../azure-monitor/app/javascript.md) |Ja |Nej |
| Du måste återskapa koden |Ja | Nej |



## <a name="monitor-a-live-iis-web-app"></a>Övervaka en IIS-livewebbapp

Om din app finns på en IIS-server aktiverar du Application Insights med hjälp av Statusövervakaren.

1. Logga in med administratörsbehörighet på IIS-webbservern.
2. Om Application Insights Status Monitor inte redan är installerad [ladda ned och kör installationsprogrammet](#download)
3. I statusövervakaren väljer du den installerade webbappen eller en webbplats som du vill övervaka. Logga in med dina Azure autentiseringsuppgifter.

    Konfigurera den resurs där du vill visa resultatet i Application Insights-portalen. (Normalt är det bäst att skapa en ny resurs. Välj en befintlig resurs om du redan har [webbtester][availability] eller [klientövervakning][client] för den här appen.) 

    ![Välj en app och en resurs.](./media/monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Starta om IIS.

    ![Välj Starta om överst i dialogrutan.](./media/monitor-performance-live-website-now/appinsights-036-restart.png)

    Webbtjänsten avbryts en liten stund.

## <a name="customize-monitoring-options"></a>Anpassa övervakningsalternativ

När Application Insights aktiveras läggs DLL-filer och ApplicationInsights.config till i webbappen. Du kan [redigera .config-filen](../../azure-monitor/app/configuration-with-applicationinsights-config.md) och ändra en del av alternativen.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>När du publicerar appen igen ska du återaktivera Application Insights

Innan du publicerar din app igen bör du överväga att [lägga till Applications Insights i koden i Visual Studio][greenbrown]. Du får mer detaljerad telemetri och möjligheten att skriva anpassad telemetri.

Om du vill publicera på nytt utan att lägga till Application Insights i koden, tänk på att distributionsprocessen kan ta bort DLL-filer och ApplicationInsights.config från den publicerade webbplatsen. Därför:

1. Om du har redigerat ApplicationInsights.config tar du en kopia av den innan du publicerar appen igen.
2. Publicera om appen.
3. Återaktivera övervakning med Application Insights. (Använd lämplig metod: antingen Azure-kontrollpanelen för webbappar eller statusövervakaren på en IIS-värd.)
4. Återställa redigeringar som har utförts på .config-filen.


## <a name="troubleshoot"></a>Felsökning

### <a name="confirm-a-valid-installation"></a>Bekräfta en giltig installation 

Det här är några steg som du kan utföra för att bekräfta att installationen lyckades.

- Bekräfta att filen applicationInsights.config finns i målkatalogen för appen och innehåller din ikey.

- Om du misstänker att data saknas kan du köra en enkel fråga i [Analytics](../log-query/get-started-portal.md) att lista alla molnroller som skickar telemetri.
  ```Kusto
  union * | summarize count() by cloud_RoleName, cloud_RoleInstance
  ```

- Om du vill kontrollera att Application Insights är har bifogats kan du köra [Sysinternals referensen](https://docs.microsoft.com/sysinternals/downloads/handle) i ett kommando fönstret för att bekräfta att applicationinsights.dll har lästs in av IIS.
  ```cmd
  handle.exe /p w3wp.exe
  ```


### <a name="cant-connect-no-telemetry"></a>Går det inte att ansluta? Ser du ingen telemetri?

* Öppna [de nödvändiga utgående portarna](../../azure-monitor/app/ip-addresses.md#outgoing-ports) i serverns brandvägg för att Statusövervakare ska fungera.

### <a name="unable-to-login"></a>Det går inte att logga in

* Om Status Monitor inte kan logga in, gör en installation från kommandoraden i stället. Statusövervakaren försöker logga in för att samla in din ikey, men du kan ange detta manuellt med hjälp av kommandot:

```powershell
Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'
Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000
```

### <a name="could-not-load-file-or-assembly-systemdiagnosticsdiagnosticsource"></a>Det gick inte att läsa in filen eller sammansättningen 'System.Diagnostics.DiagnosticSource'

Du får det här felet när du har aktiverat Application Insights. Det beror på att installationsprogrammet ersätter denna DLL-fil i bin-katalogen.
Åtgärda uppdatera filen web.config:

```xml
<dependentAssembly>
    <assemblyIdentity name="System.Diagnostics.DiagnosticSource" publicKeyToken="cc7b13ffcd2ddd51"/>
    <bindingRedirect oldVersion="0.0.0.0-4.*.*.*" newVersion="4.0.2.1"/>
</dependentAssembly>
```

Vi följer upp problemet [här](https://github.com/Microsoft/ApplicationInsights-Home/issues/301).


### <a name="application-diagnostic-messages"></a>Diagnostikmeddelanden för program

* Öppna Status Monitor och välj ditt program i den vänstra rutan. Kontrollera om det finns några diagnostikmeddelanden för det här programmet i avsnittet ”Konfigurationsmeddelanden”:

  ![Öppna bladet Prestanda om du vill visa information om förfrågningar, svarstider, beroenden och andra data](./media/monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
  
### <a name="detailed-logs"></a>Detaljerade loggar

* Som standard kommer statusövervakaren utdata diagnostiska loggar på: `C:\Program Files\Microsoft Application Insights\Status Monitor\diagnostics.log`

* Om du vill spara utförliga loggar, ändra konfigurationsfilen: `C:\Program Files\Microsoft Application Insights\Status Monitor\Microsoft.Diagnostics.Agent.StatusMonitor.exe.config` och Lägg till `<add key="TraceLevel" value="All" />` till den `appsettings`.
Starta sedan om statusövervakaren.

### <a name="insufficient-permissions"></a>Otillräckliga behörigheter
  
* Om du ser ett meddelande om ”otillräcklig behörighet” på servern provar du följande:
  * I IIS-hanteraren väljer du programpoolen, öppnar **Avancerade inställningar** och noterar identiteten under **Processmodell**.
  * På kontrollpanelen för Datorhantering lägger du till den här identiteten i gruppen Användare av prestandaövervakning.

### <a name="conflict-with-systems-center-operations-manager"></a>Konflikt med System Center Operations Manager

* Om du har MMA/SCOM (Systems Center Operations Manager) installerat på servern kan vissa versioner vara i konflikt med varandra. Avinstallera både SCOM och Status Monitor och installera om de senaste versionerna.

### <a name="failed-or-incomplete-installation"></a>Misslyckad eller ofullständig installation

Om övervakaren misslyckas under en installation, kan du bli med en ofullständig installation som Status Monitor inte kan återställa från. Detta kräver en manuell återställning.

Ta bort någon av dessa filer finns i programkatalogen:
- Alla DLL: er i din bin-katalogen som börjar med antingen ”Microsoft.AI”. eller ”Microsoft.ApplicationInsights”.
- Denna DLL-fil i bin-katalogen ”Microsoft.Web.Infrastructure.dll”
- Denna DLL-fil i bin-katalogen ”System.Diagnostics.DiagnosticSource.dll”
- Ta bort ”App_Data\packages” i programkatalogen
- Ta bort ”applicationinsights.config” i programkatalogen


### <a name="additional-troubleshooting"></a>Ytterligare felsökning

* Se ytterligare [felsökning][qna].

## <a name="system-requirements"></a>Systemkrav
Operativsystemstöd för Application Insights Status Monitor på servern:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows server 2012 R2
* Windows Server 2016

med senaste SP och .NET Framework 4.5

På klientsidan: Windows 7, 8, 8.1 och 10 med .NET Framework 4.5

IIS-support är: IIS 7, 7.5, 8, 8.5 (IIS krävs)

## <a name="automation-with-powershell"></a>Automatisering med PowerShell
Du kan starta och stoppa övervakningen med hjälp av PowerShell i din IIS-server.

Importera först Application Insights-modulen:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Ta reda på vilka appar som övervakas:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Valfritt) Namnet på en webbapp.
* Visar Application Insights-övervakningsstatusen för varje webbapp (eller den namngivna appen) på den här IIS-servern.
* Returnerar `ApplicationInsightsApplication` för varje app:

  * `SdkState==EnabledAfterDeployment`: Appen övervakas och instrumenterades vid körningstillfället, antingen av verktyget Status Monitor eller med `Start-ApplicationInsightsMonitoring`.
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

## <a name="questions"></a>Frågor om Statusövervakaren

### <a name="what-is-status-monitor"></a>Vad är Statusövervakaren?

Ett program som du installerar på din IIS-webbserver. Det hjälper dig instrumentera och konfigurera webbappar. 

### <a name="when-do-i-use-status-monitor"></a>När ska jag använda Statusövervakaren?

* När du instrumenterar en webbapp som körs på IIS-servern, även om den redan har startats.
* För att få ytterligare telemetri för webbappar som har [skapats med Application Insights SDK](../../azure-monitor/app/asp-net.md) vid kompilering. 

### <a name="can-i-close-it-after-it-runs"></a>Kan jag stänga den när den körs?

Ja. Du kan stänga den efter att den har instrumenterat de webbplatser som du har valt.

Den samlar inte in telemetri på egen hand. Den bara konfigurerar webbappar och anger vissa behörigheter.

### <a name="what-does-status-monitor-do"></a>Vad gör Statusövervakaren?

När du har valt en webbapp som Statusövervakaren ska instrumentera gör den följande:

* Laddar ned och placerar Application Insights-sammansättningar och ApplicationInsights.config-filen i webbappens binärfilsmapp.
* Aktiverar CLR-profilering för att samla in beroendeanrop.

### <a name="what-version-of-application-insights-sdk-does-status-monitor-install"></a>Vilken version av Application Insights SDK installeras statusövervakaren?

Från och med nu installera statusövervakaren bara Application Insights SDK version 2.3 eller 2.4.

### <a name="do-i-need-to-run-status-monitor-whenever-i-update-the-app"></a>Måste jag köra Statusövervakaren när jag uppdaterar appen?

Inte om du distribuerar om inkrementellt. 

Om du väljer alternativet Ta bort befintliga filer i publiceringen måste du köra om Statusövervakaren för att konfigurera Application Insights.

### <a name="what-telemetry-is-collected"></a>Vilken telemetri samlas in?

För program som du endast instrumenterar vid körning med Statusövervakaren:

* HTTP-begäranden
* Anrop till beroenden
* Undantag
* Prestandaräknare

För program som redan har instrumenterats vid kompilering:

 * Processräknare.
 * Beroendeanrop (.NET 4.5;), returvärden i beroendeanrop (.NET 4.6).
 * Undantag i stackspårningsvärden.

[Läs mer](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="download"></a>Ladda ner statusövervakaren

- Ladda ned och kör den [installationsprogrammet för Status Monitor](https://go.microsoft.com/fwlink/?LinkId=506648)
- Eller kör [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) och söker efter det i för Application Insights Status Monitor.

## <a name="next"></a>Nästa steg

Visa telemetrin:

* [Utforska mått](../../azure-monitor/app/metrics-explorer.md) för att övervaka prestanda och användning
* [Sök efter händelser och loggar][diagnostic] för att diagnostisera problem
* [Analys](../../azure-monitor/app/analytics.md) för mer avancerade frågor
* [Skapa instrumentpaneler](../../azure-monitor/app/app-insights-dashboards.md)

Lägg till mer telemetri:

* [Skapa webbtester][availability] så att du är säker på att webbplatsen är aktiv.
* [Lägg till telemetri för webbklienten][usage] om du vill visa undantag från webbsidans kod och lägga till spårningsanrop.
* [Lägg till Application Insights SDK i koden][greenbrown] om du vill lägga till spårnings- och logganrop

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[availability]: monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[usage]: ../../azure-monitor/app/javascript.md
