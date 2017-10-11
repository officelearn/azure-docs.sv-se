---
title: "Cortana Intelligence lösning mallen Playbook för begäran Prognosticering energi | Microsoft Docs"
description: "En Lösningsmall med Microsoft Cortana Intelligence som hjälper till att begäran för ett företag för energiförbrukning för en prognos."
services: cortana-analytics
documentationcenter: 
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence lösning mallen Playbook för begäran Prognosticering energi
## <a name="executive-summary"></a>Sammanfattning
I de senaste åren har Sakernas Internet (IoT), alternativa energi och stordata samman för att skapa stora möjligheter för verktyget och energi domänen. Verktyget och hela energisektorn har sett förbrukning förenkla med konsumenter krävande bättre sätt att kontrollera användningen av energi på samma gång. Verktyget och smarta rutnätet företag är därför i stor måste förnya och förnya sig själva. Dessutom blir många kraft och utility rutnät inaktuella och dyra att underhålla och hantera. Teamet har arbetat på ett antal Användarsegmentet inom domänen energi under det senaste året. Vi har påträffat många fall där verktyg eller ISV: er (oberoende programvaruleverantörer) har söker i prognoser för framtida energiförbrukning under dessa åtaganden. Dessa prognoser spelar en viktig roll i verksamheten aktuella och framtida och har blivit grunden för olika användningsfall. Dessa inkluderar kortsiktiga och långsiktiga power belastningen prognos, handel, belastningsutjämning, rutnätet optimering osv. Stordata och avancerade analyser AA-metoder, till exempel Machine Learning (ML) är de viktiga aktiverarna för produktion korrekt och tillförlitlig prognoser.  

I den här playbook vi samlat affärs- och analytiska riktlinjer som behövs för en lyckad utveckling och distribution av energiförbrukning prognos lösning. Dessa föreslagna riktlinjer hjälper verktyg, dataanalytiker och data engineers vid upprättandet av fullständigt operationalized molnbaserade, begäran prognoser lösningar. För företag som precis har börjat sina stordata och avancerade analyser resa representera sådan lösning inledande startvärdet i sina långsiktig strategi för smart rutnätet.

> [!TIP]
> Du kan hämta ett diagram som ger en översikt över arkitekturen för den här mallen finns [Cortana Intelligence Lösningsmall arkitektur för begäran Prognosticering energi](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Översikt
Det här dokumentet beskriver företag, data och tekniska aspekter av med hjälp av Cortana Intelligence och i särskilda Azure Machine Learning (AML) för implementering och distribution av energi prognoser lösningar. Dokumentet består av tre delar:  

1. Förståelse för verksamheten  
2. Förstå data  
3. Teknisk implementering

Den **företag att förstå** del beskrivs business-aspekt måste du förstå och överväga innan investering beslut. Den förklarar hur att kvalificera affärsproblem till hands för att säkerställa att förutsägelseanalyser och maskininlärning är verkligen effektiva och tillämpliga. Dokumentet ytterligare förklarar grunderna i machine learning och hur den används för att åtgärda problem med energi prognoser. Det beskrivs kraven och kriteriet kriterier i ett användningsfall. Vissa exempel använda fall och företag fallet scenarier ingår också.

Data är den viktigaste komponenten för en lösning för maskininlärning. Den **Data förstå** en del av det här dokumentet beskriver vissa viktiga aspekter av data. Det visar vilken typ av data som behövs för energi prognoser data kvalitetskrav och vilka datakällor som vanligtvis finns. Vi förklarar också hur rådata för att förbereda datafunktioner som faktiskt enhet modellering del.

Den tredje delen av dokumentet innehåller de **teknisk implementering** aspekt av en lösning. Funktionen tekniker och modellering är kärnan i vetenskap av data och är därför som diskuteras i viss detalj. Den omfattar begreppet webbtjänster som är ett viktigt fordon för distribution av förutsägelseanalyslösningar. Vi beskriver också en typisk arkitektur operationalized slutpunkt till slutpunkt-lösning.

Dokumentet innehåller dessutom referensmaterialet som du kan använda för att få ytterligare förståelse för domän- och teknikbehov.

Det är viktigt att Observera att vi inte tänker i det här dokumentet beskriver djupare vetenskap av data, dess matematiska och tekniska aspekter. Dessa uppgifter finns i [Azure ML-dokumentationen](http://azure.microsoft.com/services/machine-learning/) och [bloggar](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Målgrupp
Målgruppen för det här dokumentet är både företag och tekniska personal som vill få kunskap och förståelse av Machine Learning-baserade lösningar och hur de används specifikt inom energi prognoser-domän.

Datavetare kan också dra nytta i det här dokumentet för att få en bättre förståelse för högnivåprocessen som enheter distributionen av en energi prognoser lösning. I den här kontexten den kan också användas för att upprätta en baslinje för bra och startpunkten för mer detaljerad och avancerade material.

### <a name="industry-trends"></a>Världen
I de senaste åren har IoT, alternativa energi och stordata samman för att skapa stora möjligheter för verktyget och energi utrymme. Verktyget och hela energi sektorer har sett förbrukning förenkla med konsumenter krävande bättre sätt att kontrollera användningen av energi på samma gång.

Många verktyg och smarta elbolag pioneering den [smart rutnätet](https://en.wikipedia.org/wiki/Smart_grid) genom att distribuera ett antal Använd fall som gör att användning av data som genereras av rutnätet. Många användningsområden omfångsfasen handlar om elektricitet produktion sina egenskaper: inte ackumulerade eller lagrade tagits ur bruk som inventering. Därför måste tillverkas är användas. Verktyg som vill bli mer effektiv måste prognos strömförbrukningen bara eftersom som ger dem större möjlighet att **balansera tillgång och efterfrågan**, vilket gör energi svinn **minska växthusgaser gas utsläpp**, och kontrollera kostnaden.

När man talar kostnader, finns det en annan viktig aspekt som är pris. Nya möjligheter för handel mellan verktyg har hämtat i en bra måste **göra prognoser för framtida behov och framtida pris elektricitet**. Detta kan hjälpa företag att fastställa sina produktionsvolymer.

När vi använder ordet ”smart' refererar vi faktiskt till ett rutnät som kan lära sig och sedan göra förutsägelser. Den kan förutsäga när ändringar i förbrukning samt **förutse tillfällig överbelastning situationer och justeras automatiskt för den**. Genom kontroll distans förbrukning (med hjälp av dessa smarta mätare), kan lokaliserade överlagring situationer hanteras. **Genom att förutsäga först och sedan fungerar**, rutnätet gör själva smartare över tid.

I resten av det här dokumentet fokuseras på en specifik familj av användningsområden som täcker Skapa prognoser för framtida, kortsiktiga och långsiktiga energiförbrukning. Vi har arbetat inom följande områden för några månader och har fått vissa kunskap och kunskaper som ger oss möjlighet att ge branschen klass resultat. Andra användningsfall beskrivs också i dokumentet inom en snar framtid.

## <a name="business-understanding"></a>Förståelse för verksamheten
### <a name="business-goals"></a>Affärsmål
Den **energi Demo** målet är att visa en typisk förutsägelseanalyser och maskininlärning lösning som kan distribueras i en mycket kort tidsperiod. Vår fokus är särskilt när du aktiverar energi begäran prognosen lösningar så att dess affärsvärde kan snabbt insåg och utnyttjas på. Med hjälp av informationen i det här playbook kan kunden uppnå följande mål:

* Kort tid att värdet för maskininlärning baserad lösning
* Möjligheten att expandera en pilot användningsfall till andra användningsfall eller en bredare omfattning baserat på deras affärsbehov
* Snabbt få Cortana Intelligence Suite produktinformation

Med dessa mål i åtanke syftar denna playbook till att leverera affärs- och teknisk kunskap som hjälper att uppnå dessa mål.

### <a name="power-load-and-demand-forecasting"></a>Power belastning och begäran prognoser
Inom sektorn energiförbrukning, kan det finnas tillgång och efterfrågan prognoser kan hjälpa dig med allvarliga problem på många olika sätt. I själva verket kan begäran prognoser ses grunden för många core användningsområden i branschen. I allmänhet Vi anser att två typer av energi begäran prognoser: kortsiktigt och långsiktigt. Var och en kan ett annat syfte och använda en annan metod. Den största skillnaden mellan två är prognosmodellen horizon, vilket innebär att tidsintervallet för när vi skulle göra prognoser som i framtiden.

#### <a name="short-term-load-forecasting"></a>Kort sikt belastningen prognoser
Inom ramen för energiförbrukning definieras kort sikt ladda prognoser (STLF) som aggregerade belastningen prognostiserat inom en nära framtid på olika delarna i rutnätet (eller rutnät som helhet). I den här kontexten definieras kortvarigt tidsrymd inom intervallet 1 timme till 24 timmar. I vissa fall kan också en horizon 48 timmar. Därför är STLF väldigt vanligt i en operativa användningsfall i rutnätet. Här följer några exempel på STLF drivs användningsområden:

* Tillgång och efterfrågan belastningsutjämning
* Stöd för energisparfunktioner handel
* Marknaden gör (inställningen power pris)
* Rutnätet operativa optimering
* [Svaret på begäran](https://en.wikipedia.org/wiki/Demand_response)
* Högsta begäran prognoser
* Hantering av efterfrågan på klientsidan
* Belastningsutjämning och överlagra förebyggande
* Långsiktigt belastningen prognoser
* Identifiering av fel och avvikelseidentifiering
* Utjämning av belastning/reducering 

STLF modellen främst baseras på nära tidigare (sista dag eller vecka) uppgifter och Använd prognostiserat temperatur som en viktig ge prognoser. Att hämta korrekt temperatur för nästa timma och upp till 24 timmar blir mindre en utmaning nu dagar. Dessa modeller är mindre känsliga för säsongsbaserade mönster eller trender för långsiktig användning.

SLTF lösningar sannolikt kommer att generera en stor volym med förutsägelse anrop (tjänstbegäranden) eftersom de är anropas timme och i vissa fall även med högre frekvens. Det är också mycket vanligt att implantation där varje enskild understation eller transformatorn representeras som en fristående modell och är därför volymen av begäranden för förutsägelse ännu större.

#### <a name="long-term-load-forecasting"></a>Långsiktigt belastningen prognoser
Målet på lång sikt belastningen prognoser (LTLF) är att förutsäga power begäran med en tidsrymd mellan 1 vecka till flera månader (och i vissa fall för ett antal år). Det här intervallet för horizon gäller främst för planering och investering användningsfall.

Beständigt scenarier är det viktigt att få hög kvalitet data som beskriver ett intervall av flera år (lägsta 3 år). Dessa modeller vanligtvis extraheras säsongsvärdet mönster från historiska data och använda externa predicators sådana som väder och klimatförändringar mönster.

Det är viktigt att klargöra att ju längre tid den prognosmodellen är, desto mindre exakt kanske prognosen. Det är därför viktigt att skapa vissa förtroende intervall tillsammans med faktiska prognosen som skulle låta människor att dela upp möjliga variationen i sina planeringsprocessen.

Eftersom förbrukning scenario för LTLF främst planering, kan vi räknar med mycket lägre förutsägelse volymer (jämfört med STLF). Vi skulle normalt finns dessa förutsägelser bädda in i visualiseringsverktyg som Excel eller PowerBI och skötas manuellt av användaren.

### <a name="short-term-vs-long-term-prediction"></a>Kort sikt vs. Långsiktigt förutsägelse
I följande tabell jämförs STLF och LTLF i fråga om de viktigaste attribut:

| Attribut | Kort sikt belastning vid en prognos | Långsiktigt belastningen prognos |
| --- | --- | --- |
| Prognos Horizon |Från 1 timme 48 timmar |Från 1 till 6 månader eller mer |
| Data granularitet |Varje timme |Varje timme eller varje dag |
| Vanliga användningsområden |<ul><li>/ Efterfrågan belastningsutjämning</li><li>Välj timme prognoser</li><li>Svaret på begäran</li></ul> |<ul><li>Långsiktigt planering</li><li>Rutnätet tillgångar planering</li><li>Resursplanering</li></ul> |
| Vanliga predictors |<ul><li>Dag eller vecka</li><li>Timme på dagen</li><li>Varje timme temperatur</li></ul> |<ul><li>Månad</li><li>Dagen i månaden</li><li>Långsiktigt temperatur- och klimatförändringar</li></ul> |
| Historiska dataintervall |Två till tre års data |Fem 10 års data |
| Vanliga noggrannhet |MAPE * 5% eller lägre |MAPE * 25% eller lägre |
| Prognosen frekvens |Varje timme eller var 24: e timme |Genereras när varje månad, kvartal eller år |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – innebär genomsnittlig procent fel

Det är mycket viktigt att skilja mellan kort och lång sikt prognoser scenarier då dessa representerar olika affärsbehov och kan ha olika distribution och användningsmönster som kan ses från den här tabellen.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Exempel användningsfall 1: eSmart system – överlagring optimering
En viktig roll i en [smart rutnätet](https://en.wikipedia.org/wiki/Smart_grid) är att dynamiskt ständigt optimera och justera för ändra förbrukning mönster. Strömförbrukningen kan påverkas av kortsiktig ändringar som beror huvudsakligen på temperatur variationer (*t.ex.*, mer kraft används för luften villkors- eller uppvärmning). På samma gång påverkas också strömförbrukningen av långsiktiga trender. Dessa kan innehålla säsongsvärdet effekter, helgdagar, långsiktig förbrukning tillväxt och även ekonomiska faktorer, till exempel konsumenten index, olja pris och BNP.

I det här fallet används [eSmart](http://www.esmartsystems.com/) ville distribuera en molnbaserad lösning som gör att förutsäga benägenheten av en överlagring på alla angivna understation i rutnätet. I synnerhet ville eSmart identifiera omformarstationer som sannolikt kommer att överlagra inom nästa timma, så en omedelbar åtgärd kan vidtas för att undvika eller lösa denna situation.

En korrekt och snabb göra förutsägelse kräver implementering av tre förutsägelsemodeller:

* Lång sikt modellen som gör det möjligt att skapa prognoser för strömförbrukningen på varje understation under nästa några veckor eller månader
* Kort sikt modellen som gör att förutsägelser av överlagring situationen på en viss understation under nästa timma
* Temperatur-modell som ger prognoser för framtida temperatur över flera scenarier

Syftet med den långsiktiga modellen är att rangordnas omformarstationer av sina benägenheten för överlagring (beroende på deras Strömkapaciteten överföring) under nästa vecka eller månad. Detta gör att skapa en kort lista med omformarstationer som skulle användas som indata för kortsiktig förutsägelse. Temperatur är ett viktigt ge prognoser för långsiktig modellen, krävs en ständigt producera flera scenariot temperatur prognoser och feed dem som indata till långsiktig modellen. Kort sikt prognosen anropas sedan för att förutsäga vilken understation sannolikt kommer att överlagra under nästa timma.

Kort och lång sikt modeller distribueras separat per varje understation. Praktiska körningen av dessa modeller kräver därför omfattande orchestration. En mer detaljerad modell är dedikerad för varje timme på dagen för att få högre förutsägelsefunktionen på kort sikt. Dessa modeller utförs varje timme och Slutför körning inom några minuter så att tillräckligt länge för att svara och vidta förebyggande åtgärder om det behövs. Den här samlingen av modeller hålls uppdaterad med periodiska omtränings den senaste informationen.

Mer information om den här användningsfall hittar [här](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Använd Case kriterier – krav
Huvudsakliga styrkan hos Cortana Intelligence har kraftfulla möjligheten att distribuera och skalanpassa machine learning central lösningar. Det är utformat för att stödja tusentalsavgränsare förutsägelser som körs samtidigt. Det kan automatiskt anpassas efter ett ändra förbrukning mönster. Det är därför en lösning fokus på beräkningar prestanda. Till exempel är ett verktyg för företag intresserad av att producera korrekta energiförbrukning för nästa timma och för varje timme på dagen. Å andra sidan vi är mindre intresserade besvaras av varför efterfrågan prognoser att vara som den är (modellen själva ta hand om).

Det är därför viktigt att tänka på att inte alla användningsfall och företagsproblem kan lösas effektivt använder maskininlärning.

Cortana Intelligence och maskininlärning kan vara mycket effektivt lösa affärsproblem angivna när följande villkor är uppfyllda:

* Problem i verksamheten i hand är **förutsägande** till sin natur. En förutsägbar användning case exempel är ett verktyg för företag som vill förutsäga power belastningen på en viss understation under nästa timma. Å andra sidan, analysera och betygsätta drivrutiner till historiska begäran skulle vara **beskrivande** natur och därför mindre tillämplig.
* Det finns en Rensa **sökvägen för åtgärden** som ska vidtas när förutsägelser är tillgänglig. Till exempel kan att förutsäga en överlagring på en understation under nästa timma utlösa en proaktiv åtgärd för att minska belastning som är associerad med den understation och vilket potentiellt gör en överlagring.
* Användningsfallet representerar en **vanliga typ av problem** så att när löst den kan kunna få till följd att lösa andra liknande användningsfall.
* Kunden kan ange **kvantitativa och kvalitativa mål** att visa en lyckad lösningsimplementering. Till exempel ett bra kvantitativt mål för energi begäran prognos skulle vara nödvändiga noggrannhet tröskelvärdet (*t.ex.*, upp till 5% fel tillåts) eller när förutsäga understation överlagra sedan precision (antal true positiva identifieringar) och återkalla (omfattningen av SANT positiva identifieringar) ska vara överskrider ett visst tröskelvärde. Dessa mål måste härledas från kundens affärsmål.
* Det finns en Rensa **integrering scenariot** med företagets business arbetsflöde. Till exempel kan understation belastningen prognosen integreras i rutnätet kontrollcenter att överlagring förebyggande aktiviteter.
* Kunden har klart att använda **data med tillräckligt hög kvalitet** att stödja användningsfallet (finns i nästa avsnitt, **Data Quality**, för den här playbook).
* Kunden omfattar cloud central data arkitektur eller **molnbaserade maskininlärning**, inklusive Azure ML och andra komponenter i Cortana Intelligence Suite.
* Kunden är villigt att upprätta **en slutpunkt till slutpunkt dataflöde** som verksamhet distribution av data i molnet på kontinuerligt och är villigt att **operationalisera** lösningen.
* Kunden är redo att **dedikerar resurser** som ska vara aktivt ägnar åt vid den första pilotprojekt implementeringen så att kunskap och ägarskap för lösningen kan överföras till kunden vid slutförande.
* Kunden resursen ska vara en **skicklig data professional**, helst en data-forskare.

Kriteriet av användningsfall baserat på ovanstående villkor kan kraftigt förbättra slutförandefrekvenser för användningsfall och upprätta en bra beachhead för implementeringen av framtida användningsområden.

### <a name="cloud-based-solutions"></a>Molnbaserade lösningar
Cortana Intelligence Suite i Azure är en integrerad miljö som finns i molnet. Distributionen av en lösning för avancerade analyser i en molnmiljö innehåller betydande fördelar för företag och samtidigt kan innebära stor förändring för företag att fortfarande använda lokala IT-lösningar. Inom sektorn energiförbrukning finns en tydlig trend över stegvis migrering av åtgärder i molnet. Denna trend går hand i hand tillsammans med utvecklingen av smart rutnätet som beskrivs ovan, i **världen**. Eftersom den här playbook fokuserar på en molnbaserad lösning i domänen energiförbrukning, är det viktigt att förklarar fördelarna och andra överväganden för att distribuera en molnbaserad lösning.

Kanske är den största fördelen med en molnbaserad lösning kostnaden. Som en lösning använder molnet distribuerade komponenter, det finns ingen direktkostnader eller kostnad för sålda varor (kostnader för sålda varor) Komponentkostnader som är kopplade till den. Det innebär att det behövs ingen ska investera i maskinvara, programvara och IT-underhåll och det finns därför en betydande minskning av företag risk.

En annan viktig fördel är strukturen betalning per användning kostnaden för molnbaserade lösningar. Molnbaserad servrar för datoranvändning eller lagring kan distribueras och skalas på en just-efter behov. Detta motsvarar kostnaden effektivitet fördelen med en molnbaserad lösning.

Slutligen behövs det ingen investera i IT-underhåll eller för framtida infrastrukturutveckling, eftersom detta är en del av molnbaserat erbjudande. I den utsträckning Cortana Intelligence Suite innehåller bäst i klassen services och dess översikt över håller utvecklas. Nya funktioner, komponenter och funktioner introduceras hela tiden och utvecklas.

För ett företag som precis har startats dess övergång till molnet, rekommenderar vi starkt för att vidta en stegvis metod genom att implementera ett moln migrering översikt över. Vi tror att användningsområden som beskrivs i den här playbook för verktyg och företag i domänen energi representerar en utmärkt möjlighet för testkörning förutsägelseanalyslösningar i molnet.

#### <a name="business-case-justification-considerations"></a>Business-Case motivering överväganden
Kunden kan vara intresserad av att göra en motivering för ett givet användningsfall där en molnbaserad lösning och Machine Learning är viktiga komponenter i många fall. Till skillnad från en lokal lösning för en molnbaserad lösning komponenten summa kostnaden är minimal och de flesta element kostnader som är associerade med faktiska användningen. När det gäller att distribuera en energi prognoser lösning på Cortana Intelligence Suite kan flera tjänster integreras med en enda gemensam kostnadsstruktur. Exempelvis databaser (*t.ex.*, SQL Azure) kan användas för lagring av rådata och för den faktiska införs Azure ML används som värd för prognosmodellen tjänsterna. I det här exemplet kan kostnaden strukturen omfatta lagring och komponenter.

Å andra sidan bör ett ha en god förståelse av affärsvärde drift ett energiförbrukning prognoser (kort eller lång sikt). I själva verket är det viktigt att Observera värdet av varje prognosen åtgärd. Till exempel korrekt Prognosticering power belastningen för det närmaste dygnet kan förhindra overproduction eller kan förhindra överlagringar i rutnätet och det kan vara möjligt vad gäller finansiella besparingar dagligen.

En grundläggande formel för beräkning av ekonomiska fördelen att begäran prognos lösning är: ![grundläggande formel för beräkning av ekonomiska fördelen att begäran prognos lösning](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Eftersom Cortana Intelligence Suite ger en betalning per användning prismodellen så behövs det ingen medför en fast kostnadskomponent på den här formeln. Den här formeln kan beräknas för varje dag, varje månad eller årlig.

Aktuella Cortana Intelligence Suite och Azure ML prissättning finns [här](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Lösning utvecklingsprocessen
Utvecklingscykel för av en energiförbrukning prognoser lösningen innebär vanligtvis 4 faser, som vi göra utnyttjar molnbaserad teknik och tjänster i Cortana Intelligence Suite.

Detta illustreras i följande diagram:

![Smart rutnätet cykel](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Följande punkt beskrivs processen steg 4:

1. **Datainsamling** – alla avancerade analyser baserade lösningen är beroende av data (se **Data förstå**). Mer specifikt när det gäller att förutsägelseanalyser och skapa prognoser vi förlitar sig på pågående, dynamiska flödet av data. När det gäller energi prognoser för begäran, dessa data kan hämtas direkt från smart mätare eller aggregeras redan på en lokal databas. Vi använder sig också av andra externa datakällor, till exempel väder och temperatur. Den här pågående flödet av data måste styrd, schemalagd och lagras. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADM) är vår huvudsakliga bestämmer hög grad för att utföra den här uppgiften.
2. **Modeling** – för korrekt och tillförlitlig energi prognoser en utveckla (tåg) och upprätthålla en bra modell som gör använder av historiska data och extraherar meningsfull och förutsägbara mönster i data. Området för Machine Learning (ML) har växt snabbt med mer avancerade algoritmer utvecklas regelbundet. Azure ML Studio ger en bättre användarupplevelse som hjälper till att använda de mest avancerade algoritmerna ML inom en fullständig arbetsflöde. Det här arbetsflödet illustreras i en intuitiv flödesdiagram och innehåller förberedelse av data, funktionen extrahering, modellering och utvärdering av modellen. Användaren kan dra på hundratals olika modeller som ingår i den här miljön. I slutet av den här fasen har en data-forskare en aktiv-modell som har utvärderats och är klar för distribution.
   
   Följande diagram är en illustration av ett vanligt arbetsflöde:
   
   ![Modellera arbetsflödet](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Distribution** – med en aktiv-modell i nästa steg är distributionen. Här omvandlas modellen till en webbtjänst som Exponerar en RESTful-API som kan anropas samtidigt från olika förbrukning klienter via Internet. Azure ML ger ett enkelt sätt att distribuera en modell direkt från Azure ML Studio med ett enda klick på en knapp. Hela distributionsprocessen sker under huven. Den här lösningen kan anpassas automatiskt för att uppfylla nödvändiga förbrukningen.
4. **Förbrukning** – i det här steget ska vi faktiskt göra för att producera förutsägelser i prognosmodellen. Förbrukningen kan köras från en användarprogram (*t.ex.*, instrumentpanel) eller direkt från ett system som fungerar som/efterfrågan NLB system eller en lösning för optimering av rutnätet. Flera användningsområden kan köras från en modell.

## <a name="data-understanding"></a>Förstå data
Efter täcker business överväganden (se **företag att förstå**) en efterfrågan på energi prognoser lösning, vi är nu redo att diskutera datadel. Alla förutsägelseanalys är beroende av tillförlitliga data. För energi prognoser för begäran, vi förlitar sig på av historiska förbrukningsdata med olika detaljnivåer. Den historiska data används som raw materialet. Den kommer att göras en noggrann analys där data forskare identifierar predictors (kallas även funktioner) som kan placeras i en modell som slutligen genererar krävs prognoser.

I resten av det här avsnittet beskriver vi olika åtgärder och överväganden för att förstå data och hur du gör ett användbart format.

### <a name="the-model-development-cycle"></a>Utvecklingscykeln modellen
Produktion av bra prognoser modeller kräver vissa förberedelser för noggrann planering och. Bryta ned modelleringsprocessen i flera steg och fokuserar på ett steg i taget kan kraftigt förbättra resultatet av hela processen.

Följande diagram illustrerar hur modelleringsprocessen kan delas upp i flera steg:

![Modellen utvecklingscykeln](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Som framgår cykeln består av sex steg:

* Problemet formulering
* Datapåfyllning och datagranskning
* Förberedelse av data och funktionen konstruktion
* Modellering
* Modellen utvärdering
* Utveckling

I resten av det här avsnittet beskriver vi enskilda stegen och sakerna att tänka på vid varje steg.

### <a name="problem-formulation"></a>Problemet formulering
Vi kan du överväga att problemet formulering som mest kritiska steg måste du vidta innan du implementerar en förutsägelseanalys. Vi skulle här transformeringen problem i verksamheten och dela upp det specifika element som kan lösas med hjälp av data och modellering tekniker. Det är en bra idé att formulera problemet som en uppsättning frågor som vi vill att svara. Här följer några möjliga frågor som kan användas inom omfånget för energi begäran prognoser:

* Vad är den förväntade belastningen på en enskild understation nästa timmar eller dagar?
* Vid vilken tidpunkt på dagen min rutnät får belastning begäran?
* Hur troligt är min rutnätet för att hantera förväntad belastning?
* Hur mycket energi bör power station generera under varje timme på dagen?

Utformningen av dessa frågor kan vi fokusera på att få rätt data och implementera en lösning som är fullständigt justerad med affärsproblem till hands. Dessutom kan vi ange vissa viktiga mått som ger oss möjlighet att utvärdera modellen. Till exempel hur exakt prognosen ska och vad är intervallet för fel som är godkända av företaget?

### <a name="data-sources"></a>Datakällor
Moderna smart rutnätet samlar in data från olika delar och komponenter i rutnätet. Dessa data representerar olika aspekter av åtgärden och användning av power rutnätet. Vi är att begränsa diskussion om datakällor som motsvarar den faktiska begärd förbrukningen inom omfånget för energiförbrukning prognos. En viktig källa för energiförbrukning är smart mätare. Verktyg över hela världen distribuerar snabbt smart mätare för sina kunder. Smart mätare registrera den faktiska strömförbrukningen och ständigt relä informationen tillbaka till verktyget företaget. Data samlas in och skickas tillbaka på ett fast intervall mellan var 5: e minut och 1 timme. Mer avancerade smart mätare kan via fjärranslutning vara programmerad att styra och balansera faktisk förbrukning inom ett hushåll. Smart mätaren data är relativt tillförlitlig och innehåller en tidsstämpel. Det gör en viktig beståndsdel för begäran vid en prognos. Mätaren data kan sammanställas (samlade in) på olika nivåer i rutnätet topologin: transformatorn, understation, region, *etc*. Vi kan sedan välja nivån aggregering krävs att skapa en prognosmodell för den. Till exempel om verktyget företaget vill göra prognoser för framtida belastningen på var och en av dess rutnätet omformarstationer kan sedan alla mätare data därefter för varje enskild understation och används som indata för prognosmodellen. Vi kallar smart mätare en intern datakälla.

En tillförlitlig energi begäran prognos förlitar sig även på andra externa datakällor. En viktig faktor som påverkar strömförbrukningen är väder eller mer exakt temperatur. Historiska data visar stark korrelation mellan utanför temperatur- och strömförbrukning. Under varm sommar dagar användare se sina luftkonditionering och under vinter power på systemet. En pålitlig källa för historiska temperaturer på rutnätet plats är därför nyckel. Dessutom vi också förlitar sig på rättvisande prognos av temperatur som en ge prognoser energiförbrukningen.

Andra externa datakällor kan också skapa energi begäran prognosmodeller. Dessa kan innehålla långsiktiga klimatförändringar ändringar, ekonomiska index (*t.ex.*, BNP), med mera. I det här dokumentet innehåller vi inte dessa andra datakällor.

### <a name="data-structure"></a>Datastruktur
När du har identifierat de nödvändiga datakällorna vill vi se till att rådata som har samlats in innehåller korrekta data-funktioner. För att skapa en tillförlitlig begäran prognosmodell behöver vi säkerställa att insamlade data innehåller dataelement som kan hjälpa dig att förutsäga framtida behovet. Här följer några grundläggande kraven datastruktur (schema) av rådata.

Rådata består av rader och kolumner. Varje mått representeras som en enda rad med data. Varje rad med data innehåller flera kolumner (även kallat funktioner eller fält).

1. **Tidsstämpeln** – tidsstämpelfältet motsvarar den faktiska tiden när mätningen registrerades. Det måste uppfylla något av de vanliga datum/tid-format. Både datum och tid delar ska tas med. I de flesta fall behövs det ingen tid registreras till andra nivå av granularitet. Det är viktigt att ange den tidszon som data registreras.
2. **Läsa ID** -fältet identifierar mätaren eller mätning enheten. Det är en kategoriska variabel och kan vara en kombination av siffror och tecken.
3. **Värdet för förbrukning** – detta är den faktiska förbrukningen på ett angivet datum/tid. Förbrukningen kan mätas i kWh (kilowatt-hour) eller andra önskade enheter. Det är viktigt att notera att måttenheten måste vara konsekvent över alla mått i data. I vissa fall kan förbrukning gav över 3 power faser. I så fall behöver vi samla in alla oberoende förbrukning faser.
4. **Temperatur** – temperaturen vanligtvis samlas in från en oberoende källa. Det bör dock vara kompatibel med förbrukningsdata. Den ska innehålla en tidsstämpel som beskrivs ovan som gör att den synkroniseras med faktisk förbrukningsdata. Temperatur-värdet kan anges i grader eller Fahrenheit men ska vara konsekvent över alla mått.
5. **Plats –** Platsfältet är vanligtvis kopplad till den plats där temperatur data har samlats in. Det kan representeras som en zip-nummer eller latituden/longitud (lat/långt)-format.

Följande tabeller visar exempel på ett bra förbrukning och temperatur dataformat:

| **Datum** | **Tid** | **Mätaren ID** | **Fas 1** | **Fas 2** | **Fas 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Datum** | **Tid** | **Plats** | **Temperatur** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Som visas ovan, innehåller det här exemplet 3 olika värden för användning som är associerade med 3 power faser. Observera också att datum och tid fälten avgränsas, men de kan också slås samman till en kolumn. I det här fallet representeras kolumnen plats i formatet 5-siffriga postnummer och temperatur i grader Celsius format.

### <a name="data-format"></a>Dataformatet
Cortana Intelligence Suite stöder de vanligaste dataformat som CSV, TVS, JSON, *etc*. Det är viktigt att dataformatet förblir konsekventa för hela livscykeln för projektet.

### <a name="data-ingestion"></a>Datainhämtning
Eftersom energi begäran prognos är kontinuerligt och ofta förutsade, måste vi Kontrollera att rådata flödar med hjälp av en heltäckande och tillförlitlig införandet av data. Införandet processen måste garantera att rådata är tillgänglig för prognosmodellen processen vid den här tiden. Innebär att data införandet frekvensen måste vara större än frekvensen prognosmodellen.

Exempel: om våra begäran prognoser lösning skulle Generera en ny prognos på 8:00:00 dagligen så vi måste se till att alla data som har samlats in under de senaste 24 timmarna har fullständigt inhämtas till den tidpunkten och måste även inkludera den senaste timmen av data.

För att kunna göra detta, erbjuder Cortana Intelligence Suite olika sätt att stödja en tillförlitlig införandet av data. Detta diskuteras ytterligare i den **distribution** i det här dokumentet.

### <a name="data-quality"></a>Data Quality
Rådata datakällan som krävs för att utföra prognoser tillförlitliga och korrekta begäran måste uppfylla vissa grundläggande Datakriterier. Även om avancerade statistiska metoder kan användas för att kompensera för vissa möjliga data quality problemet, måste vi se till att vi passerande vissa grundläggande data quality tröskelvärde när du vill föra in nya data. Här följer några överväganden om rådata kvalitet:

* **Värde saknas** – avser situationen när specifikt mått inte samlades in. Det grundläggande kravet är att frekvensen saknas värdet inte får vara större än 10% för en viss tidsperiod. I fall att ett värde saknas det bör anges med ett fördefinierat (till exempel: '9999') och inte '0' som kan vara ett giltigt mått.
* **Mätningsnoggrannhet** – det faktiska värdet för förbrukning eller temperatur redovisas korrekt. Felaktiga mätningar genererar felaktiga prognoser. Normalt måste mätning felet vara lägre än 1% i förhållande till värdet true.
* **Tid för mätning** – det krävs att faktiska tidsstämpel för data som samlas in kommer inte avvika med mer än 10 sekunder i förhållande till true tiden för den verkliga mätningen.
* **Synkronisering** – när flera datakällor som används (*t.ex.*, förbrukning och temperatur) vi måste se till att det inte finns några tidssynkronisering problem mellan dem. Det innebär att tidsskillnaden mellan insamlade tidsstämpeln från alla två oberoende datakällor inte får överstiga mer än 10 sekunder.
* **Latens** – som beskrivs ovan, i **Datapåfyllning**, vi är beroende av en tillförlitlig trafikflöde och införandet av data. Att kontrollera att vi måste vi kontrollera data svarstiden. Det har angetts som tidsskillnaden mellan den tid som den verkliga mätningen togs och den tidpunkt då det har lästs in i Cortana Intelligence Suite lagring och är redo för användning. För kortsiktigt prognoser den totala svarstiden får inte vara större än 30 minuter. För lång sikt prognoser den totala svarstiden får inte vara större än 1 dag.

### <a name="data-preparation-and-feature-engineering"></a>Förberedelse av data och funktionen konstruktion
När rådata har tagits inhämtas (se **Datapåfyllning**) och har på ett säkert sätt lagras, är den redo att bearbetas. Förberedelsefasen data i grunden tar rådata och konvertera (omvandla, är Omforma) till ett formulär för modellering fasen. Som kan innehålla enkla åtgärder som att använda kolumnen rådata som med dess faktiska mätvärdet, standardiserade värden, mer komplexa åtgärder som [tid eftersläpande](https://en.wikipedia.org/wiki/Lag_operator), med mera. Den nyligen skapade kolumner kallas datafunktioner och att generera dessa kallas funktionen tekniker. I slutet av den här processen har vi en ny datauppsättning som erhållits från rådata och kan användas för förutsägelsemodellering. Dessutom förberedelsefasen data behöver ta hand om saknade värden (se **Data Quality**) och kompensera för dem. I vissa fall kan vi också behöva normalisera data för att säkerställa att alla värden som representeras i samma skala.

I det här avsnittet vi listas några av de vanliga data-funktionerna som ingår i energin prognosmodeller begäran.

**Tid som drivs funktioner:** funktionerna härleds från datum/tidsstämpel. Dessa extraheras och konverteras till kategoriska funktioner som:

* Tid på dagen – detta är timme på dagen som tar värden från 0 till 23
* Dag i veckan – detta representerar dag i veckan och tar värden från 1 (söndag) till 7 (lördag)
* Dag i månaden – detta motsvarar det faktiska datumet och kan ta värden mellan 1 och 31
* Månaden på året – detta motsvarar månaden och det tar värden från 1 (januari) och 12 (December)
* Lördag – detta är ett binärt värde funktion som tar värdet 0 för veckodagar eller 1 för lördag
* Helgdag - detta är ett binärt värde funktion som tar värdet 0 för en vanlig dag eller 1 för en helgdag
* Fourier – Fourier villkoren är vikterna härleds från tidstämpeln som används för att avbilda säsongsvärdet (cycles) i data. Eftersom vi kan ha flera säsonger på våra data kan vi behöver flera Fourier termer. Begäran värden kan till exempel ha årlig, varje vecka och dag säsonger/cykler vilket leder till 3 Fourier villkoren.

**Oberoende mätning funktioner:** oberoende funktioner omfattar alla dataelement som vi vill använda som predictors i vår modell. Här exkludera vi funktionen beroende som vi vill förutsäga.

* Funktionen lag – dessa är flyttat tid värdena för den faktiska efterfrågan. Fördröjning 1-funktioner ska innehålla värdet för begäran i föregående Timma (förutsatt att per timme) i förhållande till aktuell tidsstämpel. På liknande sätt kan vi kan lägga till fördröjning 2, lag 3, *etc*. Under fasen modellering bestäms faktiska kombinationen av fördröjning funktioner som används av utvärdering av modellen resultaten.
* Långsiktigt trender – den här funktionen representerar en linjär ökning efterfrågan mellan år.

**Funktionen för beroende:** funktionen beroende är datakolumnen som vi vill gärna vår modell ska förutsäga. Med [övervakad maskininlärning](https://en.wikipedia.org/wiki/Supervised_learning), vi måste först träna modellen med beroende funktioner (som även kallas etiketter). Detta gör att modellen för att lära dig mönster i data som är associerade med funktionen beroende. Energiförbrukning prognos vi vanligtvis vill förutsäga den faktiska begäran och därför kan vi använda den som funktionen beroende.

**Hantering av värden som saknas:** under förberedelsefasen data som behöver avgöra den bästa strategin för att hantera värden som saknas. Detta görs med hjälp av olika statistiska främst [uppräkning datametoder](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). När det gäller energi prognoser för begäran, vi vanligtvis sedan imputera saknade värden med hjälp av glidande medelvärde från föregående tillgängliga datapunkter.

**Databasnormalisering:** databasnormalisering är en annan typ av transformation som används för att sätta alla numeriska data, till exempel begäran prognos i liknande omfattning. Detta hjälper vanligtvis det förbättra modellen precision. Vi vanligtvis gör du genom att dividera det faktiska värdet med en uppsättning data.
Det ursprungliga värdet skalar till ett mindre intervall, vanligtvis mellan 1 och 1.

## <a name="modeling"></a>Modellering
Fasen modellering är om konvertering av data till en modell äger rum. Är i grunden i den här processen det avancerade algoritmer som skanna historiska data (utbildningsdata), extrahera mönster och skapa en modell. Att modellen kan senare användas för att förutsäga på nya data som inte har använts för att skapa modellen.

När vi har en fungerande tillförlitlig modell vi kan sedan använda den för att samla in nya data som är strukturerade för att inkludera de nödvändiga funktionerna (X). Användning av beständiga modellen (objekt från fasen utbildning) och förutsäga en målvariabel som markerats med Ŷ gör bedömningsprofil processen.

### <a name="demand-forecasting-modeling-techniques"></a>Begäran prognoser Modeling tekniker
Om begäran prognoser vi göra användning av historiska data som är sorterade efter tid. Vi vanligtvis refererar till data som innehåller time-dimension som [time series](https://en.wikipedia.org/wiki/Time_series). Målet i tid serien modellering är att hitta tid relaterade trender, säsongsvärdet, automatisk korrelation (korrelation över tid) och formulera de till en modell.

Under de senaste åren har avancerade algoritmer utvecklats för tid serien prognoser och prognosmodellen noggrannhet kan förbättras. Kort diskuterar vi några av dem här.

> [!NOTE]
> Det här avsnittet är inte avsedd att användas som en machine learning och skapa prognoser översikt utan istället som en kort undersökning av modeling tekniker som ofta används för begäran Prognosticering. Mer information och utbildningsmaterial om tid serien prognoser rekommenderar vi starkt online book [Prognosticering: principer och praxis](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (glidande medelvärde)**](https://www.otexts.org/fpp/6/2)
Glidande medelvärde är en av de första analytiska tekniker som har använts för tid serien Prognosticering och det är en av mest vanliga tekniker per dag. Det är också grunden för mer avancerade prognoser tekniker. Med glidande medelvärde vi prognoser nästa datapunkt som medelvärdet under de senaste punkterna K, där K anger ordningen för glidande medelvärde.

Glidande medelvärde tekniken innebär Utjämning prognosen och därför inte kan hantera även stora volatil i data.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (exponentiell utjämning)**](https://www.otexts.org/fpp/7/5)
Exponentiell utjämning ETS är en familj av olika metoder som använder viktat medelvärde för senaste datapunkter för att förutsäga nästa datapunkten. Tanken är att tilldela högre vikter till nyare värden och minska gradvis denna vikt för äldre uppmätta värden. Det finns ett antal olika metoder med den här serien, dem bland hantering av säsongsvärdet i data som [Holt somrar vad gäller såväl när metoden](https://www.otexts.org/fpp/7/5).

Vissa av dessa metoder även ta hänsyn till säsongsvärdet av data.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automatisk Regression integrerat glidande medelvärde)**](https://www.otexts.org/fpp/8)
Automatisk Regression integrerad glidande medelvärde (ARIMA) är en annan familj av metoder som används för tidsserier prognoser. Det kombinerar praktiskt taget automatiskt regression metoder med glidande medelvärde. Automatisk regression metoder Använd regression modeller med tidigare time series-värden för att beräkna nästa datum punkt. ARIMA metoder kan du också använda differentierande metoder som omfattar att skillnaden mellan datapunkter och använda dem i stället för det ursprungliga uppmätta värdet. Slutligen ARIMA också använder glidande medelvärde tekniker som beskrivs ovan. Kombinationen av alla dessa metoder på olika sätt är vad konstruktioner familj av ARIMA-metoder.

ETS och ARIMA används brett idag för energi begäran prognoser och många andra prognosmodellen problem. I många fall dessa kombineras tillsammans för att leverera mycket korrekta resultat.

**Allmän multipel Regression** Regression modeller kan vara den viktigaste modellering-metoden i domänen för machine learning och statistik. I samband med tidsserier vi använder regression för att förutsäga framtida värden (*t.ex.*, på begäran). I regression vi tar en linjär kombination av predictors och Läs vikterna (kallas även koefficienter) av dessa predictors under utbildning. Målet är att skapa en regressionslinjen som kommer prognos våra förutsägelsevärdet. Regression metoder är lämplig när en målvariabel är numeriska och därför passar också tid serien prognoser. Det finns en mängd olika metoder för regression inklusive väldigt enkelt regression modeller som [linjär Regression](https://en.wikipedia.org/wiki/Linear_regression) och mer avancerade de som till exempel beslutsträd, [slumpmässiga skogar](https://en.wikipedia.org/wiki/Random_forest), [Neurala nätverk](https://en.wikipedia.org/wiki/Artificial_neural_network), och förstärkta beslutsträd.

Konstruera energiförbrukning prognoser som regression problem ger oss en stor flexibilitet vid val av våra datafunktioner som kan kombineras från den faktiska begäran tid series-data och externa faktorer, till exempel temperatur. Mer information om de valda funktionerna beskrivs i funktionen Engineering (se **förberedelse av Data och funktionen Engineering**) avsnitt i den här playbook.

Från våra erfarenhet av implementering och distribution av energi begäran prognoser pilot vi har hittat som avancerade regression modeller som är tillgängliga i Azure ML tenderar att ge bästa resultat och vi använder dem.

## <a name="model-evaluation"></a>Modellen utvärdering
Utvärdering av modellen har en viktig roll i den **modellen utvecklingscykeln**. I det här steget ser vi till att verifiering av modellen och dess prestanda med verkliga data. Under steget modellering använder vi en del av tillgängliga data för att träna modellen. Under utvärderingsfasen vidtar vi resten av data till testa modellen. Praktiskt taget innebär det att vi mata modellen nya data som har varit omstruktureras och innehåller samma funktioner som utbildning dataset. Dock under verifieringsprocessen använder vi modellen att förutsäga målet variabeln i stället för att ange variabeln tillgängliga målservrar. Vi refererar ofta till den här processen som poängsättning av modellen. Vi skulle sedan true målvärden och jämför dem med de förväntade. Avsikten är att mäta och minimera förutsägelse fel, vilket innebär att skillnaden mellan förutsägelser och värdet true. Kvantifiera fel måttet är nyckeln eftersom vi vill finjustera modellen och validera om felet faktiskt minskar. Finjustera modellen kan göras genom att ändra Modellparametrar som styr hur learning eller genom att lägga till eller ta bort funktioner (kallas [parametrar Svep](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praktiskt taget som innebär att vi kan behöva upprepa funktionen konstruktion, modellering, och modell utvärdering faser flera gånger innan vi kan minska felet till önskad nivå.

Det är viktigt att betoning förutsägelse felet kommer aldrig att noll som aldrig är en modell som perfekt förutsäga var resultatet. Det finns dock omfattning fel som kan accepteras av företaget. Under verifieringsprocessen, vill vi se till att vår modell förutsägelse fel är på nivå eller bättre än företag toleransnivån. Det är därför viktigt att ställa in den tillåtna avvikelse i början av cykel under den **problemet formulering** fasen.

### <a name="typical-evaluation-techniques"></a>Utvärdering av vanliga tekniker
Det finns olika sätt i vilka förutsägelse fel kan mätas och möjligt. I det här avsnittet fokuserar vi diskussion om utvärdering tekniker som är relevanta tidsserier och som är specifika för energiförbrukning prognos.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE står för innebär absoluta procentandel fel. Med MAPE vi computing skillnaden mellan varje prognostiserat punkt och det faktiska värdet för den tidpunkten. Vi sedan kvantifiera fel per punkt genom att beräkna andelen skillnaden i förhållande till det faktiska värdet. I det sista steget genomsnittlig vi dessa värden. Matematiska formeln som används för MAPE är följande:

![MAPE formeln](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*där A<sub>t</sub> är det faktiska värdet F<sub>t</sub> är det förväntade värdet och n är prognosen horizon.*

## <a name="deployment"></a>Distribution
När vi har lagt fasen modellering och godkänts modellen prestanda vi är redo att gå till distributionsfasen av. I den här kontexten distribution innebär att aktivera kunden att konsumera modellen genom att köra faktiska förutsägelser på den i stor skala. Distributionen är nyckeln i Azure ML eftersom vårt huvudmål är att ständigt anropa förutsägelser i stället för bara hämta kunskaper från data. Distribueringsfasen är del där vi aktivera modellen som ska förbrukas i stor skala.

Våra målet är att anropa kontinuerliga och periodiska prognoser samtidigt säkerställa att nya data är tillgänglig för modellen och att prognosdata skickas tillbaka till konsumerande klienten inom ramen för energi begäran prognos.

### <a name="web-services-deployment"></a>Distribution av Web Services
Det huvudsakliga distribuerbara byggblocket i Azure ML är webbtjänsten. Detta är det mest effektiva sättet att aktivera förbrukning av en förutsägelsemodell i molnet. Webbtjänsten kapslar modellen och kapslar in den i en [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). API: et kan användas som en del av alla klientkod som visas i diagrammet nedan.

![Vi Tjänstdistribution och användning](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Som kan ses webbtjänsten har distribuerats i Cortana Intelligence Suite molnet och kan anropas via dess exponerade REST API-slutpunkten. Olika typer av klienter i olika domäner kan anropa tjänsten via Web API samtidigt. Webbtjänsten kan även skala för att stödja tusentals samtidiga anrop.

### <a name="a-typical-solution-architecture"></a>En typisk lösningsarkitektur
När du distribuerar en energiförbrukning prognoser lösningen är vi intresserade av att distribuera en heltäckande lösning som är mer omfattande än förutsägelse webbtjänsten och underlättar hela dataflödet. När vi anropa en ny prognos, måste vi Kontrollera att modellen matas med funktioner för aktuella data. Innebär att rådata nyligen insamlade är ständigt inhämtas, bearbetas och omvandlas till de nödvändiga funktionerna på modellen har skapats. På samma gång vill vi göra prognosticerade data tillgängliga för end förbrukar klienter. Ett exempel data flödet cykel (eller data pipeline) visas i diagrammet nedan:

![Energiförbrukning prognos slutpunkt till slutpunkt-dataflöde](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Dessa är de steg som äger rum som en del av energi begäran prognosen cykel:

1. Miljontals distribuerade data mätare som ofta genererar strömförbrukningsdata i realtid.
2. Den här informationen samlas in och överförs till en databas i molnet (*t.ex.*, Azure Blob).
3. Innan den kan bearbetas slås rådata samman till en understation eller regionsnivån som definieras av företaget.
4. Funktionen bearbetningen (se **förberedelse av Data och bearbetning av funktionen**) sedan sker och ger de data som krävs för att modellera utbildning eller bedömningen – funktionen uppsättning data lagras i en databas (*t.ex.*, SQL Azure).
5. Tjänsten igen utbildning anropas för att träna nytt prognosmodellen – den uppdaterade versionen av modellen sparas så att den kan användas av bedömningsprofil webbtjänsten.
6. Bedömningsprofil webbtjänsten anropas enligt ett schema som passar krävs prognosen frekvensen.
7. Prognosticerade data lagras i en databas som kan användas av slutet förbrukning av klienten.
8. Förbrukning klienten hämtar prognoser gäller i rutnätet och förbrukar i enlighet med det obligatoriska användningsfallet.

Det är viktigt att Observera att hela cykeln helt automatiserad och körs enligt ett schema. Hela orchestration av data cykeln kan göras med hjälp av verktyg som [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arkitektur för slutpunkt till slutpunkt-distribution
Vi behöver säkerställa att de nödvändiga komponenterna är etablerad och korrekt konfigurerad för att distribuera en energi begäran prognosen lösning på Cortana Intelligence praktiskt taget.

Följande diagram illustrerar en typisk Cortana Intelligence-baserad arkitektur som implementerar och samordnar flödet datacykel som beskrivs ovan:

![Arkitektur för slutpunkt till slutpunkt-distribution](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Mer information om de olika komponenterna och hela arkitekturen finns energi Lösningsmall.

