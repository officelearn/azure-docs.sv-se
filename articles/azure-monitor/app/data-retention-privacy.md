---
title: Datalagring och lagring i Azure Application Insights | Microsoft-dokument
description: Uttalande om lagring och sekretesspolicy
ms.topic: conceptual
ms.date: 09/29/2019
ms.openlocfilehash: 30878eecf795c85713b9f09b8325b326416022b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276002"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datainsamling, lagring och lagring i Application Insights

När du installerar [Azure Application Insights][start] SDK i din app skickar den telemetri om din app till molnet. Naturligtvis vill ansvariga utvecklare veta exakt vilka data som skickas, vad som händer med data och hur de kan behålla kontrollen över dem. I synnerhet kan känsliga uppgifter skickas, var lagras de och hur säkert är det? 

Först det korta svaret:

* Standardmodulerna för telemetri som körs "out of the box" är osannolikt att skicka känsliga data till tjänsten. Telemetrin gäller inläsnings-, prestanda- och användningsmått, undantagsrapporter och andra diagnostikdata. De viktigaste användardata som visas i diagnostikrapporterna är webbadresser. men din app bör inte i något fall placera känsliga data i oformaterad text i en webbadress.
* Du kan skriva kod som skickar ytterligare anpassad telemetri för att hjälpa dig med diagnostik och övervakningsanvändning. (Denna utökningsbarhet är ett bra inslag i Application Insights.) Det skulle av misstag vara möjligt att skriva denna kod så att den innehåller personliga och andra känsliga uppgifter. Om ditt program fungerar med sådana data bör du tillämpa en grundlig granskningsprocess för all kod du skriver.
* När du utvecklar och testar din app är det enkelt att granska vad som skickas av SDK. Data visas i debugging utdatafönstren i IDE och webbläsare. 
* Data lagras på [Microsoft Azure-servrar](https://azure.com) i USA eller Europa. (Men appen kan köras var som helst.) Azure har [starka säkerhetsprocesser och uppfyller ett brett spektrum av efterlevnadsstandarder](https://azure.microsoft.com/support/trust-center/). Endast du och ditt utsedda team har tillgång till dina data. Microsofts personal kan ha begränsad åtkomst till den endast under särskilda begränsade omständigheter med din vetskap. Det är krypterat under transport och i vila.
*   Granska insamlade data, eftersom detta kan inkludera data som är tillåtna under vissa omständigheter men inte andra.  Ett bra exempel på detta är Enhetsnamn. Enhetsnamnet från en server har ingen integritetspåverkan och är användbart, men ett enhetsnamn från en telefon eller bärbar dator kan ha en integritetseffekt och vara mindre användbart. En SDK som utvecklats främst för att rikta servrar, skulle samla enhetsnamn som standard, och detta kan behöva skrivas över i både normala händelser och undantag.

Resten av denna artikel utvecklar mer fullständigt på dessa svar. Den är utformad för att vara fristående, så att du kan visa den för kollegor som inte ingår i ditt närmaste team.

## <a name="what-is-application-insights"></a>Vad är Application Insights?
[Azure Application Insights][start] är en tjänst som tillhandahålls av Microsoft och som hjälper dig att förbättra prestanda och användbarhet för ditt liveprogram. Den övervakar ditt program hela tiden det körs, både under testning och efter att du har publicerat eller distribuerat det. Application Insights skapar diagram och tabeller som visar dig, till exempel vilka tider på dagen du får flest användare, hur lyhörd appen är och hur väl den betjänas av externa tjänster som den är beroende av. Om det finns krascher, fel eller prestandaproblem kan du söka igenom telemetridata i detalj för att diagnostisera orsaken. Och tjänsten kommer att skicka e-post om det finns några ändringar i tillgänglighet och prestanda för din app.

För att få den här funktionen installerar du ett Application Insights SDK i ditt program, som blir en del av dess kod. När appen körs övervakar SDK dess funktion och skickar telemetri till application insights-tjänsten. Det här är en molntjänst som tillhandahålls av [Microsoft Azure](https://azure.com). (Men Application Insights fungerar för alla program, inte bara program som finns i Azure.)

Tjänsten Application Insights lagrar och analyserar telemetrin. Om du vill se analysen eller sökningen via den lagrade telemetrin loggar du in på ditt Azure-konto och öppnar application insights-resursen för ditt program. Du kan också dela åtkomst till data med andra medlemmar i ditt team eller med angivna Azure-prenumeranter.

Du kan exportera data från application insights-tjänsten, till exempel till en databas eller till externa verktyg. Du ger varje verktyg en speciell nyckel som du får från tjänsten. Nyckeln kan återkallas om det behövs. 

Programinsikter SDK:er är tillgängliga för en rad olika programtyper: webbtjänster som finns i din egen Java EE- eller ASP.NET-servrar eller i Azure. webbklienter - det vill än koden som körs på en webbsida; Skrivbordsappar och -tjänster. enhetsappar som Windows Phone, iOS och Android. De skickar alla telemetri till samma tjänst.

## <a name="what-data-does-it-collect"></a>Vilka data samlar den in?
Det finns tre datakällor:

* SDK, som du integrerar med din app antingen [under utveckling](../../azure-monitor/app/asp-net.md) eller [vid körning](../../azure-monitor/app/monitor-performance-live-website-now.md). Det finns olika SDK:er för olika programtyper. Det finns också en [SDK för webbsidor](../../azure-monitor/app/javascript.md), som laddas i slutanvändarens webbläsare tillsammans med sidan.
  
  * Varje SDK har ett antal [moduler](../../azure-monitor/app/configuration-with-applicationinsights-config.md), som använder olika tekniker för att samla in olika typer av telemetri.
  * Om du installerar SDK under utveckling kan du använda dess API för att skicka din egen telemetri, utöver standardmodulerna. Den här anpassade telemetrin kan innehålla alla data som du vill skicka.
* I vissa webbservrar finns det också agenter som körs tillsammans med appen och skickar telemetri om CPU, minne och nätverksbeläggning. Azure-virtuella datorer, Docker-värdar och [Java EE-servrar](../../azure-monitor/app/java-agent.md) kan till exempel ha sådana agenter.
* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) är processer som körs av Microsoft och som skickar förfrågningar till din webbapp med jämna mellanrum. Resultaten skickas till application insights-tjänsten.

### <a name="what-kinds-of-data-are-collected"></a>Vilka typer av data samlas in?
De viktigaste kategorierna är:

* [Webbservertelemetri](../../azure-monitor/app/asp-net.md) - HTTP-begäranden.  Uri, det tar tid att behandla begäran, svarskod, klient-IP-adress. `Session id`.
* [Webbsidor](../../azure-monitor/app/javascript.md) - Antal sidor, användare och sessions. Sidans laddningstider. Undantag. Ajax synar.
* Prestandaräknare - Minne, CPU, IO, Nätverksbeläggning.
* Klient- och serverkontext - OS, språk, enhetstyp, webbläsare, skärmupplösning.
* [Undantag](../../azure-monitor/app/asp-net-exceptions.md) och krascher - `build id` **stack dumpar**, , CPU-typ. 
* [Beroenden](../../azure-monitor/app/asp-net-dependencies.md) - anrop till externa tjänster som REST, SQL, AJAX. URI eller anslutningssträng, varaktighet, framgång, kommando.
* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) - testtid och steg, svar.
* [Spåra loggar](../../azure-monitor/app/asp-net-trace-logs.md) och [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md) - **allt du kodar i dina loggar eller telemetri**.

[Mer detaljerat](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hur kan jag verifiera vad som samlas in?
Om du utvecklar appen med Visual Studio kör du appen i felsökningsläge (F5). Telemetrin visas i utdatafönstret. Därifrån kan du kopiera den och formatera den som JSON för enkel inspektion. 

![](./media/data-retention-privacy/06-vs.png)

Det finns också en mer läsbar vy i diagnostikfönstret.

Öppna webbläsarens felsökningsfönster för webbsidor.

![Tryck på F12 och öppna fliken Nätverk.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan jag skriva kod för att filtrera telemetrin innan den skickas?
Detta skulle vara möjligt genom att skriva en [telemetri processor plugin](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Hur länge sparas uppgifterna?
Rådatapunkter (det vill säga objekt som du kan fråga i Analytics och inspektera i Sök) sparas i upp till 730 dagar. Du kan [välja en kvarhållningslängd på](https://docs.microsoft.com/azure/azure-monitor/app/pricing#change-the-data-retention-period) 30, 60, 90, 120, 180, 270, 365, 550 eller 730 dagar. Om du behöver behålla data längre än 730 dagar kan du använda [Kontinuerlig export](../../azure-monitor/app/export-telemetry.md) för att kopiera dem till ett lagringskonto under datainmatning. 

Data som lagras längre än 90 dagar medför tilläggsavgifter. Läs mer om priser för Application Insights på [prissidan för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Aggregerade data (det vill säga antal, medelvärden och andra statistiska data som visas i Metric Explorer) lagras på en korn av 1 minut i 90 dagar.

[Felsökningsögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md) lagras i 15 dagar. Den här bevarandeprincipen anges per program. Om du behöver öka det här värdet kan du begära en ökning genom att öppna ett supportärende i Azure-portalen.

## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
Data är synliga för dig och, om du har ett organisationskonto, dina gruppmedlemmar. 

Det kan exporteras av dig och dina gruppmedlemmar och kan kopieras till andra platser och vidarebefordras till andra personer.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Vad gör Microsoft med den information som min app skickar till Application Insights?
Microsoft använder endast data för att tillhandahålla tjänsten till dig.

## <a name="where-is-the-data-held"></a>Var finns uppgifterna?
* Du kan välja plats när du skapar en ny Application Insights-resurs. Läs mer om tillgänglighet för Application Insights per region [här](https://azure.microsoft.com/global-infrastructure/services/?products=all).

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Betyder det att min app måste vara värd i USA, Europa eller Sydostasien?
* Nej. Ditt program kan köras var som helst, antingen i dina egna lokala värdar eller i molnet.

## <a name="how-secure-is-my-data"></a>Hur säkra är mina data?
Application Insights är en Azure-tjänst. Säkerhetsprinciper beskrivs i [faktabladet för Azure Security, Sekretess och Efterlevnad](https://go.microsoft.com/fwlink/?linkid=392408).

Data lagras i Microsoft Azure-servrar. För konton i Azure-portalen beskrivs kontobegränsningar i [Azure Security, Privacy och Compliance-dokumentet](https://go.microsoft.com/fwlink/?linkid=392408).

Åtkomsten till dina data av Microsofts personal är begränsad. Vi får endast tillgång till dina uppgifter med ditt tillstånd och om det är nödvändigt för att stödja din användning av Application Insights. 

Data som aggregerats i alla våra kunders program (t.ex. datahastigheter och genomsnittlig storlek på spårningar) används för att förbättra Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Kan någon annans telemetri störa mina Application Insights-data?
De kan skicka ytterligare telemetri till ditt konto med hjälp av instrumenteringsnyckeln, som finns i koden för dina webbsidor. Med tillräckligt med ytterligare data skulle dina mått inte korrekt representera appens prestanda och användning.

Om du delar kod med andra projekt bör du komma ihåg att ta bort instrumenteringsnyckeln.

## <a name="is-the-data-encrypted"></a>Är data krypterade?
Alla data krypteras i vila och när de flyttas mellan datacenter.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Är data krypterade under överföring från mitt program till Application Insights-servrar?
Ja, vi använder https för att skicka data till portalen från nästan alla SDK:er, inklusive webbservrar, enheter och HTTPS-webbsidor. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Skapar SDK tillfällig lokal lagring?

Ja, vissa telemetrikanaler sparas data lokalt om en slutpunkt inte kan nås. Läs nedan för att se vilka ramverk och telemetrikanaler som påverkas.

Telemetrikanaler som använder lokal lagring skapar temporära filer i TEMP- eller APPDATA-katalogerna, som är begränsade till det specifika konto som kör ditt program. Detta kan inträffa när en slutpunkt var tillfälligt otillgänglig eller om du når begränsningsgränsen. När problemet har lösts återupptas telemetrikanalen att skicka alla nya och beständiga data.

Dessa beständiga data krypteras inte lokalt. Om detta är ett problem, granska data och begränsa insamlingen av privata data. (Mer information finns i [Så här exporterar och tar du bort privata data](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data).)

Om en kund behöver konfigurera den här katalogen med specifika säkerhetskrav kan den konfigureras per ramverk. Kontrollera att processen som kör programmet har skrivbehörighet till den här katalogen, men se också till att den här katalogen är skyddad för att undvika att telemetri läses av oavsiktliga användare.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp`används för beständiga data. Den här platsen kan inte konfigureras från konfigurationskatalogen och behörigheterna för att komma åt den här mappen är begränsade till den specifika användaren med nödvändiga autentiseringsuppgifter. (Mer information finns i [implementeringen](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.Net

Som `ServerTelemetryChannel` standard använder den aktuella användarens lokala appdatamapp `%localAppData%\Microsoft\ApplicationInsights` eller tempmapp `%TMP%`. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.)


Via konfigurationsfil:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via kod:

- Ta bort ServerTelemetryChannel från konfigurationsfilen
- Lägg till det här kodavsnittet i konfigurationen:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore (på ett sätt)

Som `ServerTelemetryChannel` standard använder den aktuella användarens lokala appdatamapp `%localAppData%\Microsoft\ApplicationInsights` eller tempmapp `%TMP%`. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.) I en Linux-miljö inaktiveras lokal lagring om inte en lagringsmapp anges.

Följande kodavsnitt visar hur du `ServerTelemetryChannel.StorageFolder` ställer `ConfigureServices()` in `Startup.cs` i klassens metod:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Mer information finns i [AspNetCore Custom Configuration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

Som `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` standard används för beständiga data. Behörigheter för åtkomst till den här mappen är begränsade till den aktuella användaren och administratörer. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) här.)

Mappprefixet `appInsights-node` kan åsidosättas genom att ändra körningsvärdet för den statiska variabeln `Sender.TEMPDIR_PREFIX` som finns i [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (webbläsare)

[HTML5 Session Storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) används för att bevara data. Två separata buffertar `AI_buffer` används: och `AI_sent_buffer`. Telemetri som är batchat och väntar på `AI_buffer`att skickas lagras i . Telemetri som just skickats `AI_sent_buffer` placeras i tills inmatningsservern svarar att den har tagits emot. När telemetri har tagits emot tas den bort från alla buffertar. Vid tillfälliga fel (till exempel förlorar en användare nätverksanslutning), telemetri finns kvar tills `AI_buffer` den tas emot eller inmatningsservern svarar att telemetrin är ogiltig (dåligt schema eller för gammalt, till exempel).

Telemetribuffertar kan inaktiveras [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) `false`genom att ställa in på . När sessionslagring är inaktiverat används i stället en lokal matris som beständig lagring. Eftersom JavaScript SDK körs på en klientenhet har användaren åtkomst till den här lagringsplatsen via webbläsarens utvecklarverktyg.

### <a name="opencensus-python"></a>OpenCensus Python

Som standard använder OpenCensus Python SDK den aktuella användarmappen `%username%/.opencensus/.azure/`. Behörigheter för åtkomst till den här mappen är begränsade till den aktuella användaren och administratörer. (Se [implementering](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) här.) Mappen med dina beständiga data kommer att namnges efter Python-filen som genererade telemetrin.

Du kan ändra lagringsfilens plats `storage_path` genom att skicka in parametern i konstruktorn för den exportör du använder.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hur skickar jag data till Application Insights med TLS 1.2?

För att försäkra säkerheten för data under överföring till application insights-slutpunkterna uppmuntrar vi starkt kunderna att konfigurera sitt program så att de använder minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och medan de fortfarande arbetar för att tillåta bakåtkompatibilitet rekommenderas de **inte**och branschen flyttar snabbt för att överge stödet för dessa äldre protokoll. 

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) har satt [en tidsfrist den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) för att inaktivera äldre versioner av TLS/SSL och uppgradera till säkrare protokoll. När Azure släpper äldre support, om ditt program/klienter inte kan kommunicera över minst TLS 1.2 kan du inte skicka data till Application Insights. Hur du ska testa och validera programmets TLS-stöd varierar beroende på operativsystem/plattform samt vilket språk/ramverk ditt program använder.

Vi rekommenderar inte att du uttryckligen ställer in ditt program så att det bara använder TLS 1.2 om det inte är nödvändigt eftersom detta kan bryta säkerhetsfunktionerna på plattformsnivå som gör att du automatiskt kan upptäcka och dra nytta av nyare säkrare protokoll när de blir tillgängliga, till exempel TLS 1.3. Vi rekommenderar att du utför en grundlig granskning av programmets kod för att kontrollera om det finns hårdkodning av specifika TLS/SSL-versioner.

### <a name="platformlanguage-specific-guidance"></a>Plattforms-/språkspecifik vägledning

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
| Azure App Services  | Konfiguration kan krävas. | Stödet tillkännagavs i april 2018. Läs meddelandet för [konfigurationsinformation](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Azure-funktionsappar | Konfiguration kan krävas. | Stödet tillkännagavs i april 2018. Läs meddelandet för [konfigurationsinformation](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Konfigurationen varierar beroende på version som stöds. | Detaljerad konfigurationsinformation för .NET 4.7 och tidigare versioner finns i [följande instruktioner](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Statusövervakare | Konfiguration som stöds krävs | Statusövervakaren är beroende av + [OS-konfiguration .NET-konfiguration](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) för att stödja TLS 1.2. [OS Configuration](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)
|Node.js |  Konfiguration som stöds i v10.5.0 kan det krävas konfiguration. | Använd den [officiella Node.js TLS/SSL-dokumentationen](https://nodejs.org/api/tls.html) för alla programspecifika konfigurationer. |
|Java | Stöds, JDK stöd för TLS 1.2 lades till i [JDK 6 uppdatering 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) och [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 använder [TLS 1.2 som standard](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-distributioner tenderar att förlita sig på [OpenSSL](https://www.openssl.org) för TLS 1.2-stöd.  | Kontrollera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows 8.0 - 10 | Stöds och aktiveras som standard. | Så här bekräftar du att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 – 2016 | Stöds och aktiveras som standard. | Så här bekräftar du att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men inte aktiverat som standard. | Mer information om hur du aktiverar finns på [registerinställningssidan för Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2008 SP2 | Stöd för TLS 1.2 kräver en uppdatering. | Se [Uppdatera om du vill lägga till stöd för TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) i Windows Server 2008 SP2. |
|Windows Vista | Stöds inte. | Ej tillämpligt

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Kontrollera vilken version av OpenSSL din Linux-distribution körs

Om du vill kontrollera vilken version av OpenSSL du har installerat öppnar du terminalen och kör:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Kör en test-TLS 1.2-transaktion på Linux

Om du vill köra ett preliminärt test för att se om ditt Linux-system kan kommunicera via TLS 1.2 öppnar du terminalen och kör:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Personuppgifter som lagras i Application Insights

Vår artikel om personuppgifter om [programinsikter](../../azure-monitor/platform/personal-data-mgmt.md) beskriver det här problemet på djupet.

#### <a name="can-my-users-turn-off-application-insights"></a>Kan mina användare stänga av Application Insights?
Inte direkt. Vi tillhandahåller inte en strömbrytare som användarna kan använda för att stänga av Application Insights.

Du kan dock implementera en sådan funktion i ditt program. Alla SDK:er innehåller en API-inställning som inaktiverar telemetrisamlingen. 

## <a name="data-sent-by-application-insights"></a>Data som skickas av Application Insights
SDK:erna varierar mellan plattformarna och det finns flera komponenter som du kan installera. (Se [Application Insights - översikt][start].) Varje komponent skickar olika data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasser av data som skickas i olika scenarier

| Din handling | Dataklasser som samlats in (se nästa tabell) |
| --- | --- |
| [Lägga till Programstatistik SDK i ett .NET-webbprojekt][greenbrown] |ServerContext<br/>Inneburit<br/>Prestandaräknare<br/>Begäranden<br/>**Undantag**<br/>Session<br/>användare |
| [Installera statusövervakare på IIS][redfield] |Beroenden<br/>ServerContext<br/>Inneburit<br/>Prestandaräknare |
| [Lägga till Programstatistik SDK i en Java-webbapp][java] |ServerContext<br/>Inneburit<br/>Förfrågan<br/>Session<br/>användare |
| [Lägga till JavaScript SDK på webbsidan][client] |ClientContext (KlientKontext) <br/>Inneburit<br/>Sida<br/>ClientPerf (klientperf)<br/>Ajax |
| [Definiera standardegenskaper][apiproperties] |**Egenskaper** för alla standard- och anpassade händelser |
| [Ring TrackMetric][api] |Numeriska värden<br/>**Egenskaper** |
| [Samtalsspår*][api] |Händelsenamn<br/>**Egenskaper** |
| [Ring TrackException][api] |**Undantag**<br/>Stack dumpa<br/>**Egenskaper** |
| SDK kan inte samla in data. Ett exempel: <br/> - kan inte komma åt perf räknare<br/> - undantag i telemetriinitierare |SDK-diagnostik |

För [SDK:er för andra plattformar][platforms]finns i deras dokument.

#### <a name="the-classes-of-collected-data"></a>Klasserna av insamlade data

| Insamlad dataklass | Inkluderar (inte en uttömmande lista) |
| --- | --- |
| **Egenskaper** |**Alla data - bestäms av din kod** |
| DeviceContext |`Id`, IP, Språk, Enhetsmodell, nätverk, nätverkstyp, OEM-namn, skärmupplösning, Rollinstans, Rollnamn, Enhetstyp |
| ClientContext (KlientKontext) |OS, språk, språk, nätverk, fönsterupplösning |
| Session |`session id` |
| ServerContext |Maskinnamn, språk, OPERATIVSYSTEM, enhet, användarsession, användarkontext, åtgärd |
| Inneburit |geografisk plats från IP-adress, tidsstämpel, OS, webbläsare |
| Mått |Måttnamn och värde |
| Händelser |Händelsenamn och värde |
| Sidvisningar |URL- och sidnamn eller skärmnamn |
| Klient perf |URL/sidnamn, inläsningstid för webbläsaren |
| Ajax |HTTP-anrop från webbsida till server |
| Begäranden |URL, varaktighet, svarskod |
| Beroenden |Type(SQL, HTTP, ...), anslutningssträng eller URI, synkronisering/async, varaktighet, framgång, SQL-uttryck (med Statusövervakare) |
| **Undantag** |Typ, **meddelande,** samtalsstaplar, källfil, radnummer,`thread id` |
| Kraschar |`Process id`, `parent process id`, `crash thread id`; programkorrigering, `id`, bygga;  undantagstyp, adress, orsak; fördunklade symboler och register, binära start- och slutadresser, binärt namn och sökväg, cpu-typ |
| Spårning |**Meddelande-** och allvarlighetsgrad |
| Prestandaräknare |Processortid, tillgängligt minne, begäranhastighet, undantagshastighet, process privata byte, IO-hastighet, varaktighet för begäran, kölängd för begäran |
| Tillgänglighet |Webbtestsvarskod, varaktighet för varje teststeg, testnamn, tidsstämpel, framgång, svarstid, testplats |
| SDK-diagnostik |Spåra meddelande eller undantag |

Du kan [stänga av en del av data genom att redigera ApplicationInsights.config][config]

> [!NOTE]
> Klient-IP används för att härleda geografisk plats, men som standard lagras IP-data inte längre och alla nollor skrivs till det associerade fältet. För att förstå mer om hantering av personuppgifter rekommenderar vi den här [artikeln](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Om du behöver lagra IP-adressdata kommer vår artikel om insamling av [IP-adresser](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection) att gå igenom dina alternativ.

## <a name="credits"></a>Krediter
Denna produkt innehåller GeoLite2 data som skapats av MaxMind, tillgänglig från [https://www.maxmind.com](https://www.maxmind.com).



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
