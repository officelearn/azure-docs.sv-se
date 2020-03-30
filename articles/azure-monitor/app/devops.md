---
title: Prestandaövervakning för webbappar – Azure Application Insights
description: Så här passar Application Insights i devOps-cykeln
ms.topic: conceptual
ms.date: 12/21/2018
ms.openlocfilehash: 24095aade80022d1e1ebb38357971512bfc873c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669700"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Djup diagnostik för webbappar och tjänster med Application Insights
## <a name="why-do-i-need-application-insights"></a>Varför behöver jag Application Insights?
Application Insights övervakar din webbapp som körs. Den berättar om fel och prestandaproblem och hjälper dig att analysera hur kunderna använder din app. Det fungerar för appar som körs på många plattformar (ASP.NET, Java EE, Node.js, ...) och finns antingen i molnet eller lokalt. 

![Aspekter av komplexiteten i att leverera webbappar](./media/devops/010.png)

Det är viktigt att övervaka ett modernt program medan det körs. Viktigast av allt, du vill upptäcka fel innan de flesta av dina kunder gör. Du vill också upptäcka och åtgärda prestandaproblem som, även om de inte är katastrofala, kanske sakta ner saker eller orsaka vissa olägenheter för dina användare. Och när systemet presterar till din belåtenhet, vill du veta vad användarna gör med det: Använder de den senaste funktionen? Lyckas de med det?

Moderna webbapplikationer utvecklas i en cykel av kontinuerlig leverans: släpp en ny funktion eller förbättring; observera hur bra det fungerar för användarna; planera nästa utvecklingssteg baserat på denna kunskap. En viktig del av denna cykel är observationsfasen. Application Insights tillhandahåller verktyg för att övervaka ett webbprogram för prestanda och användning.

Den viktigaste aspekten av denna process är diagnostik och diagnos. Om programmet misslyckas går verksamheten förlorad. Den främsta rollen för en övervakningsram är därför att upptäcka fel på ett tillförlitligt sätt, meddela dig omedelbart och att presentera dig med den information som behövs för att diagnostisera problemet. Detta är precis vad Application Insights gör.

### <a name="where-do-bugs-come-from"></a>Var kommer buggar ifrån?
Fel i webbsystem uppstår vanligtvis från konfigurationsproblem eller dåliga interaktioner mellan deras många komponenter. Den första uppgiften när man tar itu med en live site incident är därför att identifiera locus av problemet: vilken komponent eller relation är orsaken?

Några av oss, de med grått hår, kan minnas en enklare era där ett datorprogram kördes i en dator. Utvecklarna skulle testa det ordentligt innan du skickar den; och efter att ha skickat den, skulle sällan se eller tänka på det igen. Användarna skulle behöva stå ut med de återstående buggar i många år. 

Saker och ting är så väldigt annorlunda nu. Din app har en uppsjö av olika enheter att köra på, och det kan vara svårt att garantera exakt samma beteende på var och en. Hosting apps i molnet innebär buggar kan åtgärdas snabbt, men det innebär också kontinuerlig konkurrens och förväntningar på nya funktioner med täta mellanrum. 

Under dessa förhållanden är det enda sättet att hålla en fast kontroll på felräkningen automatiserad enhetstestning. Det skulle vara omöjligt att manuellt testa allt på varje leverans. Enhetstest är nu en vanlig del av byggprocessen. Verktyg som Xamarin Test Cloud hjälp genom att tillhandahålla automatiserade gränssnittstestning på flera webbläsarversioner. Dessa testregimer tillåter oss att hoppas att antalet buggar som finns i en app kan hållas till ett minimum.

Typiska webbapplikationer har många aktiva komponenter. Förutom klienten (i en webbläsare eller enhet app) och webbservern, det är sannolikt att vara betydande backend bearbetning. Kanske backend är en pipeline av komponenter, eller en lösare samling av samarbetande bitar. Och många av dem kommer inte att vara i din kontroll - de är externa tjänster som du är beroende av.

I konfigurationer som dessa kan det vara svårt och oekonomiskt att testa för, eller förutse, alla möjliga fellägen, förutom i själva livesystemet. 

### <a name="questions-"></a>Frågor...
Några frågor vi ställer när vi utvecklar ett webbsystem:

* Kraschar min app? 
* Vad exakt hände? - Om det misslyckades med en förfrågan, vill jag veta hur den kom dit. Vi behöver ett spår av händelser...
* Är min app tillräckligt snabb? Hur lång tid tar det att svara på vanliga förfrågningar?
* Kan servern hantera belastningen? När antalet begäranden stiger, håller svarstiden stadigt?
* Hur responsiva är mina beroenden - REST-API:erna, databaserna och andra komponenter som min app anropar. I synnerhet om systemet är långsamt, är det min komponent, eller får jag långsamma svar från någon annan?
* Är min app upp eller ned? Kan det ses från hela världen? Låt mig veta om det slutar ....
* Vad är grundorsaken? Var felet i min komponent eller ett beroende? Är det en kommunikationsfråga?
* Hur många användare påverkas? Om jag har mer än en fråga att ta itu med, vilket är det viktigaste?

## <a name="what-is-application-insights"></a>Vad är Application Insights?
![Grundläggande arbetsflöde för Application Insights](./media/devops/020.png)

1. Application Insights instrumenterar din app och skickar telemetri om den medan appen körs. Antingen kan du skapa SDK för programinsikter i appen eller använda instrumentering vid körning. Den tidigare metoden är mer flexibel, eftersom du kan lägga till din egen telemetri till de vanliga modulerna.
2. Telemetrin skickas till application insights-portalen, där den lagras och bearbetas. (Även om Application Insights finns i Microsoft Azure kan den övervaka alla webbappar – inte bara Azure-appar.)
3. Telemetrin presenteras för dig i form av diagram och tabeller över händelser.

Det finns två huvudtyper av telemetri: aggregerade och råa instanser. 

* Instansdata innehåller till exempel en rapport om en begäran som har tagits emot av webbappen. Du kan söka efter och granska information om en begäran med hjälp av sökverktyget i application insights-portalen. Instansen skulle innehålla data som hur lång tid det tog att svara på begäran, samt den begärda webbadressen, ungefärlig plats för klienten och andra data.
* Aggregerade data inkluderar antal händelser per enhetstid, så att du kan jämföra antalet begäranden med svarstiderna. Den innehåller också medelvärden för mått som svarstider för begäran.

De viktigaste kategorierna av uppgifter är:

* Begäranden till din app (vanligtvis HTTP-begäranden), med data om URL, svarstid och lyckad eller misslyckad.
* Beroenden - REST- och SQL-samtal från din app, även med URI, svarstider och framgång
* Undantag, inklusive stackspårningar.
* Sidvydata, som kommer från användarnas webbläsare.
* Mått som prestandaräknare samt mått som du skriver själv. 
* Anpassade händelser som du kan använda för att spåra affärshändelser
* Loggspårningar som används för felsökning.

## <a name="case-study-real-madrid-fc"></a>Fallstudie: Real Madrid FC
Webbtjänsten i [Real Madrid Football Club](https://www.realmadrid.com/) tjänar cirka 450 miljoner fans runt om i världen. Fans tillgång till den både via webbläsare och klubbens mobilappar. Fans kan inte bara boka biljetter, men också få tillgång till information och videoklipp om resultat, spelare och kommande spel. De kan söka med filter som antal gjorda mål. Det finns också länkar till sociala medier. Användarupplevelsen är mycket personlig och är utformad som en tvåvägskommunikation för att engagera fansen.

Lösningen [är ett system för tjänster och program på Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Skalbarhet är ett viktigt krav: trafiken varierar och kan nå mycket höga volymer under och runt matchningar.

För Real Madrid är det viktigt att övervaka systemets prestanda. Azure Application Insights ger en omfattande vy över hela systemet, vilket garanterar en tillförlitlig och hög servicenivå. 

Klubben får också en djup förståelse för sina fans: var de är (endast 3% är i Spanien), vilket intresse de har för spelare, historiska resultat och kommande spel, och hur de svarar på matchresultat.

De flesta av dessa telemetridata samlas automatiskt in utan extra kod, vilket förenklade lösningen och minskad operativ komplexitet.  För Real Madrid handlar Application Insights om 3,8 miljarder telemetripoäng varje månad.

Real Madrid använder Power BI-modulen för att visa sin telemetri.

![Power BI-vy för programinsikter telemetri](./media/devops/080.png)

## <a name="smart-detection"></a>Smart identifiering
[Proaktiv diagnostik](../../azure-monitor/app/proactive-diagnostics.md) är en ny funktion. Utan någon särskild konfiguration av dig identifierar och varnar Application Insights dig automatiskt om ovanliga ökningar av felfrekvenser i din app. Det är smart nog att ignorera en bakgrund av enstaka misslyckanden, och även stiger som helt enkelt står i proportion till en ökning av förfrågningar. Så till exempel, om det finns ett fel i en av de tjänster du är beroende av, eller om den nya versionen du just distribuerat inte fungerar så bra, då vet du om det så fort du tittar på din e-post. (Och det finns webhooks så att du kan utlösa andra appar.)

En annan aspekt av den här funktionen utför en daglig djupgående analys av din telemetri, letar efter ovanliga mönster av prestanda som är svåra att upptäcka. Den kan till exempel hitta långsam prestanda som är associerad med ett visst geografiskt område eller med en viss webbläsarversion.

I båda fallen berättar aviseringen inte bara vilka symptom den har upptäckt, utan ger dig också data som du behöver för att diagnostisera problemet, till exempel relevanta undantagsrapporter.

![E-post från proaktiv diagnostik](./media/devops/030.png)

Kunden Samtec sa: "Under en nyligen funktion cutover, vi hittade en underskalad databas som slog sina resursgränser och orsakar timeout. Proaktiv upptäckt varningar kom igenom bokstavligen som vi triaging frågan, mycket nära realtid som annonseras. Den här varningen tillsammans med Azure-plattformens aviseringar hjälpte oss att nästan omedelbart åtgärda problemet. Totalt driftstopp < 10 minuter."

## <a name="live-metrics-stream"></a>Live-ström med mätvärden
Att distribuera den senaste versionen kan vara en ängslig upplevelse. Om det finns några problem, vill du veta om dem direkt, så att du kan backa ut om det behövs. Live Metrics Stream ger dig viktiga mått med en svarstid på ungefär en sekund.

![Live-mått](./media/devops/0040.png)

Och låter dig omedelbart inspektera ett urval av eventuella fel eller undantag.

![Händelser för livefel](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Programkarta
Application Map identifierar automatiskt din programtopologi, lägga prestandainformation ovanpå den, så att du enkelt kan identifiera flaskhalsar i prestanda och problematiska flöden i din distribuerade miljö. Det gör att du kan identifiera programberoenden på Azure Services. Du kan triage problemet genom att förstå om det är kod-relaterade eller beroende relaterade och från en enda plats detalj i relaterade diagnostik erfarenhet. Ditt program kan till exempel misslyckas på grund av prestandaförsämring på SQL-nivå. Med programkarta kan du se den direkt och detaljgranska till sql indexrådgivare eller frågestatistik.

![Programkarta](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Programinsiktsanalys
Med [Analytics](../../azure-monitor/app/analytics.md)kan du skriva godtyckliga frågor på ett kraftfullt SQL-liknande språk.  Det blir enkelt att diagnostisera över hela appstacken när olika perspektiv ansluts och du kan ställa rätt frågor för att korrelera serviceprestanda med affärsmått och kundupplevelse. 

Du kan fråga alla telemetriinstanser och måttråvara som lagras i portalen. Språket innehåller filter, koppling, aggregering och andra åtgärder. Du kan beräkna fält och utföra statistisk analys. Det finns både tabell- och grafiska visualiseringar.

![Analytics-fråge- och resultatdiagram](./media/devops/0025.png)

Det är till exempel enkelt att:

* Segmentera programmets prestandadata för begäran efter kundnivåer för att förstå deras upplevelse.
* Sök efter specifika felkoder eller anpassade händelsenamn under undersökningar på livewebbplatser.
* Öka detaljnivån i appanvändningen av specifika kunder för att förstå hur funktioner förvärvas och används.
* Spåra sessioner och svarstider för specifika användare så att support- och driftteam kan tillhandahålla omedelbar kundsupport.
* Ta reda på appfunktioner som används ofta för att svara på frågor om prioritering av funktioner.

Kunden DNN sa: "Application Insights har gett oss den saknade delen av ekvationen för att kunna kombinera, sortera, fråga och filtrera data efter behov. Att låta vårt team använda sin egen uppfinningsrikedom och erfarenhet för att hitta data med ett kraftfullt frågespråk har gjort det möjligt för oss att hitta insikter och lösa problem som vi inte ens visste att vi hade. Många intressanta svar kommer från frågorna som börjar med *"Jag undrar om ...".*

## <a name="development-tools-integration"></a>Integration av utvecklingsverktyg
### <a name="configuring-application-insights"></a>Konfigurera programinsikter
Visual Studio och Eclipse har verktyg för att konfigurera rätt SDK-paket för det projekt du utvecklar. Det finns ett menykommando för att lägga till Programstatistik.

Om du råkar använda ett spårningsloggningsramverk som Log4N, NLog eller System.Diagnostics.Trace får du möjlighet att skicka loggarna till Application Insights tillsammans med den andra telemetrin, så att du enkelt kan korrelera spårningarna med begäranden, beroende samtal och undantag.

### <a name="search-telemetry-in-visual-studio"></a>Sök telemetri i Visual Studio
När du utvecklar och felsöker en funktion kan du visa och söka i telemetrin direkt i Visual Studio med samma sökmöjligheter som i webbportalen.

Och när Application Insights loggar ett undantag kan du visa datapunkten i Visual Studio och hoppa direkt till relevant kod.

![Visual Studio-sökning](./media/devops/060.png)

Under felsökning har du möjlighet att behålla telemetrin i utvecklingsmaskinen, visa den i Visual Studio men utan att skicka den till portalen. Det här lokala alternativet undviker att blanda felsökning med produktionstelemetri.

### <a name="work-items"></a>Arbetsobjekt
När en avisering utlöses kan Application Insights automatiskt skapa ett arbetsobjekt i ditt arbetsspårningssystem.

## <a name="but-what-about"></a>Men hur är det...?
* [Sekretess och lagring](../../azure-monitor/app/data-retention-privacy.md) – Din telemetri sparas på Azure-säkra servrar.
* Prestanda - effekten är mycket låg. Telemetri är batchat.
* [Prissättning](../../azure-monitor/app/pricing.md) - Du kan komma igång gratis, och det fortsätter medan du är i låg volym.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Det är enkelt att komma igång med Application Insights. De viktigaste alternativen är:

* [IIS-servrar](../../azure-monitor/app/monitor-performance-live-website-now.md)och även för [Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrumentera ditt projekt under utveckling. Du kan göra detta för [ASP.NET-](../../azure-monitor/app/asp-net.md) eller [Java-appar,](../../azure-monitor/app/java-get-started.md) samt [Node.js](../../azure-monitor/app/nodejs.md) och en mängd andra [typer](../../azure-monitor/app/platforms.md). 
* Instrumentera [en webbsida](../../azure-monitor/app/javascript.md) genom att lägga till ett kort kodavsnitt.

