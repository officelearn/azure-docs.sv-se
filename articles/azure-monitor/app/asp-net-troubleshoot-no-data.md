---
title: Felsökning utan data, Application Insights för .NET
description: Inte se data i Azure Application Insights? Prova här.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 11d63889555d6899df07e83168813994907476af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691209"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Felsökning utan data, Application Insights för .NET
## <a name="some-of-my-telemetry-is-missing"></a>Några av Mina telemetri saknas
*I Application Insights visas bara en bråkdel av de händelser som genereras av min app.*

* Om du konsekvent ser den samma andel, beror det troligen på grund av anpassningsbar [sampling](../../azure-monitor/app/sampling.md). För att bekräfta detta, Öppna sökning (från översiktsbladet) och titta på en instans av en begäran eller händelse. Klicka på ”...” för att få fullständig egenskapsinformationen längst ned i egenskapsavsnittet. Om begär antal > 1 och sedan sampling är i drift.
* I annat fall är det möjligt att du får en [data hastighetsbegränsning](../../azure-monitor/app/pricing.md#limits-summary) för prisplanen. Dessa begränsningar tillämpas per minut.

## <a name="no-data-from-my-server"></a>Inga data från Min server
*Jag har installerat min app på webbservern och nu jag ser inte någon telemetri från den. Det fungerade OK på min utvecklingsdator.*

* Förmodligen ett brandväggen problem. [Ange brandväggsundantag för Application Insights att skicka data](../../azure-monitor/app/ip-addresses.md).
* IIS-servern kan sakna vissa krav: .NET Extensibility 4.5 och ASP.NET 4.5.

*Jag [installerat statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md) på webbservern för att övervaka befintliga appar. Jag ser inte några resultat.*

* Se [felsökning statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>Alternativet ”Lägg till Application Insights” i Visual Studio
*När jag högerklickar på ett befintligt projekt i Solution Explorer kan se jag inte några alternativ för Application Insights.*

* Inte alla typer av .NET-projekt stöds av verktygen. Webb-och WCF stöds. För andra projekttyper av som skrivbord eller tjänsten kan du fortfarande [lägga till en Application Insights SDK i ditt projekt manuellt](../../azure-monitor/app/windows-desktop.md).
* Kontrollera att du har [Visual Studio 2013 Update 3 eller senare](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Den levereras förinstallerad med Developer Analytics-verktyg som ger Application Insights SDK.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrollera att **Developer Analytics Tools** är installerat och aktiverat. I så, fall klickar du på **uppdateringar** att se om det finns en uppdatering.
* Öppna dialogrutan Nytt projekt och välj ASP.NET-webbprogram. Om du ser det alternativet Application Insights är verktygen installerade. Om inte, avinstallera och sedan installera Application Insights Tools.

## <a name="q02"></a>Det gick inte att lägga till Application Insights
*När jag försöker lägga till Application Insights till ett befintligt projekt, visas ett felmeddelande.*

Troliga orsaker:

* Det gick inte att kommunicera med Application Insights-portalen; eller
* Det finns problem med ditt Azure-konto;
* Du behöver bara [läsbehörighet till den prenumeration eller en grupp där du försökte skapa den nya resursen](../../azure-monitor/app/resources-roles-access-control.md).

Fix:

* Kontrollera att du angett inloggningsuppgifter för rätt Azure-konto.
* I webbläsaren, kontrollera att du har åtkomst till den [Azure-portalen](https://portal.azure.com). Öppna inställningar och se om det finns några begränsningar.
* [Lägg till Application Insights i projektet befintliga](../../azure-monitor/app/asp-net.md): Högerklicka på projektet i Solution Explorer och välj ”Lägg till Application Insights”.
* Om det fortfarande inte fungerar följer du de [manuell proceduren](../../azure-monitor/app/windows-services.md) lägga till en resurs i portalen och sedan lägga till SDK: N i projektet.

## <a name="emptykey"></a>Det uppstår ett fel ”instrumenteringsnyckeln kan inte vara tom”
Det verkar som det uppstod ett fel när du installerar Application Insights eller kanske en loggning nätverkskort.

Högerklicka på projektet i Solution Explorer och välj **Application Insights > Konfigurera Application Insights**. Du får en dialogruta där du kan logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig.

## <a name="NuGetBuild"></a> ”NuGet-paket saknas” på min build-server
*Allt bygger OK när jag felsökning på min utvecklingsdator, men jag får ett NuGet-fel på build-servern.*

Se [NuGet-Paketåterställning](https://docs.nuget.org/Consume/Package-Restore) och [automatisk Paketåterställning](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Saknas menyn kommando för att öppna Application Insights från Visual Studio
*När jag högerklickar på mitt projekt Solution Explorer kan jag ser inte alla Application Insights-kommandon eller ett öppna Application Insights-kommandot finns inte.*

Troliga orsaker:

* Om du har skapat Application Insights-resursen manuellt, eller om projektet är av en typ som inte stöds av Application Insights-verktyg.
* Developer Analytics tools är inaktiverade i din Visual Studio.
* Din Visual Studio är äldre än 2013 Update 3.

Fix:

* Kontrollera din version av Visual Studio 2013 update 3 eller senare.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrollera att **Developer Analytics tools** är installerat och aktiverat. I så, fall klickar du på **uppdateringar** att se om det finns en uppdatering.
* Högerklicka på projektet i Solution Explorer. Om du ser kommandot **Application Insights > Konfigurera Application Insights**, använda den för att ansluta ditt projekt till resursen i Application Insights-tjänsten.

I annat fall stöds din projekttyp inte direkt av Application Insights-verktygen. Om du vill se din telemetri, logga in på den [Azure-portalen](https://portal.azure.com), Välj Application Insights i det vänstra navigeringsfältet och välj ditt program.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>”Åtkomst nekad” på att öppna Application Insights från Visual Studio
*Kommandot ”Öppna Application Insights” kommer jag till Azure-portalen, men jag får felmeddelandet ”åtkomst nekad”.*

Microsoft inloggningen som du senast använde i din standardwebbläsare har inte åtkomst till [resursen som skapades när Application Insights har lagts till i den här appen](../../azure-monitor/app/asp-net.md). Det finns två sannolika orsaker:

* Du har mer än en Microsoft-konto – kanske ett arbets- och ett personligt microsoftkonto? Inloggning som du senast använde i din standardwebbläsare var för ett annat konto än det som har åtkomst till [Lägg till Application Insights i projektet](../../azure-monitor/app/asp-net.md).
  * Fix: Klicka på ditt namn på överst till höger i webbläsarfönstret och logga ut. Logga sedan in med det konto som har åtkomst. Klicka på Application Insights på det vänstra navigeringsfältet och välj din app.
* Någon annan har lagt till Application Insights i projektet och de har glömt att ge dig [åtkomst till resursgruppen](../../azure-monitor/app/resources-roles-access-control.md) i som den skapades.
  * Fix: Om de har använt ett organisationskonto, kan de lägga till dig i teamet; eller de kan bevilja enskilda åtkomst till resursgruppen.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Tillgången kunde inte hittas ”med att öppna Application Insights från Visual Studio
*Kommandot ”Öppna Application Insights” kommer jag till Azure-portalen, men jag får felmeddelandet ”Det gick inte att hitta tillgången'.*

Troliga orsaker:

* Application Insights-resurs för ditt program har tagits bort; eller
* Instrumenteringsnyckeln angavs eller ändrades i ApplicationInsights.config genom att redigera direkt, utan att uppdatera projektfilen.

Instrumenteringsnyckeln i ApplicationInsights.config kontroller där telemetri som skickas. En rad i projektfilen styr vilken resurs som öppnas när du använder kommandot i Visual Studio.

Fix:

* Högerklicka på projektet i Solution Explorer och välj Application Insights, konfigurera Application Insights. I dialogrutan kan du antingen välja att skicka telemetri till en befintlig resurs eller skapa en ny. Eller:
* Öppna resursen direkt. Logga in på [Azure-portalen](https://portal.azure.com), klickar du på Application Insights i det vänstra navigeringsfältet och välj sedan din app.

## <a name="where-do-i-find-my-telemetry"></a>Var hittar jag min telemetri?
*Jag har loggat in på den [Microsoft Azure-portalen](https://portal.azure.com), och jag tittar på instrumentpanelen för Azure hem. Så var hittar jag mina Application Insights-data?*

* På det vänstra navigeringsfältet, klickar du på Application Insights och sedan appnamnet på din. Om du inte har några projekt det kan du behöva [lägga till eller konfigurera Application Insights i webbprojektet](../../azure-monitor/app/asp-net.md).  
  Det visas en sammanfattning av diagram. Du kan klicka igenom dem för att se mer information.
* Klicka på knappen Application Insights i Visual Studio medan du felsöker din app.

## <a name="q03"></a> Ingen server-data (eller inga data alls)
*Jag har kört appen och sedan öppna Application Insights-tjänsten i Microsoft Azure, men alla diagram visar ”Lär dig hur du samlar in...” eller ”ej konfigurerad”.* Eller, *endast sidvy och användardata, men inga serverdata.*

* Kör programmet i felsökningsläge i Visual Studio (F5). Använda programmet för att generera telemetri. Kontrollera att du kan se händelserna som loggas i utdatafönstret i Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* Öppna Application Insights-portalen [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md). Data visas vanligtvis här först.
* Klicka på Uppdatera. Bladet uppdaterar själva regelbundet, men du kan också göra det manuellt. Uppdateringsintervall är längre för större tidsintervall.
* Kontrollera de instrumentation nycklarna matchar. Huvudbladet innehåller för din app i Application Insights-portalen i den **Essentials** listrutan, titta på **instrumenteringsnyckeln**. I ditt projekt i Visual Studio, öppna ApplicationInsights.config och hitta den `<instrumentationkey>`. Kontrollera att två nycklar är lika med. Om inte:  
  * I portalen klickar du på Application Insights och leta efter den appresursen med rätt nyckel; eller
  * Högerklicka på projektet i Visual Studio Solution Explorer och välj Application Insights, konfigurera. Återställ appen för att skicka telemetrin till rätt resurs.
  * Om du inte kan hitta de matchande nycklarna, kontrollerar du att du använder samma autentiseringsuppgifter logga in i Visual Studio som i på portalen.
* I den [home Microsoft Azure-instrumentpanelen](https://portal.azure.com), titta på kartan Tjänstehälsa. Om det finns några aviseringar indikationer, vänta tills de har kommit tillbaka till OK och stänger och öppnar ditt programblad i Application Insights.
* Kontrollera också [vår status blogg](https://blogs.msdn.microsoft.com/servicemap-status/).
* Har du skriva kod för den [SDK för serversidan](../../azure-monitor/app/api-custom-events-metrics.md) som kan komma att ändras instrumenteringsnyckeln i `TelemetryClient` instanser eller i `TelemetryContext`? Eller har du skriva en [filter eller sampling](../../azure-monitor/app/api-filtering-sampling.md) som kanske filtreras för mycket?
* Om du har redigerat ApplicationInsights.config noggrant kontrollera konfigurationen för [TelemetryInitializers och TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). En typ av fel namn eller parameter kan orsaka SDK, för att skicka inga data.

## <a name="q04"></a>Inga data om sidvisningar, webbläsare, användning
*Jag se data i svarstid för servern och servern för serverbegäranden, men inga data i vyn sidinläsningstid eller i bladen webbläsare och användning.*

Data kommer från skript i webbsidor. 

* Om du har lagt till Application Insights till ett befintligt webbprojekt [du måste lägga till skripten manuellt](../../azure-monitor/app/javascript.md).
* Se till att Internet Explorer inte visar webbplatsen i kompatibilitetsläge.
* Använda webbläsarens debug-funktionen (F12 på vissa webbläsare, väljer nätverket) för att verifiera att data som skickas till `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Inga data beroende eller undantag
Se [beroendetelemetri](../../azure-monitor/app/asp-net-dependencies.md) och [undantagstelemetri](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Inga prestandadata
Prestandadata (CPU, IO-frekvens och så vidare) är tillgänglig för [Java webbtjänster](../../azure-monitor/app/java-collectd.md), [Windows-skrivbordsappar](../../azure-monitor/app/windows-desktop.md), [IIS webbprogram och tjänster om du installerar statusövervakaren](../../azure-monitor/app/monitor-performance-live-website-now.md), och [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Du hittar det under inställningar för servrar.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Inga (server) data eftersom jag har publicerat appen till Min server
* Kontrollera att du faktiskt kopieras alla Microsoft. ApplicationInsights DLL-filer till servern, tillsammans med Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* I brandväggen, du kan behöva [öppna vissa TCP-portar](../../azure-monitor/app/ip-addresses.md).
* Om du behöver använda en proxyserver för att skicka utanför företagets nätverk, ställa in [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) i Web.config
* Windows Server 2008: Kontrollera att du har installerat följande uppdateringar: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag använde för att se data, men den har stoppats
* Kontrollera den [status blogg](https://blogs.msdn.com/b/applicationinsights-status/).
* Har du nått din månatliga kvot för datapunkter? Öppna inställningar/kvoter och priser för att ta reda på. I så fall kan du uppgradera din plan eller betala för ytterligare kapacitet. Se den [priser schema](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Jag ser inte alla data som jag förväntar mig
Om ditt program skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, den [Adaptiv sampling](../../azure-monitor/app/sampling.md) funktionen kan användas och skicka bara en del av telemetrin.

Du kan inaktivera det, men detta rekommenderas inte. Sampling är utformat så att relaterad telemetri är korrekt överförs i diagnostiskt syfte.

## <a name="client-ip-address-is-0000"></a>Klientens IP-adress är 0.0.0.0

Februari 5 2018 meddelade vi att vi har tagit bort loggning av klient-IP-adress. Detta påverkar inte geografisk plats.

> [!NOTE]
> Om du behöver först 3 oktetter i IP-adress kan du använda en [telemetriinitieraren](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) att lägga till ett anpassat attribut.
> Detta påverkar inte data som samlats in före den 5 februari 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Fel geografiska data i användaren telemetri
Den ort, region och land dimensioner härleds från IP-adresser och alltid är inte korrekt. Dessa IP-adresser bearbetas för platsen först och sedan ändrades till 0.0.0.0 ska lagras.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? 4.6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera 4.6 för varje roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="troubleshooting-logs"></a>Felsökningsloggar

Följ dessa instruktioner för att avbilda felsökningsloggarna för ditt ramverk.

### <a name="net-framework"></a>.NET Framework

1. Installera den [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) från NuGet. Den version som du installerar måste matcha den aktuella installerade versionen av `Microsoft.ApplicationInsighs`

2. Ändra filen applicationinsights.config för att inkludera följande:

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

3. Starta om processen så att de nya inställningarna fångas upp av SDK

4. När du är klar kan du återställa dessa ändringar.

### <a name="net-core"></a>.NET Core

1. Installera den [Microsoft.AspNetCore.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.ApplicationInsights.HostingStartup) från NuGet. Den version som du installerar måste matcha den aktuella installerade versionen av `Microsoft.ApplicationInsights`

2. Ändra `ConfigureServices` -metod i din `Startup.cs` klass.:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    Programmet måste ha skrivbehörighet till den konfigurerade platsen

3. Starta om processen så att de nya inställningarna fångas upp av SDK

4. När du är klar kan du återställa dessa ändringar.

## <a name="still-not-working"></a>Fortfarande fungerar inte...
* [Application Insights-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
