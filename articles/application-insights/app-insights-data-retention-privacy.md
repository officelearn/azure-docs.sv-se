---
title: Datalagring och lagring i Azure Application Insights | Microsoft Docs
description: "Kvarhållning och sekretess Principframställning"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: mbullwin
ms.openlocfilehash: 1818e564acb0e9b5fa620d6f38db141811ca9777
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Datainsamling, kvarhållning och lagring i Application Insights


När du installerar [Azure Application Insights] [ start] SDK i din app, skickas telemetri om din app till molnet. Naturligtvis finns ansvarar utvecklare som vill veta exakt vilken information som skickas, vad som händer med data och hur de kan behålla kontrollen över den. I synnerhet kunde känsliga data skickas, där är den lagrade och hur säker är det? 

Första kort svaret:

* De moduler som standard telemetri som kör ”out of box” är inte sannolikt att känsliga data ska skickas till tjänsten. Telemetrin är bekymrad över belastning, prestanda och användning mått, undantag rapporter och andra diagnostikdata. Huvudanvändaren data visas i diagnostiska rapporter är URL: er; men din app i varje fall inte bör Placera känsliga data i oformaterad text i en URL.
* Du kan skriva kod som skickar ytterligare anpassad telemetri för att hjälpa dig med diagnostik- och användningsdata för övervakning. (Den här utökningsbarhet är en bra funktion i Application Insights.) Det är möjligt, av misstag att skriva koden så att den innehåller personlig och andra känsliga data. Om ditt program fungerar med sådana uppgifter bör du använda en noggrann granska processer på alla koden du skriver.
* När du utvecklar och testar din app, är det enkelt att kontrollera vad som ska skickas av SDK. Data visas i Felsökning utdata windows IDE och webbläsare. 
* Data lagras i [Microsoft Azure](http://azure.com) servrar i USA eller Europa. (Men din app kan köras var som helst.) Azure har [stark säkerhet processer och uppfyller ett brett spektrum av efterlevnadsstandarder](https://azure.microsoft.com/support/trust-center/). Endast du och din avsedda grupp har åtkomst till dina data. Microsoft-Personal kan ha begränsad åtkomst till den endast under vissa begränsade omständigheter med dina kunskaper. Den är krypterad under överföringen, dock inte i servrar.

Resten av den här artikeln utvecklar mer komplett svaren på dessa frågor. Den har utformats för att vara fristående, så att du kan visa att kollegor som inte är en del av din arbetsgrupp.

## <a name="what-is-application-insights"></a>Vad är Application Insights?
[Azure Application Insights] [ start] är en tjänst från Microsoft som hjälper dig att förbättra prestanda och användbarhet för live programmet. Det övervakar programmet den tid som den körs, både under testningen och när du har publicerat eller distribuera den. Application Insights skapar diagram och tabeller som visar dig, till exempel, vilka tider som du får de flesta användare, hur responsiv appen är och hur väl den hanteras av externa tjänster som den är beroende av. Om det finns krascher, fel eller prestandaproblem kan söka du igenom telemetridata detaljerat för att diagnosticera orsaken. Och tjänsten skickar du e-postmeddelanden om det finns några ändringar i tillgänglighet och prestanda för din app.

För att få den här funktionen måste installera du en Application Insights SDK i ditt program blir en del av koden. När appen körs SDK övervakar driften och skickar telemetri till Application Insights-tjänsten. Det här är en molntjänst av [Microsoft Azure](http://azure.com). (Men Application Insights fungerar för alla program, inte bara de som finns i Azure.)

![SDK i din app skickas telemetri till Application Insights-tjänsten.](./media/app-insights-data-retention-privacy/01-scheme.png)

Application Insights-tjänsten lagrar och analyserar telemetrin. Om du vill se analys eller Sök igenom lagrade telemetri, logga in på ditt Azure-konto och öppna Application Insights-resurs för ditt program. Du kan också dela åtkomst till data med andra medlemmar i gruppen, eller med angivna Azure-prenumeranter.

Du kan ha data som exporteras från Application Insights-tjänsten, till exempel till en databas eller till externa verktyg. Du kan ange varje verktyg med en särskild nyckel som hämtas från tjänsten. Nyckeln kan återkallas om det behövs. 

Application Insights SDK är tillgängliga för en mängd olika typer: webbtjänster som finns i din egen J2EE eller ASP.NET-servrar eller i Azure; Web klienter – det vill säga den kod som körs på en webbsida; -program och tjänster. appar för enheter, till exempel Windows Phone, iOS och Android. Alla skicka telemetri till samma tjänst.

## <a name="what-data-does-it-collect"></a>Vilka data samlar det?
### <a name="how-is-the-data-is-collected"></a>Hur är data samlas in?
Det finns tre datakällor för data:

* SDK, som du integrerar med din app antingen [under utveckling](app-insights-asp-net.md) eller [vid körning](app-insights-monitor-performance-live-website-now.md). Det finns olika SDK: er för olika programtyper. Det finns också en [SDK för webbsidor](app-insights-javascript.md), som läses in i den slutanvändarens webbläsare tillsammans med sidan.
  
  * Varje SDK innehåller ett antal [moduler](app-insights-configuration-with-applicationinsights-config.md), som använder olika metoder för att samla in olika typer av telemetri.
  * Du kan använda dess API för att skicka din egen telemetri, förutom modulerna som standard om du installerar SDK under utveckling. Den här telemetri om anpassade kan innehålla alla data som du vill skicka.
* I vissa webbservrar finns också agenter som kör tillsammans med appen och skicka telemetri om CPU, minne och användandet av nätverket. Till exempel virtuella Azure-datorer Docker-värdar och [J2EE servrar](app-insights-java-agent.md) kan ha dessa agenter.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) processer som körs av Microsoft som skickar begäranden till ditt webbprogram med jämna mellanrum. Resultatet skickas till Application Insights-tjänsten.

### <a name="what-kinds-of-data-are-collected"></a>Vilka typer av data samlas in?
Huvudkategorierna är:

* [Web server telemetri](app-insights-asp-net.md) -HTTP-begäranden.  URI: N, tid det tar att bearbeta begäran, svarskod, klientens IP-adress. Sessions-id.
* [Webbsidor](app-insights-javascript.md) -sidan, användare och session räknas. Sidinläsningstider. Undantag. AJAX-anrop.
* Prestandaräknare - minne, CPU, IO, användandet av nätverket.
* Klienten och servern kontext - OS, språk, typ av enhet, webbläsare, skärmupplösning.
* [Undantag](app-insights-asp-net-exceptions.md) och krascher - **stacken Dumpar**, skapa id, processortyp. 
* [Beroenden](app-insights-asp-net-dependencies.md) -anrop till externa tjänster, till exempel vila, SQL, AJAX. URI: N eller anslutningssträngen, varaktighet, lyckas, kommandot.
* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md) -varaktighet för test och steg, svar.
* [Spårningsloggar](app-insights-asp-net-trace-logs.md) och [telemetri om anpassade](app-insights-api-custom-events-metrics.md) - **något du kod till din loggar eller telemetri**.

[Mer information om](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Hur kan jag bekräfta vad samlas?
Om du utvecklar programmet med Visual Studio kör appen i felsökningsläge (F5). Telemetrin visas i utdatafönstret. Därifrån kan du kopiera den och formatera den som JSON för enkel kontroll. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

Det finns också ett mer lättläst visa i fönstret diagnostik.

Öppna din webbläsare felsökning fönster för webbsidor.

![Tryck på F12 och öppna fliken nätverk.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Kan jag skriva kod för att filtrera telemetrin innan den skickas?
Detta skulle vara möjligt genom att skriva en [telemetri processor plugin](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Hur länge sparas data?
Rådata datapunkter (objekt som du kan fråga i Analytics och inspektera i sökningen) stannar i upp till 90 dagar. Om du behöver skydda data som är längre än den som du kan använda [löpande export](app-insights-export-telemetry.md) att kopiera den till ett lagringskonto.

Sammanställda data (det vill säga antal, medelvärden och andra statistiska data som du ser i måttet Explorer) finns kvar på en kornighet på 1 minut under 90 dagar.

## <a name="who-can-access-the-data"></a>Vem kan komma åt dessa data?
Data är synliga för dig och, om du har en organisationskonto gruppmedlemmarna. 

Den kan exporteras av dig och dina gruppmedlemmar och kan kopieras till andra platser och skickas till andra personer.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Vad gör Microsoft med information om min app skickar till Application Insights?
Microsoft använder informationen endast för att tillhandahålla tjänsten till dig.

## <a name="where-is-the-data-held"></a>Där lagras data?
* I USA, Europa eller Sydostasien. Du kan välja platsen när du skapar en ny Application Insights-resurs. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>Betyder det min app måste finnas i USA, Europa eller Sydostasien?
* Nej. Programmet kan köras var som helst, i din egen lokala värdar eller i molnet.

## <a name="how-secure-is-my-data"></a>Hur säker är Mina data?
Application Insights är en Azure-tjänst. Säkerhetsprinciper beskrivs i den [Azure-säkerhet, sekretess och kompatibilitet vitboken](http://go.microsoft.com/fwlink/?linkid=392408).

Data lagras i Microsoft Azure-servrar. Begränsningar för konton i Azure Portal beskrivs i den [Azure-säkerhet, sekretess och kompatibilitet dokumentet](http://go.microsoft.com/fwlink/?linkid=392408).

Åtkomst till dina data med Microsoft-Personal är begränsad. Vi har åtkomst till data tillåtelse och om det är nödvändigt att stödja användning av Application Insights. 

Data i mängd i våra kunders program (till exempel överföringshastighet och genomsnittlig storlek på spårningar) används för att förbättra Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Det gick någon annans telemetri som störa Application Insights-data?
De kan skicka ytterligare telemetri till ditt konto med hjälp av nyckeln instrumentation hittar du i koden för webbsidor. Med tillräckligt med ytterligare data skulle dina inte korrekt återger appens prestanda och användning.

Om du delar kod med andra projekt, Kom ihåg att ta bort din instrumentation nyckel.

## <a name="is-the-data-encrypted"></a>Krypteras data?
Inte i servrar för närvarande.

Krypteras alla data som flyttas mellan datacenter.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Krypteras data under överföring från mitt program till Application Insights servrar?
Ja, vi använder https för att skicka data till portalen från nästan alla SDK: er, inklusive webbservrar, enheter och HTTPS-webbsidor. Det enda undantaget är data som skickas från vanlig HTTP-webbsidor. 

## <a name="personally-identifiable-information"></a>Personligt identifierbar Information
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>Kunde personligt identifierbar Information (PII) skickas till Application Insights?
Ja, det är möjligt. 

Som en allmän vägledning:

* De flesta standard telemetri (det vill säga telemetri som skickas utan att du skriva någon kod) innehåller inte explicit personligt identifierbar information. Dock kan det vara möjligt att identifiera enskilda användare genom härledning från en samling av händelser.
* Undantag och spåra meddelanden kan innehålla personligt identifierbar information
* Anpassad telemetri - anrop som är till exempel TrackEvent som du kan skriva i kod med API- eller loggfil spåren - kan innehålla alla data som du väljer.

Tabellen i slutet av det här dokumentet innehåller mer detaljerade beskrivningar av insamlade data.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>Kan jag ansvarar för att följa lagar och förordningar om personligt identifierbar information?
Ja. Det är ditt ansvar att säkerställa att insamling och användning av data överensstämmer med lagar och förordningar och villkoren Microsoft Online Services.

Du bör på lämpligt sätt informera kunderna om programmet samlar in data och hur data används.

#### <a name="can-my-users-turn-off-application-insights"></a>Mina användare kan stänga av Application Insights?
Inte direkt. Vi tillhandahålla inte en växel som användarna kan användas för att stänga av Application Insights.

Men kan du implementera en sådan funktion i ditt program. Alla SDK innehåller en API-inställning som inaktiverar telemetri samling. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>Mina program oavsiktligt samlar in känslig information. Application Insights Skrubba kan dessa data så att den inte finns kvar?
Application Insights inte filtrera eller ta bort dina data. Du ska hantera data på rätt sätt och undvika att skicka sådana data till Application Insights.

## <a name="data-sent-by-application-insights"></a>Data som skickas av Application Insights
SDK: erna variera mellan plattformar och det finns flera komponenter som du kan installera. (Se [Application Insights - översikt][start].) Varje komponent skickar olika data.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Typer av data som skickas i olika scenarier
| Åtgärden | Dataklasser som samlas in (se nästa tabell) |
| --- | --- |
| [Lägg till Application Insights SDK i ett .NET-webbprojekt][greenbrown] |ServerContext<br/>Härleda<br/>Prestandaräknarna<br/>Begäranden<br/>**Undantag**<br/>Session<br/>användare |
| [Installera statusövervakaren på IIS][redfield] |Beroenden<br/>ServerContext<br/>Härleda<br/>Prestandaräknarna |
| [Lägg till Application Insights SDK för Java-webbapp][java] |ServerContext<br/>Härleda<br/>Förfrågan<br/>Session<br/>användare |
| [Lägg till JavaScript SDK på webbsidan][client] |ClientContext <br/>Härleda<br/>Sida<br/>ClientPerf<br/>Ajax |
| [Definiera standardegenskaper][apiproperties] |**Egenskaper för** för alla händelser som standard och anpassade |
| [Anropa TrackMetric][api] |Numeriska värden<br/>**Egenskaper** |
| [Anropa spåra *][api] |händelsenamnet<br/>**Egenskaper** |
| [Anropa TrackException][api] |**Undantag**<br/>Stackdump<br/>**Egenskaper** |
| SDK kan samla in data. Exempel: <br/> -Det går inte att komma åt prestandaräknarna<br/> -undantag i telemetri initieraren |SDK-diagnostik |

För [SDK: er för andra plattformar][platforms], se dokumenten.

#### <a name="the-classes-of-collected-data"></a>Klasser av insamlade data
| Insamlade dataklass | Innehåller (inte en fullständig förteckning) |
| --- | --- |
| **Egenskaper** |**Några data - bestäms av din kod** |
| DeviceContext |ID, IP-, språk, enhetsmodell, nätverk, nätverkstyp, OEM-namnet, skärmupplösning Rollinstansen, Role-namn, typ av enhet |
| ClientContext |OS, språk, språk, nätverk, fönstret upplösning |
| Session |Sessions-id |
| ServerContext |Namnet på datorn, språk, OS, enhet, användarens session, användarkontext, åtgärden |
| Härleda |geoplats från IP-adress, timestamp, OS, webbläsare |
| Mått |Tjänstmåttets namn och värde |
| Händelser |Händelsenamn och värde |
| PageViews |Sidan och URL eller inloggningsnamn |
| Klienten perf |URL-sida namn, inläsningstid för webbläsare |
| Ajax |HTTP-anrop från webbsidan till servern |
| Begäranden |URL: en varaktighet, svarskod |
| Beroenden |Typ (SQL, HTTP,...), anslutningssträngen eller URI, sync/asynkrona, varaktighet, lyckas, SQL-uttryck (med Status Monitor) |
| **Undantag** |Typ, **meddelande**, anropa stackar, käll-fil- och tal, tråd-id |
| Krascher |Process-id, överordnade process-id, tråd-id som kraschar. programkorrigering av-id, build;  Undantagstyp, adress, reason; dolda symboler och register, binära start- och adresser, binärt namn och sökväg, cpu-typ |
| Spårning |**Meddelandet** och allvarlighetsgrad |
| Prestandaräknarna |Processortid, minne, förfrågningar, undantag hastighet, process privata byte, i/o-hastighet, varaktighet för begäran Kölängd för begäran |
| Tillgänglighet |Web test svarskod, varaktighet för varje steg, namn på testet, timestamp, lyckade, svarstid, test-plats |
| SDK-diagnostik |Spårningsmeddelande eller ett undantag |

Du kan [inaktivera vissa data genom att redigera ApplicationInsights.config][config]

## <a name="credits"></a>Krediter
Den här produkten innehåller GeoLite2 data som skapats av MaxMind, från [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

