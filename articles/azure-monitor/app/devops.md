---
title: Web programprestanda övervakning – Azure Application Insights | Microsoft Docs
description: Hur Application Insights passar in i devOps-cykeln
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 24b0bc01b5cb4f1d2696a7c9526d586c9b42d0fc
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674590"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Djup diagnostik för webbappar och tjänster med Application Insights
## <a name="why-do-i-need-application-insights"></a>Varför behöver jag Application Insights?
Application Insights övervakar webbappen som körs. Det finns information om fel och problem med prestanda och hjälper dig att analysera hur kunder använder din app. Den fungerar för appar som körs på flera olika plattformar (ASP.NET, Java EE, Node.js,...) och är värd för antingen i molnet eller lokalt. 

![Aspekter av komplexiteten med att leverera webbappar](./media/devops/010.png)

Det är viktigt att övervaka ett modernt program när den körs. Det viktigaste är att du vill upptäcka fel innan de flesta av dina kunder gör. Du också vill identifiera och åtgärda problem med prestanda, medan inte oåterkalleligt, kanske saker går långsammare eller orsaka vissa besvär för användarna. Och när systemet fungerar tills du är nöjd, du vill veta vad användarna gör med den: Använder de senaste funktionen? De lyckas med den?

Moderna webbprogram har utvecklats i en cykel för kontinuerlig leverans av: levererar en ny funktion eller en förbättring; Se hur väl den fungerar för användarna. Planera steg utveckling baserat på denna kunskap. En viktig del av den här cykeln är Fjärrvisning-fasen. Application Insights erbjuder verktyg för att övervaka ett webbprogram för prestanda och användning.

Den viktigaste delen av den här processen är diagnostik- och diagnos. Om programmet misslyckas är företag förlorad. Den primära rollen av ett ramverk för övervakning är därför att upptäcka fel på ett tillförlitligt sätt, meddelar dig omedelbart och att det innehåller information som behövs för att diagnosticera problemet. Detta är exakt vad Application Insights gör.

### <a name="where-do-bugs-come-from"></a>Varifrån kommer buggar?
Fel i web system uppkommer vanligtvis konfigurationsproblem eller felaktig samverkan mellan många komponenter. Den första uppgiften när du hanterar en live-webbplatsen incident är därför att identifiera lokus problemets: vilken komponent eller relationen är orsaken?

Några av oss, de som har grå hår kan komma ihåg en enklare era som körde ett program i en dator. Utvecklare kan testa den noggrant innan du levererar. och har levererats, skulle sällan se eller Tänk på det igen. Användarna behöver föreligger med resterande buggar i många år. 

Saker är nu så mycket olika. Din app har en mängd olika enheter ska köras på och det kan vara svårt att garantera exakt samma beteende på var och en. Appar i molnet innebär buggar kan åtgärdas snabbt, men det innebär också kontinuerlig tävling och en förväntan av de nya funktionerna regelbundet. 

I dessa villkor är det enda sättet att behålla en fast kontroll på antalet bugg automatiserade Enhetstestning. Det skulle vara omöjligt att manuellt igen testa allt på varje leverans. Enhetstest är nu en vanliga del av skapandeprocessen. Till exempel Xamarin Test Cloud verktygen genom att tillhandahålla automatisk Användargränssnittet för testning på flera versioner av webbläsaren. Dessa testning systemen kan vi hoppas att frekvensen för buggar i en app kan hållas till ett minimum.

Typiskt webbprogram har många live komponenter. Förutom att klienten (i en webbläsare eller enhet app) och webbservern kan förväntas det vara betydande backend-bearbetning. Kanske är serverdelen en pipeline av komponenter eller en glesare samling samverkande delar. Och många av dem inte kontroll över – de är externa tjänster som du är beroende av.

Det kan vara svårt och uneconomical att testa för eller förutser, alla möjliga fellägen i det aktiva systemet själva i konfigurationer som dessa. 

### <a name="questions-"></a>Frågor...
Vissa frågor som ber vi när vi håller på att utveckla ett webbsystem:

* Min app kraschar? 
* Vad hände exakt? – Om den misslyckades en begäran, vill jag veta hur det kom dit. Vi behöver en spårning av händelser...
* Är tillräckligt snabbt min app? Hur lång tid tar det för att svara på vanliga begäranden?
* Servern kan hantera belastningen? När antalet begäranden som ökar svarstiden håller stadig?
* Hur responsiv är Mina beroenden - REST API: erna, databaser och andra komponenter som min app anropar. I synnerhet om systemet är långsamt, är det min komponent eller får jag långsamma svar från någon annan?
* Är min app upp eller ned? Kan den ses från hela världen? Meddela mig om stoppas...
* Vad är den grundläggande orsaken? Var felet i min komponent eller ett beroende? Är det ett kommunikationsproblem?
* Hur många användare påverkas? Om jag har fler än ett problem att hantera, vilket är den viktigaste?

## <a name="what-is-application-insights"></a>Vad är Application Insights?
![Grundläggande arbetsflöde för Application Insights](./media/devops/020.png)

1. Application Insights instrumenterar din app och skickar telemetri om den medan appen körs. Du kan skapa Application Insights SDK i appen, eller du kan använda instrumentation vid körning. Den tidigare metoden är mer flexibel, som du kan lägga till din egen telemetri till vanliga moduler.
2. Telemetri som skickas till Application Insights-portalen, där de lagras och bearbetas. (Du bör även om Application Insights finns i Microsoft Azure, men du kan övervaka alla webbappar – inte bara Azure-appar.)
3. Telemetri som visas för dig i form av diagram och tabeller för händelser.

Det finns två typer av telemetri: aggregerade och raw-instanser. 

* Instansdata innehåller till exempel en rapport med en begäran som har tagits emot av webbappen. Du kan hitta för och kontrollera informationen för en förfrågan med sökverktyget i Application Insights-portalen. Instansen omfattar data, till exempel hur lång tid tog att svara på begäran, samt den begärda URL: en, göra en uppskattning av platsen för klienten och andra data på din app.
* Sammanställda data innehåller antalet händelser per tidsenhet, så att du kan jämföra andelen begäranden med svarstiderna. Den innehåller också medelvärden över mått, till exempel svarstider för begäran.

Huvudkategorier av data är:

* Begäranden till din app (vanligtvis HTTP-begäranden), med data på URL: en, svarstid, och lyckats eller misslyckats.
* Beroenden - REST- och SQL-anrop gjorda av din app, även med URI, svarstider och lyckades
* Undantag, inklusive stackspårningar.
* Data om sidvisningar, som kommer från användarnas webbläsare.
* Mått, till exempel prestandaräknare, samt mått som du har skrivit själv. 
* Anpassade händelser som du kan använda för att spåra affärshändelser
* Loggspårningar som används för felsökning.

## <a name="case-study-real-madrid-fc"></a>Fallstudie: Real Madrid F.C.
Webbtjänst [Real Madrid Football Club](https://www.realmadrid.com/) fungerar ungefär 450 miljoner fans över hela världen. Fans komma åt det både via webbläsare och den Club mobila appar. Fans kan inte bara boken biljetter, men också komma åt information och video klipp på resultat, spelare och kommande spel. De kan söka med filter som antal mål poängsätts. Det finns också länkar till sociala medier. Användarupplevelsen är mycket personlig och är utformad som en dubbelriktad kommunikation för att engagera fans.

Lösningen [är ett system med tjänster och program på Microsoft Azure](https://www.microsoft.com/inculture/sports/real-madrid/). Skalbarhet är ett krav: trafik variabel och kan nå mycket stora volymer under och runt matchningar.

För riktiga Madrid, är det viktigt att övervaka systemets prestanda. Azure Application Insights erbjuder en omfattande vy över systemet, säkerställer en tillförlitlig och hög servicenivå. 

Club får du också djup förståelse av dess fans: där de är (endast 3% är i Spanien), vilka intresse som de har i spelare, historiska resultat och kommande spel och hur de svarar för att matcha resultat.

De flesta av den här telemetridata samlas in automatiskt utan att någon extra kod som förenklade lösningen och sänkte komplexiteten i driften.  För riktiga Madrid, Application Insights innehåller 3.8 miljarder telemetri punkter varje månad.

Real Madrid använder Power BI-modulen för att visa sina telemetri.

![Power BI-vy av Application Insights telemetry](./media/devops/080.png)

## <a name="smart-detection"></a>Smart identifiering
[Proaktiv diagnostik](../../azure-monitor/app/proactive-diagnostics.md) är en nyligen funktion. Utan någon specialkonfiguration av dig, Application Insights automatiskt identifierar och varnar dig om onormala ökningar i Felfrekvens i din app. Det är tillräckligt smarta för att ignorera en bakgrund för tillfälliga fel inträffar och ökar som är helt enkelt proportionell mot en ökning av begäranden. Till exempel fungerar om ett fel uppstår på något av de tjänster du lita på, eller om den nya bygger du precis har distribuerat inte så bra och du vet om det. när du tittar på din e-post. (Och det finns webhooks så att du kan utlösa andra appar.)

En annan aspekt av den här funktionen utför en daglig djupgående analys av din telemetri som söker efter ovanliga mönster för prestanda som är svåra att identifiera. Det kan till exempel hitta långsam prestanda som är associerat med ett visst geografiskt område eller med en viss webbläsare-version.

I båda fallen anger aviseringen inte bara symptomen den identifieras, men ger dig också data du behöver för att diagnostisera problem, till exempel relevant undantagsrapporter.

![E-post från proaktiv diagnostik](./media/devops/030.png)

Kunden Samtec SA: ”Under de senaste funktionen startpunkt påträffades en under skalade-databas som nått sin resursgränser och orsakar timeout. Proaktiv identifiering aviseringar Kom bokstavligen som vi sorterar problemet, mycket nära realtid som annonseras. Den här aviseringen i kombination med Azure-plattformen aviseringar hjälpte oss att nästan omedelbart åtgärda problemet. Total nedtid < 10 minuter ”.

## <a name="live-metrics-stream"></a>Live Metrics Stream
Distribuera den senaste versionen kan det vara en dig bekymrad upplevelse. Om det finns några problem, vill du veta om dem direkt, så att du kan säkerhetskopiera reda på om det behövs. Live Metrics Stream ger dig viktiga mått med en fördröjning på ca en sekund.

![Livemått](./media/devops/0040.png)

Och du kan inspektera omedelbart ett exempel på eventuella fel eller undantag.

![Live felhändelser](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>Programkarta
Programavbildning identifierar automatiskt programtopologin, om prestandainformation ovanpå den, så att du enkelt identifiera flaskhalsar i prestanda och problematiska flöden i miljön distribuerade. Det kan du identifiera programberoenden på Azure-tjänster. Du kan sortera problemet genom att förstå om det är kodrelaterade eller beroende relaterade och från en enda plats detaljerat relaterade diagnostik uppleva. Ditt program kan till exempel att misslyckas på grund av prestandaförsämring i SQL-nivå. Med programkartan kan du se den omedelbart och öka detaljnivån i SQL Index Advisor eller Frågeinsikter upplevelse.

![Programkarta](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
Med [Analytics](../../azure-monitor/app/analytics.md), du kan skriva godtyckliga frågor i ett kraftfullt SQL-liknande språk.  Det blir enkelt att diagnostisera i hela appen stack som ansluter olika perspektiv och du kan ställa rätt frågor och korrelera tjänstens prestanda med affärsmått och kundupplevelsen. 

Du kan fråga din telemetri-instans och mått rådata som lagras i portalen. Språket innehåller filter, koppling, sammanställning och andra åtgärder. Du kan beräkna fält och utföra statistiska analyser. Det finns både tabellmodeller och grafiska visualiseringar.

![Analytics-fråga och resultatet diagram](./media/devops/0025.png)

Till exempel är det enkelt att:

* Segmentera ditt programs begäran om prestandadata efter kund nivåerna för att förstå deras upplevelse.
* Sök efter specifika felkoder eller anpassad händelsenamn under live-webbplatsen undersökningar.
* Granska nedåt i appanvändningen av specifika kunder att förstå hur funktionerna har köpt och antagit.
* Spåra sessioner och svarstider för specifika användare att aktivera och team att ge support direkt.
* Fastställa app som används ofta funktioner för att besvara frågor för prioritering av funktionen.

Kunden DNN SA: ”Application Insights har gett oss med saknas del av formeln för att du att kombinera, sortera, fråga och filtrera data efter behov. Vilket gör att vårt team att använda sina egna ingenuity och erfarenhet för att hitta data med ett kraftfullt frågespråk kan vi att söka efter insikter och lösa problem visste vi vi hade. Mycket intressant svar kommer från frågor som börjar med *”jag wonder om...”.*”

## <a name="development-tools-integration"></a>Utveckling av verktyg för integrering
### <a name="configuring-application-insights"></a>Konfiguration av Application Insights
Visual Studio och Eclipse har verktyg för att konfigurera rätt SDK-paket för projektet som du utvecklar. Det finns ett kommando att lägga till Application Insights.

Om du använder en spårning loggningsramverk, till exempel Log4N, NLog eller System.Diagnostics.Trace, sedan får du möjlighet att skicka loggarna till Application Insights tillsammans med andra telemetri, så att du enkelt kan jämföra spårningarna med begäranden, beroende anrop och undantag.

### <a name="search-telemetry-in-visual-studio"></a>Sök telemetri i Visual Studio
När du utvecklar och felsöker en funktion, kan du visa och Sök telemetri direkt i Visual Studio med hjälp av samma search anläggningar som i webbportalen.

Och när Application Insights loggar ett undantag, du kan visa datapunkten i Visual Studio och hoppa direkt till den relevanta koden.

![Visual Studio-sökning](./media/devops/060.png)

Vid felsökning, har du alternativet att behålla telemetri på utvecklingsdatorn, visar den i Visual Studio, men utan att skicka den till portalen. Det här alternativet för lokal undviker blanda felsöka med telemetri för produktion.

### <a name="work-items"></a>Arbetsobjekt
När en avisering utlöses kan Application Insights automatiskt skapa ett arbetsobjekt i ditt arbete spårningssystemet.

## <a name="but-what-about"></a>Men vad gäller...?
* [Sekretess- och storage](../../azure-monitor/app/data-retention-privacy.md) -telemetrin sparas på Azure säkra servrar.
* Prestanda - effekten är mycket låg. Telemetri är batchar.
* [Priser](../../azure-monitor/app/pricing.md) – du kan komma igång utan kostnad och som fortsätter när du använder låg volym.


## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Det är enkelt att komma igång med Application Insights. De viktigaste alternativen är:

* [IIS-servrar](../../azure-monitor/app/monitor-performance-live-website-now.md), och även för [Azure App Service](../../azure-monitor/app/app-insights-overview.md).
* Instrumentera ditt projekt under utveckling. Du kan göra detta för [ASP.NET](../../azure-monitor/app/asp-net.md) eller [Java](../../azure-monitor/app/java-get-started.md) appar, samt [Node.js](../../azure-monitor/app/nodejs.md) och en mängd [andra typer](../../azure-monitor/app/platforms.md). 
* Instrumentera [en webbsida](../../azure-monitor/app/javascript.md) genom att lägga till ett kort kodfragment.

