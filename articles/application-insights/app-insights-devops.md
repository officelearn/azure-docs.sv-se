---
title: Web application performance monitoring - Azure Application Insights | Microsoft Docs
description: Hur Application Insights passar in i devOps-cykel
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: a9a6e513d95df5dafba82556f74ec209529ff58d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294919"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Djup diagnostik för webbappar och tjänster med Application Insights
## <a name="why-do-i-need-application-insights"></a>Varför behöver Application Insights?
Application Insights övervakar webbappen körs. Det finns information om fel och problem med prestanda och hjälper dig att analysera hur kunder använder din app. Den fungerar för appar som körs på flera olika plattformar (ASP.NET, J2EE, Node.js,...) och finns i molnet eller lokalt. 

![Aspekter av komplexitet leverera webbprogram](./media/app-insights-devops/010.png)

Det är viktigt att övervaka en moderna program när den körs. Viktigast av allt vill du identifiera fel innan de flesta av dina kunder göra. Du också vill identifiera och åtgärda problem med prestanda som, när det är inte allvarligt, kanske saker går långsammare eller orsaka vissa problem som uppstår till användarna. Och när systemet genomför du är nöjd du vill veta vad användarna gör med den: använder de senaste funktionen? De lyckas med den?

Moderna program utvecklas i en cykel med kontinuerlig leverans: släpp en ny funktion eller förbättring; Se hur det fungerar för användarna. Planera nästa ökningen av utveckling baserat på denna kunskap. En viktig del av den här cykeln är observationsintervallet-fasen. Application Insights innehåller verktyg för att övervaka ett webbprogram för prestanda och användning.

Den viktigaste aspekten av den här processen är diagnostik- och diagnos. Om programmet misslyckas är business förlorad. Den primära rollen av ett ramverk för övervakning är därför att upptäcka fel på ett tillförlitligt sätt, meddela dig omedelbart och det innehåller information som behövs för att diagnosticera problemet. Detta är exakt det Application Insights.

### <a name="where-do-bugs-come-from"></a>Varifrån kommer buggar?
Fel i web system uppkommer vanligtvis konfigurationsproblem eller felaktiga samverkan mellan många komponenter. Den första uppgiften när hantera en liveplatsen incident är därför att identifiera lokus problemets: vilken komponent eller relationen är orsaken?

En del av oss dem med grå hår kan komma ihåg en enklare era som körde ett program i en dator. Utvecklare kan testa den noggrant innan du levererar. och har levererats, skulle sällan se eller göra det igen. Användare måste tappade med resterande programfel i många år. 

Vad är nu så mycket annat. Din app har en mängd olika enheter ska köras på och det kan vara svårt att garantera på var och en exakt samma beteende. Värd för appar i molnet innebär buggar kan åtgärdas snabbt, men det innebär också kontinuerlig konkurrens och förväntningen på nya funktioner med återkommande intervall. 

I dessa villkor är det enda sättet att ha en fast kontroll på antalet programfel automatiserad enhet testning. Är det omöjligt att manuellt testa allt på varje leverans. Testa enheten tillhör nu tar över som standard skapar. Verktyg, till exempel Xamarin Test molnet hjälpa genom att tillhandahålla automatisk UI tester på flera versioner av webbläsaren. Dessa tester systemen kan vi hoppas att frekvensen för fel som hittas i en app kan hållas till ett minimum.

Vanliga webbprogram har många live komponenter. Förutom klienten (i en webbläsare och enheter appen) och webbservern kan du det förväntas vara betydande backend-bearbetning. Kanske är serverdelen en pipeline-komponenter eller en glesare samling samverkande delar. Och många av dem kommer inte att påverka - de är externa tjänster som du är beroende av.

Det kan vara svårt och uneconomical att testa och vill, var möjligt felläget i live-systemet sig själv i konfigurationer som dessa. 

### <a name="questions-"></a>Frågor...
Några frågor som ber vi när vi utvecklar web systemet:

* Min app kraschar? 
* Vad hände exakt? – Om en begäran misslyckades, vill jag veta hur den har fått det. Vi behöver en spårning av händelser...
* Är min app tillräckligt snabbt? Hur lång tid tar det för att besvara vanliga begäranden?
* Servern kan hantera belastningen? När antalet förfrågningar stiger svarstiden håller konstant?
* Hur responsiv är min beroenden - REST API: erna, databaser och andra komponenter som anropar min app. Om systemet är långsam, i synnerhet är det min komponent eller får jag långsamt svar från någon annan?
* Är min app uppåt eller nedåt? Kan det ses från hela världen? Meddela mig om avbryts...
* Vad är den grundläggande orsaken? Var felet i min-komponent eller ett beroende? Är det ett kommunikationsproblem?
* Hur många användare påverkas? Om jag har fler än ett av problemen med att ta itu med, vilket är den viktigaste?

## <a name="what-is-application-insights"></a>Vad är Application Insights?
![Grundläggande arbetsflöde för Application Insights](./media/app-insights-devops/020.png)

1. Application Insights instruments din app och skickar telemetri om den medan programmet körs. Du kan skapa Application Insights SDK i appen, eller du kan använda instrumentation vid körning. Den tidigare metoden är mer flexibel som du kan lägga till egna telemetri till vanliga moduler.
2. Telemetrin skickas till Application Insights-portalen, där den lagras och bearbetas. (Även om Application Insights finns i Microsoft Azure, kan övervaka alla webbappar – inte bara Azure apps.)
3. Telemetrin visas för dig i form av diagram och tabeller av händelser.

Det finns två typer av telemetri: aggregerade och raw-instanser. 

* Instansdata innehåller till exempel en rapport med en begäran som har tagits emot av ditt webbprogram. Du kan hitta för och kontrollera informationen för en begäran med hjälp av verktyget Sök i Application Insights-portalen. Instansen skulle innehålla data, till exempel hur länge din app tog att svara på begäran, samt begärd URL, ungefärlig plats för klienten och andra data.
* Sammanställda data innehåller antalet händelser per tidsenhet, så att du kan jämföra andelen begäranden med svarstider. Den omfattar också medelvärden av mätvärden, till exempel svarstid för begäran.

De huvudsakliga kategorierna av data är:

* Begäranden till din app (vanligtvis HTTP-begäranden) med data på URL: en, svarstid, och har lyckats eller misslyckats.
* Beroenden - REST- och SQL-anrop gjorda av din app, även med URI, svarstider och lyckades
* Undantag, inklusive stackspår.
* Sidan Visa data som kommer från användarnas webbläsare.
* Mått till exempel prestandaräknare, samt mått som du skriver själv. 
* Anpassade händelser som du kan använda för att spåra affärshändelser
* Loggspårningar som används för felsökning.

## <a name="case-study-real-madrid-fc"></a>Fallstudie: Verkliga Madrid F.C.
Webbtjänsten för [verkliga Madrid Football en](http://www.realmadrid.com/) fungerar ungefär 450 miljoner fläktar runtom i världen. Fläktar komma åt den både via webbläsare och i en mobila appar. Fläktar kan inte bara bok biljetter, men också komma åt information och videon klipp på resultat, spelare och kommande spel. De kan söka med filter som antal mål bedömas. Det finns även länkar till sociala medier. Användarupplevelsen är mycket personlig och är utformad som en dubbelriktad kommunikation för att öka interaktionen fläktar.

Lösningen [är ett system med tjänster och program i Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). Skalbarhet är ett krav: trafiken variabeln och kan nå mycket höga volymer under och runt matchar.

För riktiga Madrid, är det viktigt att övervaka systemets prestanda. Azure Application Insights ger en omfattande vy över systemet, säkerställer en tillförlitlig och hög servicenivå. 

I en också hämtar djup förståelse av dess fläktar: där de är (endast 3% är i Spanien), vilka intresse som de har i spelare, historiska resultat och kommande spel och hur de ska svara för att matcha resultat.

De flesta av dessa telemetridata samlas in automatiskt med ingen lagts till kod som förenklad lösningen och minskar operativa komplikationer.  För riktiga Madrid, Programinsikter behandlar 3.8 miljarder telemetri punkter varje månad.

Verkliga Madrid använder Power BI-modulen för att visa sina telemetri.

![Power BI vy av Application Insights telemetri](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Smart identifiering
[Proaktiv diagnostik](app-insights-proactive-diagnostics.md) är en ny funktion. Utan någon specialkonfiguration av du Application Insights automatiskt identifierar och varnar dig om ovanliga ökar i fel priser i din app. Det är smart att ignorera en bakgrund för tillfälliga fel och ökar som är bara proportionell mot en ökning av begäranden. Exempelvis fungerar om det finns ett fel i en av de tjänster som du är beroende av, eller om den nya bygger du precis distribuerade inte så bra sedan vet du om den när du tittar på din e-post. (Och det finns webhooks så att du kan utlösa andra appar.)

En annan aspekt av den här funktionen utför en daglig djupgående analys av dina telemetri som söker efter ovanliga mönster av prestanda som är svåra att identifiera. Det kan till exempel hitta långsamma prestanda som är associerad med ett visst geografiskt område eller med en viss webbläsarversion.

I båda fallen visar aviseringen inte bara symptom den identifieras, men ger dig även data som behövs för att diagnostisera problem, till exempel relevant undantag rapporter.

![E-post från proaktiv diagnostik](./media/app-insights-devops/030.png)

Kunden Samtec säger: ”under de senaste funktionen cutover påträffades under skalas databasen som träffa resurs gränsen och orsakar timeout. Proaktiv identifiering aviseringar har tagits emot bokstavligt som vi triaging problemet mycket nära realtid som annonseras. Den här aviseringen tillsammans med Azure-plattformen aviseringar hjälp oss nästan omedelbart åtgärda problemet. Total avbrottstid < 10 minuter ”.

## <a name="live-metrics-stream"></a>Live Metrics Stream
Distribuera den senaste versionen kan vara en angelägna upplevelse. Om det finns problem vill du veta om dem direkt, så att du kan säkerhetskopiera om det behövs. Direktsänd dataström med mått ger nyckelvärden med en fördröjning på ca en sekund.

![Live mått](./media/app-insights-devops/040.png)

Och du kan inspektera omedelbart ett exempel på några fel och undantag.

![Live felhändelser](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Programkarta
Programavbildningen upptäcker automatiskt topologi för tjänstprogram om prestandainformation ovanpå det, så att du lättare kan identifiera flaskhalsar och problematiska flöden över din miljö. På så sätt kan du identifiera programberoenden på Azure-tjänster. Du kan hantera problemet genom att förstå om det är koden-relaterade eller beroende relaterade och från en enda plats detaljerat relaterade diagnostik upplevelse. Programmet kan till exempel att misslyckas på grund av försämring av prestanda i SQL-nivå. Med programavbildningen, du kan se den omedelbart och detaljerat SQL Index Advisor eller fråga insikter uppstå.

![Programkarta](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Med [Analytics](app-insights-analytics.md), du kan skriva godtycklig frågor i ett kraftfullt SQL-liknande språk.  Diagnostisera över hela appen stacken blir enkelt som olika perspektiv Anslut och du kan ställa rätt frågor att korrelera tjänstens prestanda med affärsmått och Customer Experience. 

Du kan fråga telemetri instans och mått rådata som lagras i portalen. Språket innehåller filter, koppling, sammanställning och andra åtgärder. Du kan beräkna fält och utföra statistiska analyser. Det finns både tabular och grafiska visualiseringar.

![Analytics-fråga och resultat diagram](./media/app-insights-devops/025.png)

Exempelvis är det enkelt att:

* Segmentera prestandadata för ditt program begäran av kunden nivåer att förstå sina erfarenheter.
* Söka efter specifika felkoder eller anpassade händelsenamn under liveplatsen undersökningar.
* Detaljnivån i appen användningen av vissa kunder att förstå hur funktionerna har köpt och antas.
* Spåra sessioner och svarstider för specifika användare att aktivera stöd och åtgärder team kan ge instant kundsupport.
* Fastställa vanliga app funktioner för att besvara frågor från funktionen prioritering.

Kunden DNN säger: ”Application Insights har gett oss med det saknas en del av Ekvationen för att kunna att kombinera, sortera, fråga och filtrera data efter behov. Att tillåta vårt team att använda sina egna ingenuity och erfarenhet som krävs för att hitta data med ett kraftfullt frågespråk kan vi hitta insikter och lösa problem visste vi vi hade. En mängd intressanta svar kommer från frågor som börjar med *' jag konstigt om '.*”

## <a name="development-tools-integration"></a>Development tools integrering
### <a name="configuring-application-insights"></a>Konfigurera Application Insights
Visual Studio och Eclipse har verktyg för att konfigurera rätt SDK-paket för projektet som du utvecklar. Det finns ett kommando för att lägga till Application Insights.

Om du använder ett loggningsramverk för spårning som Log4N, NLog eller System.Diagnostics.Trace, sedan får du möjlighet att skicka loggar till Application Insights tillsammans med andra telemetri så att du enkelt kan jämföra spåren med begäranden, beroende anrop och undantag.

### <a name="search-telemetry-in-visual-studio"></a>Sök telemetri i Visual Studio
När du utvecklar och felsökning av en funktion kan du visa och söka telemetrin direkt i Visual Studio med hjälp av samma sökning funktionerna i webbportalen.

Och när Application Insights loggar ett undantag, du kan visa datapunkten i Visual Studio och gå direkt till relevant kod.

![Visual Studio-sökning](./media/app-insights-devops/060.png)

Under felsökning, har du alternativet att behålla telemetrin i utvecklingsdatorn, visar den i Visual Studio men utan att skicka det till portalen. Det här alternativet för lokal undviker blanda felsökning med telemetri för produktion.

### <a name="build-annotations"></a>Skapa anteckningar
Om du använder Visual Studio Team Services för att skapa och distribuera din app, visas distribution anteckningar i diagram i portalen. Om den senaste versionen har någon effekt på mätvärden, blir det uppenbara.

![Skapa anteckningar](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Arbetsobjekt
När en varning utlöses kan Application Insights automatiskt skapa ett arbetsobjekt i ditt arbete system för uppföljning.

## <a name="but-what-about"></a>Men vad händer om...?
* [Sekretess- och](app-insights-data-retention-privacy.md) -din telemetri sparas på Azure säkra servrar.
* Prestanda - påverkan är mycket låg. Telemetri batchar.
* [Priser](app-insights-pricing.md) – du kan komma igång gratis och som fortsätter när du använder låg volym.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Det är lätt att komma igång med Application Insights. De huvudsakliga alternativ är:

* Instrumentera en webbapp körs redan. Detta ger dig all telemetri för inbyggda prestanda. Den är tillgänglig för [Java](app-insights-java-live.md) och [IIS-servrar](app-insights-monitor-performance-live-website-now.md), och också för [Azure-webbappar](app-insights-azure.md).
* Instrumentera ditt projekt under utveckling. Du kan göra detta för [ASP.NET](app-insights-asp-net.md) eller [Java](app-insights-java-get-started.md) appar, samt [Node.js](app-insights-nodejs.md) och [andra typer](app-insights-platforms.md). 
* Instrument [webbsidor](app-insights-javascript.md) genom att lägga till ett kort kodfragment.

