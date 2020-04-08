---
title: Felsökning utan data, Application Insights för .NET
description: Ser du inte data i Azure Application Insights? Försök här.
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 34fc51f8f656ec0f630bd984ac1b28fbaa5e4dae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802594"
---
# <a name="troubleshooting-no-data---application-insights-for-netnet-core"></a>Felsökning av inga data – Programinsikter för .NET/.NET Core

## <a name="some-of-my-telemetry-is-missing"></a>En del av min telemetri saknas
*I Application Insights ser jag bara en bråkdel av de händelser som genereras av min app.*

* Om du konsekvent ser samma fraktion, det är förmodligen på grund av adaptiv [provtagning](../../azure-monitor/app/sampling.md). För att bekräfta detta, öppna Sök (från översiktsbladet) och titta på en instans av en begäran eller annan händelse. Längst ned i egenskapsavsnittet klickar du på "..." för att få fullständig fastighetsinformation. Om Antal begäranden > 1 pågår sampling.
* Annars är det möjligt att du når en [datahastighetsgräns](../../azure-monitor/app/pricing.md#limits-summary) för din prisplan. Dessa gränser tillämpas per minut.

*Jag upplever dataförlust slumpmässigt.*

* Kontrollera om du upplever dataförlust på [Telemetri Channel](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost)

* Sök efter kända problem i Telemetri Channel [GitHub repo](https://github.com/Microsoft/ApplicationInsights-dotnet/issues)

*Jag upplever dataförlust i Console App eller i Web App när appen är på väg att sluta.*

* SDK-kanal håller telemetri i buffert och skickar dem i batchar. Om programmet stängs av kan du behöva uttryckligen anropa [Flush()](api-custom-events-metrics.md#flushing-data). Beteendet `Flush()` beror på vilken [kanal](telemetry-channels.md#built-in-telemetry-channels) som används.

## <a name="no-data-from-my-server"></a>Inga data från min server
*Jag har installerat min app på min webbserver, och nu ser jag ingen telemetri från den. Den arbetat OK på min dev maskin.*

* Förmodligen en brandvägg fråga. [Ange brandväggsundantag för Application Insights för att skicka data](../../azure-monitor/app/ip-addresses.md).
* IIS Server kan sakna vissa förutsättningar: .NET Extensibility 4.5 och ASP.NET 4.5.

*Jag [installerade Statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md) på webbservern för att övervaka befintliga appar. Jag ser inga resultat.*

* Se [Felsöka statusövervakare](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="no-add-application-insights-option-in-visual-studio"></a><a name="q01"></a>Inget alternativ för lägg till programinsikter i Visual Studio
*När jag högerklickar på ett befintligt projekt i Solution Explorer visas inga alternativ för programinsikter.*

* Alla typer av .NET-projekt stöds inte av verktygen. Webb- och WCF-projekt stöds. För andra projekttyper, till exempel skrivbords- eller tjänstprogram, kan du fortfarande [lägga till en Application Insights SDK i projektet manuellt](../../azure-monitor/app/windows-desktop.md).
* Kontrollera att du har [Visual Studio 2013 Update 3 eller senare](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Den levereras förinstallerad med Developer Analytics-verktyg, som tillhandahåller Application Insights SDK.
* Välj **Verktyg,** **Tillägg och uppdateringar** och kontrollera att Developer Analytics **Tools** är installerat och aktiverat. Om så är fallet klickar du på **Uppdateringar** för att se om det finns en tillgänglig uppdatering.
* Öppna dialogrutan Nytt projekt och välj ASP.NET webbprogram. Om du ser alternativet Application Insights där installeras verktygen. Om inte, prova att avinstallera och sedan installera utvecklaranalysverktygen igen.

## <a name="adding-application-insights-failed"></a><a name="q02"></a>Det gick inte att lägga till programinsikter
*När jag försöker lägga till Application Insights i ett befintligt projekt visas ett felmeddelande.*

Sannolika orsaker:

* Kommunikationen med application insights-portalen misslyckades. Eller
* Det finns vissa problem med ditt Azure-konto.
* Du har bara [läsbehörighet till prenumerationen eller gruppen där du försökte skapa den nya resursen](../../azure-monitor/app/resources-roles-access-control.md).

Fixa:

* Kontrollera att du har angett inloggningsuppgifter för rätt Azure-konto.
* Kontrollera att du har åtkomst till [Azure-portalen](https://portal.azure.com)i webbläsaren . Öppna Inställningar och se om det finns några begränsningar.
* [Lägg till programstatistik i ditt befintliga projekt:](../../azure-monitor/app/asp-net.md)Högerklicka på projektet i Solution Explorer och välj Lägg till programinsikter.

## <a name="i-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Jag får ett felmeddelande "Instrumenteringsnyckeln kan inte vara tom"
Ser ut som något gick fel medan du installerar Application Insights eller kanske en loggning adapter.

Högerklicka på projektet i Solution Explorer och välj **Application Insights > Konfigurera programstatistik**. Du får en dialogruta som inbjuder dig att logga in på Azure och antingen skapa en Application Insights-resurs eller återanvända en befintlig.

## <a name="nuget-packages-are-missing-on-my-build-server"></a><a name="NuGetBuild"></a>"NuGet paket (s) saknas" på min byggserver
*Allt bygger OK när jag felsöker på min utvecklingsmaskin, men jag får ett NuGet-fel på byggservern.*

Se [NuGet Paketåterställning](https://docs.nuget.org/Consume/Package-Restore) och [automatisk paketåterställning](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Menykommando saknas för att öppna Application Insights från Visual Studio
*När jag högerklickar på projektlösningsutforskaren visas inga programinsiktskommandon eller så visas inget kommandot Öppna programinsikter.*

Sannolika orsaker:

* Om du har skapat application insights-resursen manuellt, eller om projektet är av en typ som inte stöds av application insights-verktygen.
* Utvecklaranalysverktygen är inaktiverade i Visual Studio.
* Visual Studio är äldre än uppdatering 3 från 2013.

Fixa:

* Kontrollera att din Visual Studio-version är 2013 uppdatering 3 eller senare.
* Välj **Verktyg,** **Tillägg och uppdateringar** och kontrollera att Developer **Analytics-verktyg** är installerade och aktiverade. Om så är fallet klickar du på **Uppdateringar** för att se om det finns en tillgänglig uppdatering.
* Högerklicka på projektet i Solution Explorer. Om kommandot **Application Insights > Configure Application Insights**använder du det för att ansluta projektet till resursen i application insights-tjänsten.

Annars stöds inte projekttypen direkt av utvecklaranalysverktygen. Om du vill se din telemetri loggar du in på [Azure-portalen,](https://portal.azure.com)väljer Programstatistik i det vänstra navigeringsfältet och väljer ditt program.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>"Åtkomst nekad" vid öppnandet av Application Insights från Visual Studio
*Menykommandot "Öppna programinsikter" tar mig till Azure-portalen, men jag får ett felmeddelande om åtkomst nekad.*

Microsoft-inloggningen som du senast använde i standardwebbläsaren har inte åtkomst till [den resurs som skapades när Application Insights lades till i den här appen](../../azure-monitor/app/asp-net.md). Det finns två troliga skäl:

* Du har mer än ett Microsoft-konto - kanske ett arbete och ett personligt Microsoft-konto? Inloggningen som du senast använde i standardwebbläsaren var för ett annat konto än det som har åtkomst till att lägga till [Application Insights i projektet](../../azure-monitor/app/asp-net.md).
  * Åtgärdat: Klicka på ditt namn längst upp till höger i webbläsarfönstret och logga ut. Logga sedan in med kontot som har åtkomst. Klicka sedan på Programstatistik i det vänstra navigeringsfältet och välj din app.
* Någon annan har lagt till Application Insights i projektet och de glömde att ge dig [åtkomst till resursgruppen](../../azure-monitor/app/resources-roles-access-control.md) där den skapades.
  * Åtgärdat: Om de använde ett organisationskonto kan de lägga till dig i teamet. eller så kan de ge dig individuell åtkomst till resursgruppen.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>"Tillgång hittades inte" när Application Insights öppnas från Visual Studio
*Menykommandot "Öppna programinsikter" tar mig till Azure-portalen, men jag får ett fel i tillgången hittades inte.*

Sannolika orsaker:

* Application Insights-resursen för ditt program har tagits bort. Eller
* Instrumenteringsnyckeln har angetts eller ändrats i ApplicationInsights.config genom att redigera den direkt, utan att uppdatera projektfilen.

Instrumenteringsnyckeln i ApplicationInsights.config styr var telemetrin skickas. En rad i projektfilen styr vilken resurs som öppnas när du använder kommandot i Visual Studio.

Fixa:

* Högerklicka på projektet i Solution Explorer och välj Application Insights, Konfigurera programstatistik. I dialogrutan kan du antingen välja att skicka telemetri till en befintlig resurs eller skapa en ny. Eller:
* Öppna resursen direkt. Logga in [på Azure-portalen,](https://portal.azure.com)klicka på Programstatistik i det vänstra navigeringsfältet och välj sedan din app.

## <a name="where-do-i-find-my-telemetry"></a>Var hittar jag min telemetri?
*Jag har loggat in på [Microsoft Azure-portalen](https://portal.azure.com)och tittar på Azure-startpanelen. Så var hittar jag mina Application Insights-data?*

* Klicka på Programstatistik i det vänstra navigeringsfältet och sedan på appnamnet. Om du inte har några projekt där måste du [lägga till eller konfigurera Application Insights i webbprojektet](../../azure-monitor/app/asp-net.md).  
  Där ser du några sammanfattningsdiagram. Du kan klicka igenom dem för att se mer detaljer.
* Klicka på knappen Programstatistik i Visual Studio.

## <a name="no-server-data-or-no-data-at-all"></a><a name="q03"></a>Inga serverdata (eller inga data alls)
*Jag körde min app och öppnade sedan tjänsten Application Insights i Microsoft Azure, men alla diagram visar Lär dig hur du samlar in...' eller "Inte konfigurerad".* Eller *bara sidvisning och användardata, men inga serverdata.*

* Kör programmet i felsökningsläge i Visual Studio (F5). Använd programmet för att generera viss telemetri. Kontrollera att du kan se händelser som loggas i utdatafönstret i Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Öppna [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)i portalen Application Insights . Data visas vanligtvis här först.
* Klicka på knappen Uppdatera. Bladet uppdateras regelbundet, men du kan också göra det manuellt. Uppdateringsintervallet är längre för större tidsintervall.
* Kontrollera instrumenteringsknapparna som matchar. På huvudbladet för din app i application insights-portalen, i listrutan **Essentials,** tittar du på **instrumenteringsnyckeln**. Öppna sedan ApplicationInsights.config i projektet i Visual `<instrumentationkey>`Studio och hitta . Kontrollera att de två nycklarna är lika. Om inte:  
  * Klicka på Programstatistik i portalen och leta efter appresursen med rätt nyckel. Eller
  * Högerklicka på projektet i Visual Studio Solution Explorer och välj Application Insights, Configure. Återställ appen för att skicka telemetri till rätt resurs.
  * Om du inte hittar matchande nycklar kontrollerar du att du använder samma inloggningsuppgifter i Visual Studio som i portalen.
* På [instrumentpanelen](https://portal.azure.com)i Microsoft Azure home tittar du på hälsoöversikten för tjänsten. Om det finns några varningsindikationer väntar du tills de har återvänt till OK och stänger och öppnar sedan programbladet Application Insights igen.
* Kolla även [vår status blogg](https://blogs.msdn.microsoft.com/servicemap-status/).
* Har du skrivit någon kod för [serversidan SDK](../../azure-monitor/app/api-custom-events-metrics.md) som `TelemetryClient` kan ändra `TelemetryContext`instrumenteringsnyckeln i instanser eller i? Eller har du skrivit ett [filter eller samplingskonfiguration](../../azure-monitor/app/api-filtering-sampling.md) som kan filtrera bort för mycket?
* Om du har redigerat ApplicationInsights.config kontrollerar du noggrant konfigurationen av [TelemetryInitializers och TelemetriProcessors](../../azure-monitor/app/api-filtering-sampling.md). En felaktigt namngiven typ eller parameter kan leda till att SDK inte skickar några data.

## <a name="no-data-on-page-views-browsers-usage"></a><a name="q04"></a>Inga data om sidvisningar, webbläsare, användning
*Jag ser data i diagram för serversvarstid och serverbegäranden, men inga data i lästid för sidvyn eller i bladet Webbläsare eller Användning.*

Data kommer från skript på webbsidorna. 

* Om du har lagt till Application Insights i ett befintligt webbprojekt [måste du lägga till skripten för hand](../../azure-monitor/app/javascript.md).
* Kontrollera att Internet Explorer inte visar webbplatsen i kompatibilitetsläge.
* Använd webbläsarens felsökningsfunktion (F12 i vissa webbläsare och välj sedan Nätverk) `dc.services.visualstudio.com`för att kontrollera att data skickas till .

## <a name="no-dependency-or-exception-data"></a>Inga beroende- eller undantagsdata
Se [beroendetelemetri](../../azure-monitor/app/asp-net-dependencies.md) och [undantagstelemetri](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Inga prestandadata
Prestandadata (CPU, IO-hastighet och så vidare) är tillgängliga för [Java-webbtjänster](../../azure-monitor/app/java-collectd.md), [Windows-skrivbordsappar,](../../azure-monitor/app/windows-desktop.md) [IIS-webbappar och -tjänster om du installerar statusövervakare](../../azure-monitor/app/monitor-performance-live-website-now.md)och [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). hittar du den under Inställningar, Servrar.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Inga (server)-data sedan jag publicerade appen på min server
* Kontrollera att du faktiskt kopierade hela Microsoft. ApplicationInsights DLL till servern, tillsammans med Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* I brandväggen kan du behöva [öppna vissa TCP-portar](../../azure-monitor/app/ip-addresses.md).
* Om du måste använda en proxy för att skicka ut från företagets nätverk anger du [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) i Web.config
* Windows Server 2008: Kontrollera att du har installerat följande uppdateringar: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag brukade se data, men det har slutat
* Kontrollera [statusbloggen](https://blogs.msdn.com/b/applicationinsights-status/).
* Har du nått din månatliga kvot av datapunkter? Öppna inställningar/kvot och priser för att ta reda på. Om så är fallet kan du uppgradera din plan eller betala för ytterligare kapacitet. Se [prissystemet](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte all data jag förväntar mig
Om ditt program skickar mycket data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, kan [funktionen för adaptiv sampling](../../azure-monitor/app/sampling.md) fungera och skicka endast en procentandel av din telemetri.

Du kan inaktivera den, men detta rekommenderas inte. Provtagningen är utformad så att tillhörande telemetri överförs korrekt för diagnostiska ändamål.

## <a name="client-ip-address-is-0000"></a>Klientens IP-adress är 0.0.0.0

Den 5 februari 2018 meddelade vi att vi tog bort loggning av klient-IP-adressen. Detta påverkar inte geografisk plats.

> [!NOTE]
> Om du behöver de första 3 oktetterna på IP-adressen kan du använda en [telemetriinitierare](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#addmodify-properties-itelemetryinitializer) för att lägga till ett anpassat attribut.
> Detta påverkar inte data som samlats in före den 5 februari 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Felaktiga geografiska data i användartelemetri
Dimensionerna för stad, region och land härleds från IP-adresser och är inte alltid korrekta. Dessa IP-adresser bearbetas för plats först och sedan ändras till 0.0.0.0 som ska lagras.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? 4.6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera 4.6 för varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="troubleshooting-logs"></a>Felsöka loggar

Följ de här anvisningarna för att samla in felsökningsloggar för ramverket.

### <a name="net-framework"></a>.NET Framework

1. Installera [paketet Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) från NuGet. Den version du installerar måste matcha den aktuella installerade versionen av`Microsoft.ApplicationInsighs`

2. Ändra filen applicationinsights.config så att den innehåller följande:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    Programmet måste ha skrivbehörighet till den konfigurerade platsen

3. Omstartsprocessen så att de nya inställningarna plockas upp av SDK

4. Återställ ändringarna när du är klar.

### <a name="net-core"></a>.NET Core

1. Installera [paketet Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) från NuGet. Den version du installerar måste matcha den aktuella installerade versionen av`Microsoft.ApplicationInsights`

Den senaste versionen av Microsoft.ApplicationInsights.AspNetCore är 2.8.2 och refererar till Microsoft.ApplicationInsights version 2.11.2. Därför bör den version av Microsoft.AspNet.ApplicationInsights.HostingStartup som ska installeras vara 2.11.2

2. Ändra `ConfigureServices` metod `Startup.cs` i klassen.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Programmet måste ha skrivbehörighet till den konfigurerade platsen

3. Omstartsprocessen så att de nya inställningarna plockas upp av SDK

4. Återställ ändringarna när du är klar.


## <a name="collect-logs-with-perfview"></a><a name="PerfView"></a>Samla in loggar med PerfView
[PerfView](https://github.com/Microsoft/perfview) är ett kostnadsfritt diagnostik- och prestandaanalysverktyg som hjälper till att isolera CPU, minne och andra problem genom att samla in och visualisera diagnostikinformation från många källor.

Application Insights SDK-logg EventSource självfelsöker loggar som kan fångas in av PerfView.

Så här samlar du in loggar, hämtar PerfView och kör det här kommandot:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-EventCounterCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Du kan ändra dessa parametrar efter behov:
- **MaxCollectSec**. Ange den här parametern för att förhindra att PerfView körs på obestämd tid och påverkar serverns prestanda.
- **OnlyProviders**. Ange den här parametern för att endast samla in loggar från SDK. Du kan anpassa den här listan baserat på dina specifika undersökningar. 
- **NoGui**. Ange den här parametern för att samla in loggar utan guidning.


Om du vill ha mer information läser du
- [Registrera prestandaspårningar med PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Händelsekällor för programinsikter](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="how-to-remove-application-insights"></a>Så här tar du bort application insights

Lär dig hur du tar bort Programstatistik i Visual Studio genom att följa stegen i [borttagningsartikeln](../../azure-monitor/app/remove-application-insights.md).

## <a name="still-not-working"></a>Fortfarande inte fungerar ...
* [Forum för Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
