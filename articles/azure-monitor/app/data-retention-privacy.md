---
title: Kvarhållning av data och lagring i Azure Application Insights | Microsoft Docs
description: Kvarhållning och sekretess Principframställning
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mbullwin
ms.openlocfilehash: 3c74d3a6c5b66053fb968ad52f72eca181799a3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003591"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datainsamling, kvarhållning och lagring i Application Insights

När du installerar [Azure Application Insights] [ start] SDK i din app skickar telemetri om din app till molnet. Naturligtvis finns vill ansvarig utvecklare veta exakt vilka data skickas, vad händer med data och hur de kan behålla kontrollen över den. I synnerhet kunde känsliga data skickas, där är lagrade och hur säker är det? 

Första kort svaret:

* Standardmoduler moduler som kör ”out of box” är inte troligt att skicka känslig data till tjänsten. Telemetri är bekymrad över belastning, prestanda och användning mått, undantagsrapporter och andra diagnostikdata. Huvudanvändaren data visas i diagnostiska rapporter är webbadresserna. men din app i båda fallen bör inte Placera känsliga data i oformaterad text i en URL.
* Du kan skriva kod som skickar ytterligare anpassad telemetri som hjälper dig med diagnostik- och användningsdata för övervakning. (Den här utökningsbarhet är en bra funktion i Application Insights.) Det skulle vara möjligt, av misstag skriva den här koden så att den inkluderar privata och andra känsliga data. Om ditt program fungerar med sådana data, installerar du en omfattande granskning processer för all kod som du skriver.
* När du utvecklar och testar din app kan är det enkelt att granska vad som skickas av SDK. Data visas på windows-felsökning utdata av IDE och webbläsare. 
* Data lagras i [Microsoft Azure](https://azure.com) servrar i USA eller Europa. (Men appen kan köras överallt.) Azure har [stark säkerhet bearbetar och uppfyller ett brett utbud av efterlevnadsstandarder](https://azure.microsoft.com/support/trust-center/). Endast du och din avsedda grupp har åtkomst till dina data. Microsoft-Personal kan ha begränsad åtkomst till den bara under vissa begränsade omständigheter med dina kunskaper. Den är krypterad under överföringen, även om inte på servrarna.

Resten av den här artikeln utvecklar mer i detalj på dessa frågor. Det har utformats för att vara fristående, så att du kan visa den till kollegor som inte ingår i din arbetsgrupp.

## <a name="what-is-application-insights"></a>Vad är Application Insights?
[Azure Application Insights] [ start] är en tjänst från Microsoft som hjälper dig att förbättra prestanda och användbarhet av ditt liveprogram. Det övervakar programmets hela tiden som den körs, både under testning och när du har publicerat eller distribuerat den. Application Insights skapar diagram och tabeller som visar dig, till exempel, vilka tidpunkter på dagen du får de flesta användare, hur responsiv appen är och hur väl den hanteras av alla externa tjänster som den är beroende av. Om det finns krascher, fel eller prestandaproblem kan söka du igenom dessa data i detalj för att diagnostisera orsaken. Och tjänsten skickar du e-postmeddelanden om det finns ändringar i tillgänglighet och prestanda för din app.

För att få den här funktionen måste installera du en Application Insights SDK i programmet, som blir en del av koden. Om din app är igång kan SDK övervakar dess drift och skickar telemetri till Application Insights-tjänsten. Detta är en molnbaserad tjänst som värd för [Microsoft Azure](https://azure.com). (Men Application Insights fungerar för alla program, inte bara de som finns i Azure.)

Application Insights-tjänsten lagrar och analyserar telemetri. Om du vill se analys eller Sök igenom den lagrade telemetrin, logga in på ditt Azure-konto och öppna Application Insights-resurs för ditt program. Du kan också dela åtkomst till data med andra medlemmar i ditt team eller med angivna Azure-prenumeranter.

Du kan ha data som exporterats från Application Insights-tjänsten, till exempel att en databas eller externa verktyg. Du kan ange varje verktyg med en särskild nyckel som hämtas från tjänsten. Nyckeln kan återkallas om det behövs. 

Application Insights SDK: er är tillgängliga för flera typer av program: webbtjänster som finns i dina egna Java EE eller ASP.NET-servrar eller i Azure webbklienter – det vill säga den kod som körs på en webbsida; skrivbordsappar och tjänster. appar för enheter, till exempel Windows Phone, iOS och Android. Alla skicka telemetri till samma tjänst.

## <a name="what-data-does-it-collect"></a>Vilka data samlar det?
### <a name="how-is-the-data-is-collected"></a>Vad är data som samlas in?
Det finns tre datakällor:

* SDK, som du integrerar med din app antingen [i utveckling](../../azure-monitor/app/asp-net.md) eller [vid körning](../../azure-monitor/app/monitor-performance-live-website-now.md). Det finns olika SDK: er för olika programtyperna. Det finns också en [SDK för webbsidor](../../azure-monitor/app/javascript.md), som läser in i webbläsaren,-slutanvändarens och sidan.
  
  * Varje SDK har ett antal [moduler](../../azure-monitor/app/configuration-with-applicationinsights-config.md), som använda olika metoder för att samla in olika typer av telemetri.
  * Du kan använda dess API för att skicka din egen telemetri, förutom modulerna som standard om du installerar SDK under utveckling. Den här anpassad telemetri kan innehålla information du vill skicka.
* I vissa webbservrar finns också agenter som körs tillsammans med appen och skicka telemetri om processor, minne och användandet av nätverket. Till exempel virtuella Azure-datorer, Docker-värdar och [Java EE-servrar](../../azure-monitor/app/java-agent.md) kan ha dessa agenter.
* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) är processer som körs av Microsoft som skickar begäranden till webbappen med jämna mellanrum. Resultatet skickas till Application Insights-tjänsten.

### <a name="what-kinds-of-data-are-collected"></a>Vilka typer av data som samlas in?
De viktigaste kategorierna är:

* [Webb-telemetri](../../azure-monitor/app/asp-net.md) -HTTP-begäranden.  URI: N, åtgången tid för att bearbeta begäran, svarskod, klientens IP-adress. Sessions-id.
* [Webbsidor](../../azure-monitor/app/javascript.md) -antal sidan, användare och sessioner. Sidinläsningstider. Undantag. AJAX-anrop.
* Prestandaräknare - minne, CPU, IO, användandet av nätverket.
* Klient- och kontext - operativsystem, språk, typ av enhet, webbläsare, skärmupplösning.
* [Undantag](../../azure-monitor/app/asp-net-exceptions.md) och krascher - **stack Dumpar**, skapa id, processortyp. 
* [Beroenden](../../azure-monitor/app/asp-net-dependencies.md) -anrop till externa tjänster, till exempel REST, SQL, AJAX. URI: N eller anslutningssträng, varaktighet, lyckades, kommandot.
* [Tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md) -varaktigheten för testning och steg, svar.
* [Spårningsloggar](../../azure-monitor/app/asp-net-trace-logs.md) och [anpassad telemetri](../../azure-monitor/app/api-custom-events-metrics.md) - **något du koda i dina loggar och telemetri**.

[Detalj](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hur kan jag kontrollera vad som samlas in?
Om du utvecklar program med Visual Studio kan du köra appen i felsökningsläge (F5). Telemetri som visas i utdatafönstret. Därifrån kan du kopiera den och formatera den som JSON för enkelt granskning. 

![](./media/data-retention-privacy/06-vs.png)

Det finns också en enklare vy i fönstret diagnostik.

Öppna din webbläsare felsökning fönster för webbsidor.

![Tryck på F12 och öppna fliken nätverk.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan jag skriva kod för att filtrera telemetri innan den skickas?
Detta är möjligt genom att skriva en [processor-plugin-programmet för telemetri](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Hur länge sparas data?
Rådatapunkterna (det vill säga objekt som du kan fråga i Analytics och inspektera i Search) kvarhålls i upp till 90 dagar. Om du vill behålla data längre än den som du kan använda [löpande export](../../azure-monitor/app/export-telemetry.md) att kopiera den till ett lagringskonto.

Sammanställda data (dvs, antal, genomsnitt och andra statistiska data som du ser i Metric Explorer) finns kvar på en grain på 1 minut under 90 dagar.

[Felsök ögonblicksbilder](../../azure-monitor/app/snapshot-debugger.md) lagras i sju dagar. Den här bevarandeprincipen är inställd på basis av per program. Om du vill öka det här värdet kan du begära en ökning genom att öppna ett supportärende i Azure-portalen.

## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
Data är synliga för dig och, om du har ett organisationskonto, dina gruppmedlemmar. 

Den kan exporteras som du och dina gruppmedlemmar och kan kopieras till andra platser och överföras till andra personer.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Vad gör Microsoft med den information som min app skickar till Application Insights?
Microsoft använder informationen endast för att tillhandahålla tjänsten till dig.

## <a name="where-is-the-data-held"></a>Där lagras data?
* I USA, Europa eller Asien, sydöstra. Du kan välja platsen när du skapar en ny Application Insights-resurs. 

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Betyder det att min app har ska finnas i USA, Europa eller Asien, sydöstra?
* Nej. Ditt program kan köras var som helst, i din egen lokala värdar eller i molnet.

## <a name="how-secure-is-my-data"></a>Hur säker är Mina data?
Application Insights är en Azure-tjänst. Säkerhetsprinciper beskrivs i den [Azure-säkerhet, sekretess och efterlevnad white paper om](https://go.microsoft.com/fwlink/?linkid=392408).

Data lagras i Microsoft Azure-servrar. För konton i Azure Portal, kontobegränsningar beskrivs i den [Azure-säkerhet, sekretess och efterlevnad dokumentet](https://go.microsoft.com/fwlink/?linkid=392408).

Åtkomst till dina data genom Microsofts personal är begränsad. Vi har åtkomst till dina data endast med ditt tillstånd och om det är nödvändigt att stödja din användning av Application Insights. 

Data i aggregering i våra kunders program (till exempel datataxa och genomsnittlig storlek på spårningar) används för att förbättra Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Det gick någon annans telemetri som stör Mina Application Insights-data?
De kan skicka ytterligare telemetri till ditt konto med hjälp av instrumentation nyckeln, som finns i koden för dina webbsidor. Med tillräckligt med ytterligare information, skulle dina mått inte korrekt återger appens prestanda och användning.

Om du delar kod med andra projekt kan du komma ihåg att ta bort din instrumentationsnyckel.

## <a name="is-the-data-encrypted"></a>Krypteras data?
Alla data krypteras i viloläge och som den flyttar mellan data datacenter.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Krypteras data under överföring från mitt program till Application Insights-servrar?
Ja, vi använder https för att skicka data till portalen från nästan alla SDK: er, inklusive webbservrar, enheter och HTTPS-webbsidor. Det enda undantaget är data som skickas från vanlig HTTP-webbsidor.

## <a name="does-the-sdk-create-temporary-local-storage"></a>Skapar SDK tillfälligt lokal lagring?

Ja, en viss telemetri kanaler behålls data lokalt om en slutpunkt inte kan nås. Granska nedan för att se vilka ramverk och telemetri kanaler som påverkas.


Telemetri kanaler som använder lokal lagring skapa tillfälliga filer i kataloger TEMP eller APPDATA som är begränsade till det specifika konto som kör ditt program. Detta kan inträffa när en slutpunkt har tillfälligt otillgänglig eller du når gränsen. När det här problemet har lösts återupptas telemetri kanalen skickar alla nya och beständiga data.


Den här beständiga data är **inte krypterade** och vi rekommenderar starkt att omstrukturera din princip för datainsamling för att inaktivera insamling av personliga data. (Se [exportera och ta bort privata data](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) för mer information.)


Om en kund behöver du konfigurerar den här katalogen med specifika säkerhetskrav kan den konfigureras per framework. Kontrollera att processen som kör ditt program har skrivbehörighet till den här katalogen, men också kontrollera att den här katalogen skyddas för att undvika telemetriavlästa av oönskade användare.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` används för att bevara data. Den här platsen är inte konfigureras från konfigurationskatalogen och behörighet att komma åt den här mappen är begränsade till specifika användare med autentiseringsuppgifter som krävs. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) här.)

###  <a name="net"></a>.Net

Som standard `ServerTelemetryChannel` använder den aktuella användarens lokala app datamapp `%localAppData%\Microsoft\ApplicationInsights` eller tillfällig mapp för `%TMP%`. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.)


Via konfigurationsfil:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Via kod:

- Ta bort ServerTelemetryChannel från konfigurationsfilen
- Lägg till det här kodfragmentet i konfigurationen:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Som standard `ServerTelemetryChannel` använder den aktuella användarens lokala app datamapp `%localAppData%\Microsoft\ApplicationInsights` eller tillfällig mapp för `%TMP%`. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) här.) I en Linux-miljö inaktiveras lokal lagring om inte en mapp anges.

Följande kodfragment visar hur du ställer in `ServerTelemetryChannel.StorageFolder` i den `ConfigureServices()`  -metoden för din `Startup.cs` klass:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Se [AspNetCore anpassad konfiguration](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) för mer information. )

### <a name="nodejs"></a>Node.js

Som standard `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` används för att bevara data. Behörighet att komma åt den här mappen är begränsade till aktuell användare och administratörer. (Se [implementering](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) här.)

Prefixet mappen `appInsights-node` kan åsidosättas genom att ändra värdet för den statiska variabeln runtime `Sender.TEMPDIR_PREFIX` hittades i [Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Hur skickar jag data till Application Insights med hjälp av TLS 1.2?

Om du vill se till att skydda data under överföring till Application Insights-slutpunkter, rekommenderar vi starkt att kunder kan konfigurera sina program för att använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**, och branschen snabbt flytta att lämna support äldre protokoll. 

Den [PCI Security Standards Council](https://www.pcisecuritystandards.org/) har ställt in en [deadline på den 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) att inaktivera äldre versioner av TLS/SSL och uppgraderingen säkrare protokoll. När Azure sjunker bakåtkompatibelt stöd om dina program/klienter inte kan kommunicera över minst TLS 1.2 inte skulle du kunna skicka data till Application Insights. Den metod som du använder för att testa och validera ditt programs TLS-stödet varierar beroende på operativsystem/plattform samt/språkramverket används i ditt program.

Vi rekommenderar inte uttryckligen ställa in ditt program att bara använda TLS 1.2, såvida inte är absolut nödvändigt, eftersom det kan innebära att säkerhet på funktioner som gör att du kan identifiera och dra nytta av nya säkrare protokoll när de blir automatiskt tillgängliga, till exempel TLS 1.3. Vi rekommenderar att du utför en omfattande granskning av din programkod för att söka efter hardcoding av specifika TLS/SSL-versioner.

### <a name="platformlanguage-specific-guidance"></a>Specifika anvisningar för plattform/språk

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
| Azure App Services  | Stöd för behövas konfiguration. | Support lanserades i April 2018. Läs meddelandet för [konfigurationsinformation](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/).  |
| Azure Function Apps | Stöd för behövas konfiguration. | Support lanserades i April 2018. Läs meddelandet för [konfigurationsinformation](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/). |
|.NET | Stöd för varierar konfigurationen beroende på version. | Detaljerad konfigurationsinformation för .NET 4.7 och tidigare versioner finns i [instruktionerna](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Statusövervakare | Konfigurationer som stöds, krävs | Statusövervakaren förlitar sig på [Operativsystemets konfiguration](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET Configuration](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12) till support TLS 1.2.
|Node.js |  Stöds i v10.5.0, behövas konfiguration. | Använd den [officiell Node.js TLS/SSL-dokumentation](https://nodejs.org/api/tls.html) för eventuella specifika programkonfiguration. |
|Java | Stöd för JDK-stöd för TLS 1.2 har lagts till i [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) och [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 använder [TLS 1.2 som standard](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Linux-distributioner tenderar att förlita dig på [OpenSSL](https://www.openssl.org) för stöd för TSL 1.2.  | Kontrollera den [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) att bekräfta din version av OpenSSL stöds.|
| Windows 8.0-10 | Stöds och aktiverat som standard. | Bekräfta att du fortfarande använder den [standardinställningar](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Stöds och aktiverat som standard. | Bekräfta att du fortfarande använder den [standardinställningar](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 och Windows Server 2008 R2 SP1 | Stöds, men inte är aktiverad som standard. | Se den [registerinställningar för Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) för information om hur du aktiverar.  |
| Windows Server 2008 SP2 | Stöd för TLS 1.2 kräver en uppdatering. | Se [Update för att lägga till stöd för TLS 1.2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) i Windows Server 2008 SP2. |
|Windows Vista | Stöds inte. | Gäller inte

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Kontrollera vilken version av OpenSSL körs din Linux-distribution

Om du vill kontrollera vilken version av OpenSSL som du har installerat, öppna terminalen och kör:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Köra ett test TLS 1.2 transaktion på Linux

Att köra en grundläggande preliminär test för att se om din Linux-system kan kommunicera över TLS 1.2. Öppna terminalen och kör:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Personliga data som lagras i Application Insights

Vår [Application Insights personuppgifter artikeln](../../azure-monitor/platform/personal-data-mgmt.md) beskriver problemet djupgående.

#### <a name="can-my-users-turn-off-application-insights"></a>Mina användare kan stänga av Application Insights?
Inte direkt. Vi tillhandahåller inte en växel som användarna kan användas för att stänga av Application Insights.

Dock kan du implementera en sådan funktion i ditt program. Alla SDK: erna innehåller en API-inställning som inaktiverar telemetriinsamling. 

## <a name="data-sent-by-application-insights"></a>Data som skickas av Application Insights
SDK: erna kan variera mellan plattformar och det finns flera komponenter som du kan installera. (Se [Application Insights – översikt][start].) Varje komponent skickar olika data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Typer av data som skickas i olika scenarier

| Åtgärden | Dataklasser som samlas in (se nästa tabell) |
| --- | --- |
| [Lägg till Application Insights SDK till en .NET-webbprojekt][greenbrown] |ServerContext<br/>Härledd<br/>Prestandaräknare<br/>Begäranden<br/>**Undantag**<br/>Session<br/>användare |
| [Installera Status Monitor på IIS][redfield] |Beroenden<br/>ServerContext<br/>Härledd<br/>Prestandaräknare |
| [Lägg till Application Insights SDK i en Java-webbapp][java] |ServerContext<br/>Härledd<br/>Förfrågan<br/>Session<br/>användare |
| [Lägg till JavaScript SDK till webbsidan][client] |ClientContext <br/>Härledd<br/>Sidan<br/>ClientPerf<br/>Ajax |
| [Definiera standardegenskaper][apiproperties] |**Egenskaper för** på alla standardentiteter och anpassade händelser |
| [Anropa TrackMetric][api] |Numeriska värden<br/>**Egenskaper** |
| [Anropa spåra *][api] |Händelsenamn<br/>**Egenskaper** |
| [Anropa TrackException][api] |**Undantag**<br/>Stackdump<br/>**Egenskaper** |
| SDK kan inte samla in data. Exempel: <br/> -Det går inte att få åtkomst till prestandaräknare<br/> -undantag i telemetri-initierare |SDK-diagnostik |

För [SDK: er för andra plattformar][platforms], finns i sina dokument.

#### <a name="the-classes-of-collected-data"></a>Klasser av insamlade data

| Insamlade data-klass | Innehåller (inte en fullständig förteckning) |
| --- | --- |
| **Egenskaper** |**Alla data – avgörs av din kod** |
| DeviceContext |ID, IP-, språk, enhetsmodell, nätverk, nätverkstyp, namn på OEM, skärmupplösning, Rollinstans, rollnamn, typ av enhet |
| ClientContext |Operativsystem, språk, språk, nätverk och fönstret lösning |
| Session |Sessions-id |
| ServerContext |Datornamn, språk, OS, enhet, användarens session, användarkontext, igen |
| Härledd |geoplats från IP-adress, tidsstämpel, operativsystem, webbläsare |
| Mått |Tjänstmåttets namn och värde |
| Händelser |Händelsenamn och värde |
| PageViews |URL- och eller inloggningsnamn |
| Klienten perf |URL-/ sidnamn, inläsningstid för webbläsare |
| Ajax |HTTP-anrop från webbsida till servern |
| Begäranden |URL: en, varaktighet, svarskod |
| Beroenden |Typ (SQL, HTTP,...), anslutningssträngen eller URI, synkronisering/async, varaktighet, lyckades, SQL-uttryck (med Status Monitor) |
| **Undantag** |Typ, **meddelande**, anropa stackar, käll-fil- och rad number, tråd-id |
| Krascher |Process-id, id för överordnad process, krascher tråd-ID. programmet patch, id, build;  Undantagstyp, adress, reason; dold symboler och registrerar, binära start- och slut-adresser, binärt namn och sökväg, processortyp |
| Spårning |**Meddelandet** och allvarlighetsgrad |
| Prestandaräknare |Processortid, minne, förfrågningar, antal undantag, privata byte för process, IO-frekvens, varaktighet för begäran, Kölängd för begärande |
| Tillgänglighet |Svarskod för Web-test, varaktigheten för varje steg, testnamn, tidsstämpel, lyckades, svarstid, testplats |
| SDK-diagnostik |Spårningsmeddelande eller undantag |

Du kan [inaktivera vissa data genom att redigera ApplicationInsights.config][config]

> [!NOTE]
> Klientens IP-adress som används för att härleda geografisk plats, men som standard IP-data lagras inte längre och nollor skrivs till det tillhörande fältet. Vill veta mer om hantering av personuppgifter vi rekommenderar detta [artikeln](../../azure-monitor/platform/personal-data-mgmt.md#application-data). Om du behöver lagra IP-adress kan du göra det med en [telemetriinitieraren](./../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer).

## <a name="credits"></a>Eftertexter
Den här produkten innehåller GeoLite2 data som skapats av MaxMind, tillgängliga från [ https://www.maxmind.com ](https://www.maxmind.com).



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

