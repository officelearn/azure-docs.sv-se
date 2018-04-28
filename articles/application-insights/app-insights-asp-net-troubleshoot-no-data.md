---
title: Felsökning utan data, Application Insights för .NET
description: Inte visa data i Azure Application Insights? Försök här.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 951a3217d795df6360cd3cfa2d47db08c11f978e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Felsökning utan data, Application Insights för .NET
## <a name="some-of-my-telemetry-is-missing"></a>Vissa av min telemetri saknas
*I Application Insights visas bara en del av de händelser som genereras av min app.*

* Om du alltid ser samma när, beror det troligen på grund av anpassningsbar [provtagning](app-insights-sampling.md). För att bekräfta detta, Öppna sökning (från bladet översikt) och titta på en instans av en begäran eller händelse. Klicka på ”...” för att få fullständig egenskapsinformationen längst ned i egenskapsavsnittet. Om begär antal > 1 sedan provtagning är i drift. 
* I annat fall är det möjligt att du träffa en [data hastighetsbegränsning](app-insights-pricing.md#limits-summary) för din prisavtal. Dessa begränsningar tillämpas per minut.

## <a name="no-data-from-my-server"></a>Inga data från servern
*Jag har installerat mitt program på webbservern och nu visas inte alla telemetri från den. Det fungerade OK på datorn dev.*

* Förmodligen en brandvägg problemet. [Ange brandväggsundantag för Application Insights att skicka data](app-insights-ip-addresses.md).
* IIS-servern kan sakna vissa krav: .NET Extensibility 4.5 och ASP.NET 4.5.

*Jag [installerat statusövervakaren](app-insights-monitor-performance-live-website-now.md) på webbservern för att övervaka befintliga appar. Det visas inte några resultat.*

* Se [felsökning statusövervakaren](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights). 

## <a name="q01"></a>Alternativet ”Lägg till Application Insights' i Visual Studio
*När jag högerklickar på ett befintligt projekt i Solution Explorer, visas inte alla alternativ som Application Insights.*

* Inte alla typer av .NET-projekt som stöds av verktygen. Webb-och WCF stöds. För andra projekttyper, till exempel skrivbord eller tjänsten program kan du fortfarande [manuellt lägga till en Application Insights SDK i projektet](app-insights-windows-desktop.md).
* Kontrollera att du har [Visual Studio 2013 Update 3 eller senare](http://go.microsoft.com/fwlink/?LinkId=397827). Det finns förinstallerat med utvecklare Analytics verktyg som ger Application Insights SDK.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrollera att **Analytics utvecklingsverktyg** är installerat och aktiverat. I så fall, klickar du på **uppdateringar** om det finns en uppdatering.
* Öppna dialogrutan Nytt projekt och välj ASP.NET-webbprogram. Om du ser det alternativet Application Insights är verktygen installerade. Om inte, försök att avinstallera och sedan installera Application Insights Tools.

## <a name="q02"></a>Det gick inte att lägga till Application Insights
*När jag försöker lägga till Application Insights till ett befintligt projekt, visas ett felmeddelande.*

Troliga orsaker:

* Det gick inte att kommunicera med Application Insights-portalen; eller
* Det finns problem med ditt Azure-konto;
* Du har bara [läsbehörighet till prenumerationen eller grupp där du försökte skapa den nya resursen](app-insights-resources-roles-access-control.md).

Åtgärda:

* Kontrollera att du har angett inloggningsuppgifter för höger Azure-konto. 
* I webbläsaren och kontrollera att du har åtkomst till den [Azure-portalen](https://portal.azure.com). Öppna inställningar och se om det finns några begränsningar.
* [Lägg till Application Insights i ditt befintliga projekt](app-insights-asp-net.md): I Solution Explorer högerklickar du på projektet och välj ”Lägg till Application Insights”.
* Om det fortfarande inte fungerar följer den [manuell proceduren](app-insights-windows-services.md) lägga till en resurs i portalen och sedan lägga till SDK i projektet. 

## <a name="emptykey"></a>Ett felmeddelande ”Instrumentation nyckeln kan inte vara tomt”
Något verkar ha gått fel när du installerar Application Insights eller kanske loggning nätverkskort.

Högerklicka på projektet i Solution Explorer och välj **Programinsikter > Konfigurera Application Insights**. Du får en dialogruta som uppmanar dig att logga in på Azure och skapa en Application Insights-resurs eller återanvända en befintlig.

## <a name="NuGetBuild"></a> ”NuGet-paket saknas” på min build-server
*Allt bygger OK när jag felsökningsläget datorn utveckling, men får ett NuGet-fel på build-servern.*

Se [NuGet-Paketåterställning](http://docs.nuget.org/Consume/Package-Restore) och [automatisk Paketåterställning](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Saknas menykommandot öppna Application Insights från Visual Studio
*När jag högerklickar på projektet Solution Explorer, visas inte några Application Insights-kommandon eller en öppen Application Insights-kommandot finns inte.*

Troliga orsaker:

* Om du har skapat Application Insights-resursen manuellt, eller om projektet är av en typ som inte stöds av Application Insights-verktyg.
* Verktyg för utvecklare Analytics är inaktiverade i din Visual Studio. 
* Din Visual Studio är äldre än 2013 Update 3.

Åtgärda:

* Kontrollera din version av Visual Studio 2013 update 3 eller senare.
* Välj **verktyg**, **tillägg och uppdateringar** och kontrollera att **Developer Analytics verktyg** är installerat och aktiverat. I så fall, klickar du på **uppdateringar** om det finns en uppdatering.
* Högerklicka på projektet i Solution Explorer. Om du ser kommandot **Programinsikter > Konfigurera Application Insights**, använda den för att ansluta ditt projekt till resursen i Application Insights-tjänsten.

I annat fall stöds din projekttypen direkt inte av Application Insights-verktyg. Om du vill se dina telemetri, logga in på den [Azure-portalen](https://portal.azure.com), Välj Application Insights i det vänstra navigeringsfältet och välj ditt program.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>”Åtkomst nekad” på Öppna Application Insights från Visual Studio
*Kommandot Öppna Programinsikter för att komma till Azure-portalen, men jag får felmeddelandet ”åtkomst nekad”.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Microsoft logga in som senast användes på din standardwebbläsare har inte åtkomst till [resursen som skapades när Application Insights har lagts till i den här appen](app-insights-asp-net.md). Det finns två troliga orsaker: 

* Du har mer än ett Microsoft-konto – kanske en arbete och personligt Microsoft-konto? Logga in som senast användes på din standardwebbläsare var för ett annat konto än det som har åtkomst till [Lägg till Application Insights i projektet](app-insights-asp-net.md). 
  
  * Korrigera: Klicka på ditt namn på de främsta höger i webbläsarfönstret och logga ut. Logga sedan in med det konto som har åtkomst till. Klicka på Application Insights på det vänstra navigeringsfältet och välj din app.
* Någon annan har lagt till Application Insights i projektet och glömt att ge dig [åtkomst till resursgruppen](app-insights-resources-roles-access-control.md) i som det skapades. 
  
  * Lösning: Om de används ett organisationskonto de kan lägga till dig team; eller de kan bevilja enskilda åtkomst till resursgruppen.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Tillgången inte att hitta' på Öppna Application Insights från Visual Studio
*Kommandot Öppna Programinsikter för att komma till Azure-portalen, men ett felmeddelande ”det gick inte att hitta tillgången'.*

Troliga orsaker:

* Application Insights-resurs för tillämpningsprogrammet har tagits bort; eller
* Nyckeln instrumentation angavs eller ändrades i ApplicationInsights.config genom att redigera den direkt, utan att uppdatera projektfilen. 

Instrumentation nyckeln i ApplicationInsights.config kontroller där telemetrin skickas. En rad i projektfilen styr vilken resurs som öppnas när du använder kommandot i Visual Studio. 

Åtgärda:

* Högerklicka på projektet i Solution Explorer och välj Application Insights, konfigurera Application Insights. I dialogrutan kan du antingen välja att skicka telemetri till en befintlig resurs eller skapa en ny. Eller:
* Öppna resursen direkt. Logga in på [Azure-portalen](https://portal.azure.com), klicka på Application Insights i det vänstra navigeringsfältet och välj sedan din app.

## <a name="where-do-i-find-my-telemetry"></a>Var hittar jag telemetri?
*Jag har loggat in på den [Microsoft Azure-portalen](https://portal.azure.com), och jag tittar på Azure startinstrumentpanel. Så var hittar jag mina Application Insights-data?*

* I det vänstra navigeringsfältet Klicka Application Insights appens namn. Om du inte har några projekt det, måste du [lägga till eller konfigurera Application Insights i webbprojektet](app-insights-asp-net.md).
  
    Det ser du vissa sammanfattning diagram. Du kan klicka igenom dem för att se mer information.
* Klicka på knappen Application Insights i Visual Studio när du felsöker din app.

## <a name="q03"></a> Ingen server-data (eller inga data alls)
*Jag har kört min app och sedan öppnas Application Insights-tjänsten i Microsoft Azure, men alla diagram visar ”Lär dig att samla in...” eller ”inte konfigurerad”.* Eller, *endast vyn sida och användardata, men inga serverdata.*

* Kör ditt program i felsökningsläge i Visual Studio (F5). Använd programmet för att generera vissa telemetri. Kontrollera att du kan se händelserna som loggas i utdatafönstret Visual Studio. 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* Öppna i Application Insights-portalen [diagnostiska Sök](app-insights-diagnostic-search.md). Data visas vanligtvis här först.
* Klicka på knappen Uppdatera. Bladet uppdaterar sig själv med jämna mellanrum, men du kan också göra det manuellt. Uppdateringsintervallet är längre för större tidsintervall.
* Kontrollera instrumentation nycklarna matcha. På huvudbladet för din app i Application Insights-portalen i den **Essentials** listrutan, titta på **Instrumentation nyckeln**. I ditt projekt i Visual Studio, öppna ApplicationInsights.config och hitta det `<instrumentationkey>`. Kontrollera att de båda nycklarna är lika. Om inte:
  
  * I portalen klickar du på Application Insights och leta efter den appresursen med rätt nyckel; eller
  * Högerklicka på projektet i Visual Studio Solution Explorer och välj Application Insights, konfigurera. Återställ appen för att skicka telemetri till rätt resurs.
  * Om du inte hittar matchande nycklar kan du kontrollera att du använder de samma inloggningsuppgifter i Visual Studio som i till portalen.
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* I den [Microsoft Azure startinstrumentpanel](https://portal.azure.com), titta på kartan tjänstens hälsa. Om det finns vissa uppgifter som avisering, vänta tills de har återgått till OK och Stäng och öppna bladet din Application Insights-programmet igen.
* Kontrollera också [vår status blogg](http://blogs.msdn.com/b/applicationinsights-status/).
* Har du skriva kod för den [serversidan SDK](app-insights-api-custom-events-metrics.md) som kan ändra nyckeln instrumentation i `TelemetryClient` instanser eller i `TelemetryContext`? Eller har du skriver en [filter eller provtagning](app-insights-api-filtering-sampling.md) som kanske filtreras för mycket?
* Om du redigerade ApplicationInsights.config noggrant kontrollera konfigurationen av [TelemetryInitializers och TelemetryProcessors](app-insights-api-filtering-sampling.md). En typ av fel namn eller parameter som kan orsaka SDK, för att skicka inga data.

## <a name="q04"></a>Inga data om sidvisningar, webbläsare, användning
*Data i serversvarstid och serverbegäranden, men inga data i tid för sidhämtning vyn, eller visas i webbläsaren eller användning blad.*

Data hämtas från skript i webbsidor. 

* Om du har lagt till ett befintligt webbprojekt för Application Insights [du måste lägga till skripten manuellt](app-insights-javascript.md).
* Se till att Internet Explorer inte visar webbplatsen i kompatibilitetsläge.
* Använd webbläsarens debug funktion (F12 på vissa webbläsare, väljer nätverket) för att verifiera att data skickas till `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Inga beroenden eller undantag data
Se [beroendetelemetri](app-insights-asp-net-dependencies.md) och [undantagstelemetri](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Inga prestandadata
Prestandadata (CPU, IO-frekvens och så vidare) är tillgängligt för [Java-webbtjänster](app-insights-java-collectd.md), [Windows-skrivbordsappar](app-insights-windows-desktop.md), [av IIS-program och tjänster om du installerar statusövervakaren](app-insights-monitor-performance-live-website-now.md), och [Azure-molntjänster](app-insights-azure.md). Du hittar den under inställningar för servrar.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Inga (server) data sedan jag publicerades appen Min server
* Kontrollera att du verkligen kopieras alla Microsoft. ApplicationInsights DLL-filer på servern, tillsammans med Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
* I brandväggen, du kan behöva [öppna vissa TCP-portarna](app-insights-ip-addresses.md).
* Om du måste använda en proxyserver för att skicka utanför företagets nätverk, ställa in [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) i Web.config
* Windows Server 2008: Kontrollera att du har installerat följande uppdateringar: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Jag för att se data, men den har stoppats
* Kontrollera den [status blogg](http://blogs.msdn.com/b/applicationinsights-status/).
* Har du uppnått den månatliga kvoten för datapunkter? Öppna inställningar/kvoten och priser för att ta reda på. I så fall, kan du uppgradera din plan eller betala för extra kapacitet. Finns det [priser schemat](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>Alla data som jag förväntade visas inte
Om ditt program skickar stora mängder data och du använder Application Insights SDK för ASP.NET version 2.0.0-beta3 eller senare, den [anpassningsbar provtagning](app-insights-sampling.md) funktion kan fungera och skicka endast en del av din telemetri. 

Du kan inaktivera det, men detta rekommenderas inte. Beräkningarna fungerar så att relaterade telemetri korrekt överförs, för att ställa diagnoser. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Felaktiga geografiska data i användaren telemetri
Ort, region och land dimensioner härleds från IP-adresser och alltid är inte korrekt.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Undantaget ”metoden hittades inte” vid körning i Azure Cloud Services
Utvecklade du för .NET 4.6? 4.6 stöds inte automatiskt i Azure Cloud Services-roller. [Installera 4.6 för varje roll](../cloud-services/cloud-services-dotnet-install-dotnet.md) innan du kör din app.

## <a name="still-not-working"></a>Fortfarande fungerar inte...
* [Application Insights-forum](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

