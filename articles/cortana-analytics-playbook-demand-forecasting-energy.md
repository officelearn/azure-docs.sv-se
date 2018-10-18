---
title: Cortana Intelligence Solution mallen Spelboken för prognostisering av efterfrågan på energi
description: En Lösningsmall med Microsoft Cortana Intelligence som hjälper till att prognostisera efterfrågan för ett företag för energi-verktyget.
services: machine-learning
author: ilanr9
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/24/2016
ms.author: garye
ms.openlocfilehash: 43a75c31c0f094bdcb7008a39140226815bda163
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49390303"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cortana Intelligence Solution mallen Spelboken för prognostisering av efterfrågan på energi
## <a name="executive-summary"></a>Sammanfattning
Under de senaste åren, Internet of Things (IoT), alternativa energikällor och stordata har slagit samman för att skapa stora möjligheter i verktyget och energi domänen. På samma gång, har verktyget och hela energisektorn sett förbrukning förenkling med konsumenter krävande bättre sätt att styra hur används av energiförbrukning. Därför verktyget och smart grid företag som är i bra behovet av att förnya och förnya sig själva. Dessutom är blir många power och utility rutnät inaktuella och dyra att underhålla och hantera. Teamet har arbetat på ett antal engagemang inom domänen energi under det senaste året. Under dessa engagemang, har det uppstått många fall där verktyg eller ISV: er (oberoende programvaruleverantörer) har letat i prognoser för efterfrågan på energi för framtida. De här prognoserna spelar en viktig roll i sin nuvarande och framtida verksamhet och har blivit grunden för olika användningsfall. Dessa inkluderar kort och lång sikt power belastningen prognosen, handel, belastningsutjämning, grid optimering osv. Stordata och avancerad analys AA-metoder, till exempel Machine Learning (ML) är viktiga aktiverarna för att producera korrekta och tillförlitliga prognoser.  

I den här spelbok kan vi sätta ihop affärs- och analytiska riktlinjer som behövs för en lyckad utveckling och distribution av efterfrågan på energi Prognostisera lösning. De här föreslagna riktlinjer hjälper verktyg, dataexperter och datatekniker biovetenskapskunder att upprätta fullständigt produktionsslutpunkt, molnbaserade och prognoser för efterfrågan lösningar. För företag som precis har börjat deras stordata och avancerad analys resa, kan en lösning representera inledande startvärdet i sina långsiktig strategi för smart rutnätet.

> [!TIP]
> För att ladda ned ett diagram som ger en översikt över arkitekturen i den här mallen [Cortana Intelligence-lösningsmallen arkitektur för prognostisering av efterfrågan på energi](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Översikt
Det här dokumentet beskriver de företag, data och tekniska aspekter av med Cortana Intelligence och i viss Azure Machine Learning (AML) för implementering och distribution av energi prognoser lösningar. Dokumentet består av tre delar:  

1. Förståelse för verksamheten  
2. Förstå data  
3. Teknisk implementering

Den **förståelse för verksamheten** del beskriver företag aspekten som krävs för att förstå och tänka på innan du fattar ett beslut om investeringar. Den förklarar hur du kvalificera affärsproblem till hands så att förutsägelseanalys och machine learning är verkligen effektiva och tillämpliga. Dokumentet ytterligare förklarar grunderna i machine learning och hur de används för att åtgärda problem med prognoser energi. Den beskriver kraven och kompetenskrav av användningsfall. Vissa exempel använda fall och affärsfall scenarier ingår också.

Data är den viktigaste komponenten för alla machine learning-lösning. Den **Data förstå** en del av det här dokumentet beskriver några viktiga aspekter av data. Den beskriver vilken typ av data som behövs för energi prognoser kvalitetskrav för data och vilka datakällor som vanligtvis finns. Vi förklarar också hur rådata för att förbereda datafunktioner som faktiskt Driver modellering del.

Den tredje delen av dokumentet omfattar den **teknisk implementering** aspekt av en lösning. Funktionsframställning och modellering är kärnan i data science process och är därför som diskuteras i detalj. Den behandlar begreppet webbtjänster och de är ett viktigt fordon för distribution av lösningar för förutsägelseanalys. Vi också visas en typisk arkitekturen för en produktionsslutpunkt lösning från slutpunkt till slutpunkt.

Dokumentet innehåller dessutom referensmaterial som du kan använda för att få ytterligare förståelse för domänen och teknik.

Det är viktigt att Observera att vi inte tänker upp i det här dokumentet djupare datavetenskapsprocess dess matematiska och tekniska aspekter. Dessa uppgifter finns i [dokumentation för Azure ML](http://azure.microsoft.com/services/machine-learning/) och [bloggar](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Målgrupp
Målgruppen för det här dokumentet är både företags- och teknisk personal som vill få kunskap och förståelse för Machine Learning-baserade lösningar och hur de används specifikt inom domänen energi prognostisering.

Dataexperter kan också dra från att läsa det här dokumentet för att få en bättre förståelse för högnivåprocessen som styr distributionen av en lösning för prognostisering. I det här sammanhanget det kan också användas för att upprätta en baslinje för bra och startpunkt för mer detaljerad och avancerade material.

### <a name="industry-trends"></a>Branschtrender
Under de senaste åren, IoT, alternativt energikällor och stordata har slagit samman för att skapa stora affärsmöjligheter i området verktyget och energi. På samma gång, har verktyget och hela energi-sektorer sett förbrukning förenkling med konsumenter krävande bättre sätt att styra hur används av energiförbrukning.

Många verktyg och smart energibolag har banbrytande den [smart grid](https://en.wikipedia.org/wiki/Smart_grid) genom att distribuera ett antal Använd fall som gör att användning av data som genereras av rutnätet. Många användningsområden kretsar kring elproduktionskällan sina egenskaper: inte ackumuleras eller tagits ur bruk lagras som inventering. Så måste vad produceras användas. Verktyg som vill bli effektivare måste Prognostisera energiförbrukningen helt enkelt eftersom det ger dem större möjlighet att **balansera tillgång och efterfrågan**, vilket gör energi slöseri **minska växthusgaser utsläpp**, och kontrollera kostnaden.

När man talar kostnader, finns det en annan viktig aspekt, vilket är priset. Nya funktioner för handeln power mellan verktyg ha medfört i bra behöver **göra prognoser om framtida efterfrågan och framtida priset för elektricitet**. Detta kan hjälpa företag att fastställa deras produktionsvolymer.

När vi använder ordet ”smarta” refererar vi faktiskt till ett rutnät som kan läsa och göra förutsägelser. Det kan förutse säsongens ändringar i användning, samt **förutse situationer med tillfällig överbelastning och justera automatiskt för den**. Genom kontroll av förbrukning (med hjälp av dessa smarta elmätare) via en fjärranslutning, kan lokaliserade överlagring situationer hanteras. **Genom att förutsäga först och sedan fungerar**, rutnätet gör själva smartare med tiden.

I resten av det här dokumentet fokuserar vi på en specifik familj av användningsfall som täcker prognoser i framtiden, kortsiktiga och långsiktiga efterfrågan på energi. Vi har jobbat inom följande områden för några månader och fått lite kunskap och kunskaper som gör att vi kan ge branschen i företagsklass resultat. Andra användningsfall beskrivs också i dokumentet inom en snar framtid.

## <a name="business-understanding"></a>Förståelse för verksamheten
### <a name="business-goals"></a>Affärsmål
Den **energi Demo** målet är att demonstrera en typisk förutsägelseanalys och machine learning-lösning som kan distribueras i en mycket kort tidsperiod. Mer specifikt ligger våra aktuella fokus på att aktivera energi begäran prognoser lösningar så att dess affärsvärde kan snabbt insåg och utnyttjas på. Informationen i den här playbook kan hjälpa kunden utföra följande mål:

* Kort tid till värde av machine learning-baserad lösning
* Möjlighet att utöka ett pilotprojekt användningsfall till andra användningsfall eller till ett bredare omfång utifrån sina affärsbehov
* Snabbt få Cortana Intelligence Suite produktinformation

Med följande mål i åtanke syftar spelboken till att leverera affärs- och teknisk kunskap som hjälper dig att uppnå dessa mål.

### <a name="power-load-and-demand-forecasting"></a>Effektbelastningar och prognoser för efterfrågan
Inom energisektorn, kan det finnas många sätt tillgång och efterfrågan prognoser kan lösa allvarliga problem. I själva verket kan prognoser för efterfrågan ses grunden för många kärnor användningsfall i branschen. Generellt sett är vi anser att två typer av energi begäran prognoser: kort sikt och lång sikt. Var och en kan ett annat syfte och använda en annan metod. Den största skillnaden mellan två är prognosmodellen horizon, vilket innebär att tidsintervallet för när vi skulle göra prognoser framåt.

#### <a name="short-term-load-forecasting"></a>Kort sikt belastningen prognoser
Inom ramen för efterfrågan på energi definieras kort sikt läsa in prognostisering (STLF) som den sammanställda belastningen som prognostiserat inom en nära framtid på olika delarna i rutnätet (eller rutnätet som helhet). I det här sammanhanget definieras kort sikt måttypen tidsrymd inom intervallet 1 timme till 24 timmar. I vissa fall kan också en horizon i 48 timmar. Därför är STLF väldigt vanligt inom en operational användningsfall i rutnätet. Här följer några exempel på STLF driven användningsfall:

* Tillgång och efterfrågan belastningsutjämning
* Handel stöd för energisparfunktioner
* Marknaden göra (inställningen power pris)
* Rutnätet operativa optimering
* [Begäran-svar](https://en.wikipedia.org/wiki/Demand_response)
* Högsta prognoser för efterfrågan
* Hantering av efterfrågan på klientsidan
* Belastningsutjämning och överbelasta dataförlustskydd
* Långsiktigt belastningen prognoser
* Fel och avvikelseidentifiering
* Högsta reducering/Köbaserad 

STLF modellen huvudsakligen baseras på nära tidigare (senaste dagen eller veckan) förbrukningsdata och Använd prognostiserat temperatur som en viktig ge säkrare prognoser. Hämta korrekt temperatur prognos för nästa timme och upp till 24 timmar blir mindre en utmaning nu dagar. Dessa modeller är mindre känsliga för säsongens mönster eller trender för långsiktig användning.

SLTF lösningar sannolikt kommer att generera stort antal förutsägande anrop (tjänstbegäranden) eftersom de är som anropas på timbasis och i vissa fall även med högre frekvens. Det är också mycket vanligt att implantation där varje enskild understation eller omvandlare representeras som en fristående modell och volymen av begäranden för förutsägelse är därför ännu större.

#### <a name="long-term-load-forecasting"></a>Långsiktigt belastningen prognoser
Målet för lång sikt belastningen prognostisering (LTLF) är att prognostisera efterfrågan för power med en tidsrymd som sträcker sig från 1 vecka till flera månader (och i vissa fall för ett antal år). Det här intervallet för horizon gäller främst för att planera och investeringar användningsfall.

För långsiktig scenarier är det viktigt att du har stora mängder data som omfattar ett intervall av flera år (minsta 3 år). Dessa modeller vanligtvis extraheras säsongsberoende mönster från historiska data och använda externa predicators, till exempel som väder och klimat mönster.

Det är viktigt att tydliggöra att ju längre tid den prognostisering är, desto mindre exakta prognosen kan vara. Det är därför viktigt att skapa vissa konfidensintervall tillsammans med faktiska prognosen som skulle låta människor att ta med möjliga variationen i sina planeringsprocessen.

Eftersom förbrukning scenariot för LTLF huvudsakligen planerar vi förväntar oss mycket lägre förutsägelse-volymer (jämfört med STLF). Vi har vanligtvis skulle se de här förutsägelserna inbäddad i visualiseringsverktyg, till exempel Excel eller Power BI och ska startas manuellt av användaren.

### <a name="short-term-vs-long-term-prediction"></a>Kort sikt vs. Långsiktigt förutsägelse
I följande tabell jämförs STLF och LTLF i jämfört med de viktigaste attribut:

| Attribut | Skapa prognoser för kortsiktigt Load | Långsiktigt belastningen prognos |
| --- | --- | --- |
| Skapa prognos för Horizon |Från 1 timme till 48 timmar |Från 1 till 6 månader eller mer |
| Granulariteten för data |Varje timme |Varje timme eller varje dag |
| Vanliga användningsområden |<ul><li>/ Efterfrågan belastningsutjämning</li><li>Välj timme prognoser</li><li>Begäran-svar</li></ul> |<ul><li>Långsiktigt planering</li><li>Rutnätet tillgångar planering</li><li>Resursplanering</li></ul> |
| Vanliga förutsägelserna |<ul><li>Dagens eller veckans lopp</li><li>timme på dagen</li><li>Per timme temperatur</li></ul> |<ul><li>Månad på år</li><li>Dagen i månaden</li><li>Långsiktigt temperatur och klimat</li></ul> |
| Historiska dataintervall |Två till tre års data |Fem till 10 års data |
| Vanliga noggrannhet |MAPE * 5% eller lägre |MAPE * 25% eller lägre |
| Prognoser frekvens |Genereras varje timme eller var 24: e timme |Genereras när per månad, kvartal eller år |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – innebära genomsnittlig procent fel

Det är ganska viktigt att skilja mellan kort och lång sikt prognoser scenarier som dessa representerar olika affärsbehov och kan ha olika distribution och förbrukning mönster som kan ses från den här tabellen.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Användningsfall för exempel 1: eSmart system – överlagring optimering
En viktig roll i en [smart grid](https://en.wikipedia.org/wiki/Smart_grid) är att dynamiskt och ständigt optimera och justera efter föränderliga förbrukning mönster. Energiförbrukningen kan påverkas av kortsiktiga ändringar främst orsakade av temperatur variationer (*t.ex.*, mer kraft används för air villkor eller uppvärmning). På samma gång påverkas också energiförbrukningen av långvariga trender. Dessa kan innehålla säsongsberoende effekter, helgdagar, tillväxt för långsiktig användning och ekonomiska faktorer som konsument index, olja pris och BNP.

I det här användningsfallet [eSmart](http://www.esmartsystems.com/) ville distribuera en molnbaserad lösning som gör det möjligt att förutsäga benägenhet av en överbelastning på alla angivna understation i rutnätet. I synnerhet ville eSmart identifiera delstationer som sannolikt kommer att överbelasta inom nästa timma, så en omedelbar åtgärd som kan utföras för att undvika eller lösa denna situation.

Korrekt och snabbt utföra förutsägelse kräver implementering av tre förutsägande modeller:

* Länge termen-modell som gör det möjligt att skapa prognoser för strömförbrukningen på varje understation under nästa några veckor eller månader
* Kortsiktigt modell som aktiverar förutsägelser av överbelastning situationen på en viss understation under nästa timma
* Temperatur-modell som ger prognoser för framtida temperatur över flera scenarier

Målet med långsiktig modellen är att ranka delstationer enligt deras benägenhet överbelasta (beroende på deras Strömkapaciteten överföring) under nästa vecka eller månad. På så sätt kan skapandet av en kort lista med delstationer som skulle fungera som indata för kortsiktig förutsägelse. När temperaturen är ett viktigt ge säkrare prognoser för långsiktig modellen, finns det ett behov av att producera flera scenariot temperatur prognoser och mata dem som indata till långsiktig modellen hela tiden. Kortsiktigt prognosen anropas sedan för att förutsäga vilka understation troligen överbelasta under nästa timma.

Kort och lång sikt modeller distribueras separat per varje understation. Därför kräver praktiska körningen av dessa modeller omfattande dirigering. En mer detaljerad modell är dedikerad för varje timme på dagen för att få högre förutsägelsefunktionen på kort sikt. Dessa modeller körs varje timme och slutförts inom några minuter så att tiden räcker att svara och vidta förebyggande åtgärder vid behov. Den här samlingen av modeller hålls uppdaterad med periodiska omtränings den senaste informationen.

Mer information om det här användningsfallet finns [här](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Använda fallet kriterier – krav
Den huvudsakliga styrkan hos Cortana Intelligence är i dess kraftfulla möjlighet att distribuera och skala centric maskininlärningslösningar. Den är utformad att ge stöd för tusentals förutsägelser som körs samtidigt. Den kan automatiskt skala för att uppfylla arbetsprofilen föränderliga förbrukning. Det är därför en lösning fokus på Precision och prestanda för. Till exempel är ett verktyget företag intresserad av att producera korrekta energiefterfrågan göra prognoser för nästa timme och för varje timme på dagen. Å andra sidan, vi är mindre intresserad av genom att besvara frågan om varför efterfrågan förväntas vara som det är (modellens egen säkerhetsuppsättning ta hand om detta).

Det är därför viktigt att du upptäcker att inte alla användningsfall och företagsproblem kan lösas effektivt med maskininlärning.

Cortana Intelligence och machine learning kan vara mycket effektivt i lösa en viss affärsproblem när följande villkor är uppfyllda:

* Affärsproblem i hand är **förutsägande** sin natur. Ett ärende exempel förutsägande användning är ett verktyg företag som vill förutsäga power belastningen på en viss understation under nästa timma. Å andra sidan, analysera och rangordning för historiska begäran skulle vara **beskrivande** karaktär och därför mindre tillämplig.
* Det finns en clear **sökvägen för åtgärden** som ska vidtas när förutsägelsen är tillgänglig. Till exempel kan att förutsäga ett överlagrade på en understation under nästa timma utlösa en proaktiv åtgärd för att minska belastning som är associerad med den understation och därmed potentiellt hindra en överlagring.
* Användningsfallet representerar en **vanliga typ av problem** så att när löst den kan kunna få till följd att lösa andra liknande användningsfall.
* Kunden kan ställa in **kvantitativ och kvalitativ mål** att demonstrera en lyckad lösningsimplementering. Till exempel ett bra kvantitativt för energi begäran prognosen skulle vara nödvändig Precision tröskelvärdet (*t.ex.*, upp till 5% fel tillåts) eller när förutse understation överbelasta sedan precision (pris för positiva) och återkallande (omfattningen av positiva) ska finnas på ett visst tröskelvärde. Dessa mål måste härledas från kundens affärsmål.
* Det finns en clear **integreringsscenario** med företagets business-arbetsflöde. Till exempel kan understation belastningen prognosen integreras i rutnätet kontrollcenter att tillåta överlagring dataförlustskydd aktiviteter.
* Kunden har är klara att använda **data med tillräckligt hög kvalitet** för användningsfallet (Mer information i nästa avsnitt, **datakvalitet**, av spelboken).
* Kunden använder sig av molnet centric data arkitekturen eller **molnbaserade maskininlärning**, inklusive Azure ML och andra komponenter i Cortana Intelligence Suite.
* Kunden är villigt att upprätta **ett flöde från slutpunkt till slutpunkt** de anläggningar distribution av data i molnet med jämna mellanrum, och är villig att **operationalisera** lösningen.
* Kunden är redo att **dedikerar resurser** som ska vara engagerade under den första pilotprojekt implementeringen så att kunskap och ägarskap för lösningen kan överföras till kunden vid slutförande.
* Kund-resurs ska vara en **skickliga data professional**, helst inom data Science.

Kvalificering av användningsfall baserat på ovanstående villkor kan avsevärt förbättra slutförandefrekvenser för användningsfall och upprätta en bra beachhead för implementeringen av framtida användningsfall.

### <a name="cloud-based-solutions"></a>Molnbaserade lösningar
Cortana Intelligence Suite i Azure är en integrerad miljö som finns i molnet. Distributionen av en analyslösning i en molnmiljö har betydande fördelar för företag och samtidigt kan innebära stor förändring för företag att fortfarande använda lokala IT-lösningar. Inom energisektorn finns det en tydlig trend över gradvis migrering av åtgärder i molnet. Denna trend går hand i hand tillsammans med utvecklingen av smart rutnätet som beskrivs ovan, i **branschtrender**. När denna spelbok fokuserar på en molnbaserad lösning i energi-domän, är det viktigt att förklara fördelarna och annat att tänka på för att distribuera en molnbaserad lösning.

Kanske är den största fördelen med en molnbaserad lösning kostnaden. Lösningen utnyttjar molnet-distribuerade komponenter, det finns inga startavgifter eller kostnad för sålda varor (kostnad för sålda varor) Komponentkostnader i samband med den. Det innebär att det finns inget behov att investera i maskinvara, programvara och IT-Underhåll det finns därför en betydande minskning i affärsrisk.

En annan viktig fördel är användningsbaserad kostnadsstruktur molnbaserade lösningar. Cloud-baserade servrar för databehandling eller lagring kan distribueras och skalas på basis av precis efter behov. Detta representerar kostnaden effektivitet fördelen med en molnbaserad lösning.

Slutligen finns inget behov av att investera i IT-underhåll eller framtida utveckling eftersom allt detta är en del av Microsofts molnbaserade-erbjudandet. I den utsträckning Cortana Intelligence Suite innehåller bäst i klassen tjänster och dess översikten ser till att utvecklas. Nya funktioner, komponenter och funktioner introduceras ständigt och utvecklas.

För ett företag som precis har startats samband med övergången till molnet, rekommenderar vi starkt för att ta en stegvis metod genom att implementera en vägkarta för migrering av molnet. Vi tror att användningsfallen som beskrivs i denna spelbok för samhällstjänster och företag i domänen energi representerar en utmärkt möjlighet för testkörning av lösningar för förutsägelseanalys i molnet.

#### <a name="business-case-justification-considerations"></a>Företag fallet motivering överväganden
Kunden kan vara intresserad av att få en motivering för en viss användningsfall som en molnbaserad lösning och Machine Learning är viktiga komponenter i många fall. Till skillnad från en lokal lösning, när det gäller en molnbaserad lösning komponenten startkostnad är minimal och de flesta av kostnadsfaktorer är associerad med den faktiska användningen. När det gäller att distribuera en lösning på Cortana Intelligence Suite för prognostisering energi, kan flera tjänster integreras med en enda vanliga kostnadsstrukturen. Till exempel databaser (*t.ex.*, SQL Azure) kan användas för att lagra rådata och sedan för den faktiska prognoser Azure ML används som värd för prognostisering tjänsterna. I det här exemplet kan kostnadsstrukturen omfatta lagring och komponenter.

Å andra sidan, ska en ha en god förståelse av affärsvärde för att hantera en efterfrågan på energi som prognostisering (kort eller lång sikt). Det är i själva verket Tänk affärsvärde för varje prognoser åtgärd. Till exempel korrekt prognostisering effektbelastningar för nästkommande 24 timmar kan förhindra overproduction eller kan förhindra överlagringar i rutnätet och detta kan vara kvantifierade när det gäller finansiella besparingar per dag.

En grundläggande formel för beräkning av finansiella fördelen med begäran Prognostisera lösning kan vara: ![grundläggande formel för beräkning av finansiella fördelen med begäran göra prognoser för lösningen](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Eftersom Cortana Intelligence Suite tillhandahåller en prismodell som betalar per användning, finns det inget behov av att faktureras en fast kostnadskomponent formel. Den här formeln kan beräknas på grundval av varje dag, månad eller årlig.

Aktuella Cortana Intelligence Suite och Azure ML prissättningsplaner finns [här](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Process för utveckling av lösning
Utvecklingscykeln av en efterfrågan på energi prognoser lösningen omfattar vanligtvis 4 faser, som vi gör använda molnbaserad teknik och tjänster i Cortana Intelligence Suite.

Detta illustreras i följande diagram:

![Smart Grid cykel](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

Följande stycke beskrivs processen 4 steg:

1. **Insamling av data** – alla avancerade baserat analyslösning förlitar sig på data (se **Data förstå**). Särskilt när det gäller förutsägelseanalys och prognoser, vi förlitar sig på pågående, dynamiska flödet av data. När det gäller energi prognoser för efterfrågan, dessa data kan hämtas direkt från smarta elmätare eller aggregeras redan i en lokal databas. Vi också beroende av andra externa datakällor, till exempel väder och temperatur. Den här pågående flödet av data måste samordnas, schemalagda och lagras. [Azure Data Factory](http://azure.microsoft.com/services/data-factory/) (ADF) är vår största bestämmer hög grad för att utföra den här uppgiften.
2. **Modellering** – för korrekta och tillförlitliga energi prognoser, måste en utveckla (train) och underhålla en bra modell att gör använder av historiska data och extraherar meningsfulla och förutsägande mönster i data. Området för Machine Learning (ML) har växt snabbt med mer avancerade algoritmer som utvecklas regelbundet. Azure ML Studio ger en bra användarupplevelse som hjälper dig att använda de mest avancerade ML-algoritmerna i en fullständig arbetsflöde. Det här arbetsflödet illustreras i en intuitiv flödesdiagram tillsammans med förberedelse av data, extrahering av funktionen, modellering och utvärdering av modellen. Du kan hämta hundratals olika modeller som ingår i den här miljön. I slutet av den här fasen har inom data Science en aktiv-modell som är fullständigt utvärderade och klar för distribution.
   
   Följande diagram är en illustration av ett vanligt arbetsflöde:
   
   ![Arbetsflöde för modellering](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Distribution** – med en aktiv-modell, nästa steg är distribution. Här konverteras modellen till en webbtjänst som Exponerar en RESTful-API som samtidigt kan anropas från olika förbrukning klienter via Internet. Azure ML gör det enkelt att distribuera en modell direkt från Azure ML Studio med ett enda klick för en knapp. Hela distributionsprocessen sker under huven. Den här lösningen kan anpassas automatiskt efter nödvändiga förbrukningen.
4. **Förbrukning** – i den här fasen vi faktiskt göra använder i prognosmodellen för att skapa förutsägelser. Förbrukningen kan också styras från en användarprogram (*t.ex.*, instrumentpanelen) eller direkt från ett operational system såsom/efterfrågan belastningsutjämning system eller en lösning för optimering av rutnät. Flera Användningsscenarier kan också styras från en enda modell.

## <a name="data-understanding"></a>Förstå data
Efter som täcker överväganden för företag (se **förståelse för verksamheten**) av en efterfrågan på energi lösning för prognostisering, är vi nu redo att diskutera datadelen. Lösning för förutsägelseanalys är beroende av pålitlig data. För energi prognoser för efterfrågan, vi förlitar sig på historiska förbrukningsdata med olika detaljnivåer. Den historiska data används som råmaterial. Det krävs en noggrann analys där data Science identifierar förutsägelserna (kallas även funktioner) som kan placeras i en modell som så småningom genererar prognoserna som krävs.

I resten av det här avsnittet beskriver vi de olika åtgärder och överväganden för att förstå data och göra den till ett användbart format.

### <a name="the-model-development-cycle"></a>Utvecklingscykeln modell
Producera bra prognos modeller kräver vissa noggrann förberedelser och planering. Bryta ned modelleringsprocessen i flera steg och fokusera på ett steg i taget kan avsevärt förbättra resultatet av hela processen.

Följande diagram illustrerar hur modelleringsprocessen kan delas upp i flera steg:

![Modellen utvecklingscykeln](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Se cykeln består av sex steg:

* Problemet formulering
* Datainmatning och datautforskning
* Förberedelse av data och funktioner
* Modellering
* Modell-utvärdering
* Utveckling

I resten av det här avsnittet beskriver vi de enskilda stegen och saker att tänka på vid varje steg.

### <a name="problem-formulation"></a>Problemet formulering
Vi kan du överväga att problemet formulering som mest kritiska steg som krävs för att ta innan någon lösning för förutsägelseanalys. Här vi skulle transformera affärsproblemet och dela upp den till specifika element som kan lösas genom att använda data och modellering tekniker. Det är en bra idé att formulera problemet som en uppsättning frågor som vi vill att besvara. Här följer några möjliga frågor som kan tillämpas inom omfånget för prognostisering av efterfrågan i energi:

* Vad är den förväntade belastningen på en enskild understation i nästa timme eller dag?
* Vilken tid på dagen min grid får toppar i efterfrågan?
* Hur sannolikt är min rutnät för hantering av den förväntade hög belastningen?
* Hur mycket kraft bör power station generera under varje timme på dagen?

Utformningen av dessa frågor kan vi fokusera på att få rätt data och implementera en lösning som är helt i linje med affärsproblem till hands. Dessutom kan vi sedan ange vissa viktiga mått som gör att vi kan utvärdera prestanda för modellen. Till exempel hur exakt prognosen ska och vad är intervallet för fel som är godkända företaget?

### <a name="data-sources"></a>Datakällor
Moderna smart rutnätet samlar in data från olika delar och komponenter i rutnätet. Dessa data representerar olika aspekter av åtgärden och användningen av power rutnätet. Inom omfånget för den göra prognoser för efterfrågan på energi, vi begränsar diskussion om datakällor som motsvarar den faktiska efterfrågan förbrukningen. En viktig källa för energiförbrukning är smarta elmätare. Hjälpmedel i hela världen distribuerar snabbt smarta elmätare för sina kunder. Smarta elmätare registrera den faktiska energiförbrukningen och ständigt vidarebefordra dessa data tillbaka till verktyget företaget. Data som samlas in och skickas tillbaka med ett fast intervall som sträcker sig från varje 5 minuter till 1 timme. Mer avancerade smarta elmätare kan programmeras via en fjärranslutning för att styra och balansera den faktiska förbrukningen inom ett hushåll. Smart mätaren data är relativt tillförlitlig och innehåller en tidsstämpel. Den gör det en viktig ingrediens för Skapa prognoser för efterfrågan. Mätaren data kan aggregeras (samlade in) på olika nivåer i rutnätet-topologin: omvandlare, understation, region, *osv*. Vi kan sedan välja krävs aggregering nivå om du vill bygga en prognosmodell för den. Exempelvis om verktyget företaget vill göra prognoser för framtida belastningen på var och en av dess grid delstationer kan sedan alla mätare data aggregeras för varje enskild understation och används som indata för i prognosmodellen. Vi refererar till smarta elmätare som en intern datakälla.

En tillförlitlig energi begäran prognos också förlitar sig på andra externa datakällor. En viktig faktor som påverkar energiförbrukningen är vädret eller mer exakt temperatur. Historiska data visar stark korrelation mellan extern temperatur och energiförbrukningen. Under frekvent sommar dagar konsumenterna i deras användning av deras luftkonditionering samt under vinter-ström på systemet. En pålitlig källa för historiska temperaturerna på grid plats är därför nyckel. Dessutom beroende vi också rättvisande prognos för temperatur som en ge säkrare prognoser om energiförbrukningen.

Andra externa datakällor kan också hjälpa att skapa energi begäran prognosmodeller. Dessa kan innehålla långsiktig klimat ändringar, ekonomisk index (*t.ex.*, BNP), med mera. I det här dokumentet ingår vi inte dessa andra datakällor.

### <a name="data-structure"></a>Datastruktur
När du har identifierat de nödvändiga datakällorna, vill vi se till att rådata som har samlats in innehåller rätt datafunktioner. För att bygga en prognosmodell för tillförlitlig begäran, måste vi se till att data som samlas in omfattar dataelement som kan hjälpa dig att förutse framtida efterfrågan. Här följer några grundläggande kraven datastruktur (schema) över rådata.

Rådata består av rader och kolumner. Varje mått representeras som en enda rad med data. Varje rad med data innehåller flera kolumner (även kallat funktioner eller fält).

1. **Tidsstämpel** – tidsstämpelfältet representerar den faktiska tiden när mätningen registrerades. Den bör följa en av de vanliga datum/tid-format. Både datum och tid delar ska inkluderas. I de flesta fall finns det inget behov av den tid som ska registreras till andra granularitetsnivån. Det är viktigt att ange tidszonen där data har registrerats.
2. **Mäter ID** – det här fältet identifierar mätaren eller mätning enheten. Det är en kategoriska variabel och kan vara en kombination av siffror och tecken.
3. **Värdet för förbrukning** – det här är den faktiska förbrukningen på ett visst datum/tid. Förbrukningen kan mätas i kWh (kilowatt-hour) eller någon annan önskade enheter. Det är viktigt att notera att måttenheten måste vara konsekvent över alla mått i data. I vissa fall kan förbrukning gav över 3 power faser. I så fall behöver vi samla in alla oberoende förbrukning faser.
4. **Temperatur** – temperaturen är vanligtvis som samlas in från en oberoende källa. Det bör dock vara kompatibla med förbrukningsdata. Den ska innehålla en tidsstämpel som beskrivs ovan så att programmet synkroniseras med den faktiska förbrukningsdata. Temperaturvärdet som kan anges i grader Celsius eller Fahrenheit men ska vara konsekvent över alla mått.
5. **Plats –** Platsfältet är vanligtvis kopplade till den plats där temperaturdata som har samlats in. Det kan visas som ett postnummer tal eller latitud/longitud (lat/långt)-format.

Följande tabeller visar exempel på ett bra förbrukning och temperatur dataformat:

| **datum** | **tid** | **Mätnings-ID** | **Fas 1** | **Fas 2** | **Fas 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **datum** | **tid** | **Plats** | **temperatur** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Som visas ovan, innehåller det här exemplet 3 olika värden för användning som är associerade med 3 power faser. Observera också att datum och tid fälten avgränsas, men också kombineras i en enda kolumn. I det här fallet representeras kolumnen plats i ett 5-siffrig postnummer format och temperaturen i grader Celsius format.

### <a name="data-format"></a>Dataformat
Cortana Intelligence Suite har stöd för de vanligaste dataformat som CSV, TVS, JSON, *osv*. Det är viktigt att dataformatet förblir konsekvent för hela livscykeln för projektet.

### <a name="data-ingestion"></a>Datainhämtning
Eftersom energi begäran prognosen ständigt och ofta förväntas, måste vi Kontrollera att rådata flödar med hjälp av en stabil och tillförlitlig inhämtning process. Datainmatning processen måste garantera att rådata är tillgänglig för prognostisering processen på den begärda tiden. Som innebär att frekvensen för inmatning av data ska vara större än prognosmodellen frekvensen.

Till exempel: om vår lösning för prognostisering begäran skulle Generera en ny prognos kl 8:00 dagligen så vi måste se till att alla data som har samlats in under de senaste 24 timmarna har fullständigt inmatade till den tidpunkten och måste även inkluderar den senaste timmen av  data.

Cortana Intelligence Suite erbjuder olika sätt att stödja en tillförlitlig inhämtning process för att kunna göra detta. Det här diskuteras ytterligare i den **distribution** i det här dokumentet.

### <a name="data-quality"></a>DQS
Råa datakällan som krävs för att utföra prognoser för efterfrågan högtillförlitliga och exakta måste uppfylla vissa kvalitetskriterier för grundläggande data. Även om avancerad statistiska metoder kan användas för att kompensera för vissa möjliga data kvalitetsproblem, måste vi fortfarande att se till att vi korsa vissa grunddata kvalitet tröskelvärde när mata in nya data. Här följer några saker som om rådata kvalitet:

* **Saknade värden** – detta avser situationen när specifikt mått inte samlades in. Det grundläggande kravet är att frekvensen saknas värdet inte får vara större än 10% för en viss tidsperiod. I fall att ett enda värde saknas det bör anges med ett fördefinierat (till exempel: ”9999') och inte” 0 ”vilket kan vara ett giltigt mått.
* **Mätningsnoggrannhet** – det faktiska värdet för förbrukning eller temperatur ska registreras korrekt. Felaktiga mått skapas felaktiga prognoser. Mätning fel bör vanligtvis vara lägre än 1% i förhållande till värdet true.
* **Tid för mätning** – det krävs att den faktiska tidsstämpeln för data som samlas in kommer inte avvika med mer än 10 sekunder i förhållande till SANT tidpunkten för den verkliga mätningen.
* **Synkronisering** – när flera datakällor som används (*t.ex.*, förbrukning och temperatur) vi måste se till att det finns inga tidssynkronisering problem med varandra. Det innebär att tidsskillnaden mellan den insamlade tidsstämpeln från alla två oberoende datakällor inte får överstiga mer än 10 sekunder.
* **Svarstid** – som beskrivs ovan, i **datainmatning**, vi är beroende av en tillförlitlig flödet och inmatning process. Att kontrollera att vi måste se till att vi Kontrollera datafördröjningen. Detta har angetts som tidsskillnaden mellan den tid som den verkliga mätningen togs och tid då den har lästs in i Cortana Intelligence Suite-lagring och är redo för användning. För kortsiktigt prognoser den totala svarstiden får inte vara större än 30 minuter. För lång sikt prognoser den totala svarstiden får inte vara större än 1 dag.

### <a name="data-preparation-and-feature-engineering"></a>Förberedelse av data och funktioner
När rådata har tagits matas in (se **datainmatning**) och har på ett säkert sätt lagras, är den redo att bearbetas. Förberedelsefasen data i princip hämta rådata och konvertera (omvandla, är Omforma) den i ett formulär för modellering-fasen. Som kan innehålla enkla åtgärder som att använda kolumnen rådata skick med dess faktiska uppmätta värdet, standardiserad värden, mer komplexa åtgärder som [tid släpar](https://en.wikipedia.org/wiki/Lag_operator), med mera. Den nyligen skapade kolumner kallas datafunktioner och processen att generera dessa kallas för funktioner. I slutet av den här processen har vi en ny datauppsättning som erhållits från rådata och kan användas för modellering. Dessutom förberedelsefasen data behöver ta hand om värden som saknas (se **datakvalitet**) samt kompensera för dessa. I vissa fall kan vi också behöva normalisera data för att säkerställa att alla värden som visas i samma skala.

I det här avsnittet vi listas några av de vanliga funktioner som ingår i energi för prognosmodeller begäran.

**Tid som drivs av funktioner:** funktionerna härleds från datum/tidsstämpel. Dessa är extraherade och konverteras till kategoriska funktioner som:

* Tid på dagen – det här är timme på dagen som tar värden mellan 0 och 23
* Dag i veckan – det här representerar dag i veckan och hämtar ett värde från 1 (söndag) till 7 (lördag)
* Dag i månaden – detta motsvarar faktiska datumet och kan ha värden mellan 1 och 31
* Månad på år – det här motsvarar månaden och hämtar ett värde från 1 (januari) och 12 (December)
* Helgen – detta är ett binärt värde-funktion som hämtar ett värde på 0 för veckodagar eller 1 för helgen
* Helgdag – detta är ett binärt värde-funktion som hämtar ett värde på 0 för en vanlig dag eller 1 för en helgdag
* Fourier termer – Fourier villkoren är vikterna som härleds från tidsstämpel och används för att avbilda säsongsvärdet (cycles) i data. Eftersom vi kan ha flera säsonger i våra data kan vi behöva flera Fourier termer. Begäran värden kan exempelvis ha årliga veckovisa och dagliga säsonger/cykler som leder till 3 Fourier villkor.

**Oberoende och skäliga funktioner:** oberoende funktioner inkluderar alla dataelement som vi vill använda som förutsägelserna i modellen. Här utesluter vi funktionen beroende som vi behöver för att förutsäga.

* Funktionen lag – det här är tid beräkningsarbete värdena för den faktiska efterfrågan. Lag 1-funktioner ska innehålla värdet för begäran i föregående Timma (förutsatt att per timme) i förhållande till aktuell timestamp. På samma sätt kan vi kan lägga till fördröjning 2, lag 3, *osv*. Faktiska kombinationen av lag-funktioner som används fastställs under fasen för modellering av utvärdering av modellen resultaten.
* Långvariga trender – den här funktionen representerar linjär tillväxt i efterfrågan mellan åren.

**Beroende funktionen:** beroende funktionen är datakolumnen som vi vill gärna vår modell att förutsäga. Med [övervakat maskininlärning](https://en.wikipedia.org/wiki/Supervised_learning), måste vi först öva med modellen med hjälp av beroende funktioner (som även kallas etiketter). På så sätt kan modellen att lära dig mönster i data som är associerade med funktionen beroende. Skapa prognoser för efterfrågan på energi vi normalt vill förutsäga den faktiska efterfrågan och därför använder vi den som beroende funktion.

**Hantering av värden som saknas:** under förberedelsefasen data som behöver avgöra den bästa strategin för att hantera värden som saknas. Detta görs med hjälp av de olika statistiska huvudsakligen [data uppräkning metoder](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). När det gäller energi prognoser för efterfrågan, vi normalt sedan imputera saknade värden med hjälp av glidande medelvärde från föregående tillgängliga datapunkter.

**Databasnormalisering:** databasnormalisering är en annan typ av omvandling som används för att ta med alla numeriska data, till exempel skapa prognoser för efterfrågan i en liknande skala. Detta ger vanligtvis bättre modellens Precision och precision. Vi skulle vanligtvis göra detta genom att dividera det faktiska värdet med en uppsättning data.
Det ursprungliga värdet skalas till ett mindre intervall, vanligtvis mellan 1 och 1.

## <a name="modeling"></a>Modellering
Fasen modellering är där konverteringen av data till en modell sker. I kärnan i den här processen om det är avancerade algoritmer som Genomsök historiska data (träningsdata), extrahera mönster och skapa en modell. Den modellen kan användas senare att förutsäga på nya data som inte har använts för att bygga modellen.

När vi har en fungerande tillförlitlig modell som vi kan sedan använda den för att samla in nya data som är strukturerade för att inkludera de nödvändiga funktionerna (X). Bedömnings processen blir använder beständiga modellen (objekt från fasen utbildning) och förutsäga en målvariabel som markerats med Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Efterfrågeprognostisering modellering tekniker
När det gäller efterfrågeprognostisering vi att använda historiska data som är ordnade efter tid. Vi Allmänt refererar till data som innehåller time-dimension som [time series](https://en.wikipedia.org/wiki/Time_series). Målet i time series modellering är att hitta tid relaterade trender, säsongsberoende, automatisk korrelation (korrelation över tid) och formulera dem till en modell.

Under de senaste åren har avancerade algoritmer utvecklats för att få plats med tidsserier och för att förbättra prognosernas exakthet. Vi går kortfattat igenom några av dem här.

> [!NOTE]
> Det här avsnittet är inte avsedd att användas som en machine learning och prognostisering översikt utan i stället som en kort översikt över modellering tekniker som ofta används för prognostisering av efterfrågan. Mer information och utbildningsmaterial om tidsserier rekommenderar vi starkt online boken [prognostisering: principer och praxis](https://www.otexts.org/book/fpp).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (glidande medelvärde)**](https://www.otexts.org/fpp/6/2)
Glidande medelvärde är en av de första analystekniker som har använts för tidsserier och det är fortfarande en av mest vanliga tekniker från och med dagens datum. Det är också grunden för mer avancerade prognoser tekniker. Med glidande medelvärde vi prognoser nästa datapunkt som medelvärdet över de senaste punkterna K, där K anger ordningen för glidande medelvärdet.

Glidande medelvärde tekniken har effekten av Utjämning prognosen och därför inte kan hantera även stora volatil i data.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (exponentiell utjämning)**](https://www.otexts.org/fpp/7/5)
Exponentiell utjämning (ETS) är en familj av olika metoder som använder Viktat genomsnitt för senaste datapunkter för att förutsäga nästa datapunkt. Tanken är att tilldela högre vikter till nyare värden och gradvis minska denna vikt för äldre mätvärden. Det finns ett antal olika metoder med familjen, några av dem är hantering av säsongsberoende i data som [Holt-Winters säsongens metoden](https://www.otexts.org/fpp/7/5).

Vissa av dessa metoder också räkna in säsongsberoende data.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (automatisk Regression integrerat glidande medelvärde)**](https://www.otexts.org/fpp/8)
Automatisk Regression integrerad flytta medelvärde (ARIMA) är en annan familj av metoder som används för prognostisering tidsserie. Den kombinerar praktiskt taget automatisk regression metoder med glidande medelvärde. Metoder för automatisk regression använda regressionsmodeller genom att tidigare time series-värden för att kunna beräkna nästa datum punkt. ARIMA metoder gäller även differentierande metoder beräknar skillnaden mellan datapunkter och använda dem i stället för det ursprungliga uppmätta värdet. Slutligen ARIMA också använder glidande medelvärde tekniker som beskrivs ovan. Kombinationen av alla dessa metoder på olika sätt är vad konstruktioner familjen av ARIMA metoder.

ETS och ARIMA används ofta i dag för prognostisering av efterfrågan i energi och många andra prognosmodellen problem. I många fall dessa kombineras tillsammans för att leverera mycket korrekta resultat.

**Allmän multipel Regression** regressionsmodeller kan vara det viktigaste sättet att modellera inom domänen för machine learning och statistik. I samband med tidsserier använder vi regression för att förutsäga framtida värden (*t.ex.*, på begäran). I regression vi ta en linjär kombination av förutsägelserna och lär dig vikterna (kallas även koefficienter) av dessa förutsägelserna under utbildning. Målet är att skapa en regressionslinjen som kommer Skapa prognoser för vår förutsagt värde. Regression metoder är lämplig när en målvariabel är numeriska och därför också passar tidsserier. Det finns en mängd olika regression sätt, bland annat enkelt regressionsmodeller som [linjär Regression](https://en.wikipedia.org/wiki/Linear_regression) och mer avancerade de som till exempel beslutsträd, [slumpmässiga skogar](https://en.wikipedia.org/wiki/Random_forest), [Neural Nätverk](https://en.wikipedia.org/wiki/Artificial_neural_network), och förstärkta beslutsträd.

Konstruera efterfrågan på energi prognoser som ett regressionsproblem ger oss stor flexibilitet vid valet av våra datafunktioner som kan kombineras från den faktiska efterfrågan time series-data och externa faktorer, till exempel temperatur. Mer information om de valda funktionerna beskrivs i funktionen-tekniker (se **förberedelse av Data och funktionen tekniska**) delen av denna spelbok.

Av erfarenhet med implementering och distribution av energi begäran prognoser pilot vi har hittat att avancerade regressionsmodeller som är tillgängliga i Azure ML tenderar att skapa bästa resultat och gör vi utnyttja dem.

## <a name="model-evaluation"></a>Modell-utvärdering
Modellen utvärderingen har en viktig roll i den **modellen utvecklingscykeln**. I det här steget kan vi titta på verifiering av modellen och dess prestanda med verkliga data. Under steget modellering använder vi en del av alla tillgängliga data för att träna modellen. Under utvärderingsfasen tar vi resten av data som ska testa modellen. Praktiskt taget innebär det att vi vehicle modellen nya data som har varit strukturerats och innehåller samma funktioner som datamängd för träning. Men under verifieringsprocessen använder vi modellen att förutsäga en målvariabel i stället för att tillhandahålla en målvariabel som är tillgängliga. Vi refererar ofta till den här processen som bedömning av modellen. Vi skulle sedan SANT målvärden och jämför dem med de förväntade. Avsikten är att mäta och minimera förutsägelse felet, vilket innebär att skillnaden mellan förutsägelserna och värdet true. Kvantifiera fel mätningen är nyckeln eftersom vi vill finjustera modellen och verifiera om felet faktiskt minskar. Finjustera modellen kan göras genom att ändra Modellparametrar som styr hur learning eller genom att lägga till eller ta bort datafunktioner (kallas [parametrar Svep](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praktiskt taget innebär att vi kan behöva upprepa funktionsframställning, modellering, och modellera utvärdering faser flera gånger förrän vi kan minska felet till önskad nivå.

Det är viktigt att betoning förutsägelse felet aldrig att noll som det finns aldrig en modell som kan förutsäga var resultatet perfekt. Det finns dock omfattning fel som är godtagbar för företaget. Vi vill se till att vår modellfel för förutsägelse är på nivå under verifieringsprocessen, eller bättre än företag toleransnivån. Det är därför viktigt att ställa in den tillåtna avvikelse i början av cykeln under den **problemet formulering** fas.

### <a name="typical-evaluation-techniques"></a>Vanliga utvärdering tekniker
Det finns olika sätt i vilka förutsägelse fel kan mätas och kvantifierade. I det här avsnittet fokuserar vi diskussion om utvärderingen tekniker som är relevanta tidsserier och i viss för Skapa prognoser för efterfrågan på energi.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE står för innebära absoluta procentandel fel. Med MAPE är vi databehandling skillnaden mellan varje prognostiserat punkt och det faktiska värdet för den punkten. Vi kan sedan kvantifiera fel per punkt genom att beräkna andelen av skillnaden i förhållande till det faktiska värdet. På det sista steget genomsnittlig vi dessa värden. Matematiska formeln som används för MAPE är följande:

![MAPE formeln](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*där en<sub>t</sub> är det faktiska värdet F<sub>t</sub> är det förväntade värdet och n är prognoser horizon.*

## <a name="deployment"></a>Distribution
När vi har lagt fasen modellering och godkänts modellprestanda vi är redo att gå till distributionsfasen av. I det här sammanhanget innebär distributionen att aktivera kunden att förbruka modellen genom att köra faktiska förutsägelser på den i stor skala. Begreppet distribution är nyckeln i Azure ML eftersom vårt huvudsakliga mål är att ständigt anropa förutsägelser istället för bara hämta kunskap från data. Distribueringsfasen är del där vi göra det möjligt att modellen ska kunna användas i stor skala.

Inom ramen för energi begäran prognosen är vår syftet att anropa kontinuerliga och regelbundna prognoser samtidigt som man säkerställer att nyaste data är tillgängliga för modellen och att prognosdata skickas tillbaka till konsumerande klienten.

### <a name="web-services-deployment"></a>Web Services-distribution
Det huvudsakliga distribuerbar byggblocket i Azure ML är webbtjänsten. Det här är det mest effektiva sättet att aktivera förbrukning av en förutsägande modell i molnet. Webbtjänsten innehåller modellen och täcker med en [RESTful](http://www.restapitutorial.com/) API (Application Programming Interface). API: et kan användas som en del av alla klientkod enligt beskrivningen i diagrammet nedan.

![Vi Tjänstdistribution och användning](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Se, webbtjänsten har distribuerats i Cortana Intelligence Suite-molnet och kan anropas via dess exponerade REST API-slutpunkt. Annan typ av klienter i olika domäner kan anropa tjänsten via webb-API samtidigt. Webbtjänsten kan även skala för tusentals samtidiga anrop.

### <a name="a-typical-solution-architecture"></a>En typisk lösning-arkitektur
När du distribuerar en lösning för prognostisering efterfrågan på energi, är vi intresserade av att distribuera en heltäckande lösning som är mer omfattande än webbtjänst för förutsägelser och underlättar hela dataflödet. När vi anropa en ny prognos, måste vi se till att modellen matas med uppdaterade datafunktioner. Som innebär att nya insamlade rådata är ständigt matas in, bearbetas och omvandlas till de nödvändiga funktionerna på modellen har skapats. På samma gång vill vi göra prognostiserade data tillgängliga för end förbrukar klienter. Ett exempel data flow cykel (eller datapipeline) visas i diagrammet nedan:

![Efterfrågan på energi beräkna dataflödet från slutpunkt till slutpunkt](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Detta är de steg som äger rum som en del av prognoscykel för energi begäran:

1. Miljontals distribuerade mätare som ständigt genererar strömförbrukningsdata i realtid.
2. Den här informationen samlas in och har överförts till en molnlagringsplats (*t.ex.*, Azure Blob).
3. Innan den kan bearbetas sammanställs rådata till en understation eller regionsnivån enligt definitionen i verksamheten.
4. Funktionen bearbetningen (se **förberedelse av Data och bearbetning av funktionen**) sedan äger rum och ger de data som krävs för att modellera utbildning eller bedömning – funktionen set data lagras i en databas (*t.ex.*, SQL Azure).
5. Nytt utbildning tjänsten anropas för att träna igen i prognosmodellen – bevaras den uppdaterade versionen av modellen så att den kan användas av bedömning av webbtjänsten.
6. Bedömning av webbtjänsten anropas enligt ett schema som passar den nödvändiga prognoser frekvensen.
7. Prognostiserade data lagras i en databas som kan användas av klienten slutet förbrukning.
8. Förbrukning klienten hämtar prognoserna, tillämpar den tillbaka till rutnätet och förbrukar i enlighet med det obligatoriska användningsfallet.

Det är viktigt att Observera att den här hela livscykel helt automatiserad och körs enligt ett schema. Hela dirigering av den här cykeln data kan göras genom att använda verktyg som [Azure Data Factory](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Distributionsarkitektur för slutpunkt till slutpunkt
Vi måste se till att de nödvändiga komponenterna har upprättats och korrekt konfigurerad för att kunna distribuera en energi begäran prognoser lösning på Cortana Intelligence praktiskt taget.

Följande diagram illustrerar en typisk Cortana Intelligence-baserad arkitektur som implementerar och dirigerar flödet data-cykel som beskrivs ovan:

![Distributionsarkitektur för slutpunkt till slutpunkt](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Mer information om var och en av komponenterna och hela arkitekturen finns i lösningsmallen energi.

