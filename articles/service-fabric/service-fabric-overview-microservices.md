---
title: "Introduktion till mikrotjänster i Azure | Microsoft Docs"
description: "En översikt över Varför skapa molnprogram med en mikrotjänster är viktigt för moderna programutveckling och hur Azure Service Fabric ger en plattform för att åstadkomma detta."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: f69f594d058ba061cec116f87435c96280e19f93
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2017
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Varför en mikrotjänster-lösning för att bygga program?
Som programutvecklare finns det inget i hur vi tror att om ett program i komponentdelar factoring. Är det centrala paradigmet Objektorientering, programvara abstraktioner och Komponentindelning. Idag är tenderar det här factorization att ske i form av klasser och gränssnitt mellan delade bibliotek och tekniklager. En riskbedömning utförs vanligtvis med en backend-store, affärslogik på mellannivå och en frontend användargränssnittet (UI). Vad *har* ändrats under de senaste åren är att vi, som utvecklare skapar distribuerade program som är för molnet och styrs av företaget.

Ändra affärsbehov är:

* En tjänst som har skapats och fungerar i stor skala att nå kunder i nya geografiska regioner (till exempel).
* Snabbare leverans av funktioner och möjligheter för att kunna svara på kundens krav på ett sätt som är flexibel.
* Bättre resursutnyttjande att minska kostnaderna.

Dessa affärsbehov påverkar *hur* vi skapa program.

Mer information om metoden Azure för att mikrotjänster [Mikrotjänster: ett program revolution drivs av molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolitisk kontra mikrotjänster Designmetoden
Alla program utvecklas över tid. Lyckade program utvecklas genom att vara användbara för personer. Misslyckade program utvecklas inte och så småningom är föråldrade. Frågan blir: hur mycket vet du om dina krav idag och vad de är i framtiden? Anta exempelvis att du skapar en reporting program för en avdelning. Du är säker på att programmet fortfarande är inom omfånget för ditt företag och att rapporterna är tillfällig. Ditt val av metoden skiljer sig från, säg, skapa en tjänst som levererar videoinnehåll till tiotal miljoner kunder. 

Ibland komma åt något ut som konceptbevis är drivande faktor när du vet att programmet kan vara designats om senare. Det finns lite plats i utveckling över något som aldrig används. Det är vanligt engineering kompromiss. När företag talar om att skapa för molnet, är förväntningen tillväxt och användning. Problemet är att tillväxt och skala oförutsägbart. Vi vill kunna prototyp snabbt vid också veta att vi är på en sökväg för att hantera framtida lyckades. Det här är lean Start-metod: skapa, mått, Läs och iterera.

Under klient – server-era avsett vi att fokusera på att bygga nivåindelat program med hjälp av specifika tekniker i varje nivå. Termen *monolitisk* program är här för dessa metoder. Gränssnitt som är avsett att vara mellan nivåer och en mer tätt kopplade design användes mellan komponenter inom varje nivå. Utvecklare som utformats och beräknade klasser som har kompilerats till bibliotek och länkade till några körbara filer och DLL-filer. 

Det finns sådana monolitisk Designmetoden fördelar. Det är ofta enklare att utforma och har snabbare anrop mellan komponenter, eftersom dessa anrop ofta över kommunikation mellan processer (IPC). Alla tester också en produkt, som kan vara mer personer-resurs effektivt. Nackdelen är att det finns ett nära kopplingen mellan nivåindelade lager och du kan skala enskilda komponenter. Du måste vänta på att andra ska kunna slutföra sin testning om du behöver utföra korrigeringar eller uppgraderingar. Det är svårare att vara flexibel.

Mikrotjänster åtgärda dessa downsides och mer överensstämmer nära med kraven ovan, men de har också både fördelar och skulder. Fördelar med mikrotjänster är att var och en vanligtvis kapslar enklare business-funktioner, vilket du skala upp eller ned, testa, distribuera och hantera oberoende av varandra. En viktig fördel med en mikrotjänster metod är att team drivs mer affärsscenarier än av teknik som uppmuntrar att denna riskbedömning. I praktiken mindre team utveckla en mikrotjänster baserat på ett scenario med kund och använda några tekniker som de själva väljer. 

Med andra ord behöver inte organisationen du standardisera teknisk för att upprätthålla mikrotjänster program. Enskilda team att egna tjänster kan göra vad klokt för dem baserat på teamet kunskaper eller vad som är mest lämpliga för att lösa problemet. I praktiken kan lagra en uppsättning rekommenderade tekniker, till exempel en viss NoSQL eller web application framework, är att föredra.

Nackdelen med mikrotjänster kommer hantera ökat antal separata enheter samt hantera mer komplexa distributioner och versioner. Nätverkstrafiken mellan mikrotjänster ökar samt motsvarande nätverksfördröjningar. Många chatty, detaljerade tjänster är ett recept för utmaning prestanda. Utan verktygen för att visa dessa beroenden, är det svårt att ”finns” hela systemet. 

Standarder göra mikrotjänster fungerar genom att hur du kan kommunicera och som feltoleranta av de saker som du behöver från en tjänst, snarare än hårda kontrakt. Det är viktigt att definiera dessa kontrakt direkt i designen, eftersom services uppdatera oberoende av varandra. En annan beskrivning myntades för hur man designar med en mikrotjänster är ”detaljerade tjänstorienterad arkitektur (SOA)”.

***I sin enklaste handlar Designmetoden mikrotjänster om en frikopplad federation för tjänster med oberoende ändringar i varje och överenskomna standarder för kommunikation.***

Eftersom mer molnappar produceras identifiera personer att denna uppdelning av övergripande appen till oberoende, scenariot fokuserar services är en bättre långsiktig strategi.

## <a name="comparison-between-application-development-approaches"></a>Jämförelse mellan programutveckling närmar sig
![Programutveckling för Service Fabric-plattformen][Image1]

1) En monolitisk app dividerat normalt med funktionella lager, till exempel webb-, business- och innehåller domän-specifika funktioner.

2) Du skalar en monolitisk app genom att klona på flera servrar för virtuella datorer/behållare.

3) Ett program för mikrotjänster separerar funktioner i separata mindre tjänster.

4) Mikrotjänster metoden skalar upp genom att distribuera varje tjänst oberoende av varandra, skapar instanser av dessa tjänster via servrar för virtuella datorer/behållare.

Designa med en mikrotjänster metod är inte en panacea för alla projekt, men den överensstämmer närmare med affärsmål som beskrivs ovan. Från och med en monolitisk metod kan användas om du vet att du har möjlighet att omarbeta koden senare i en mikrotjänster design. Vanligare, börja med ett monolitisk program och dela långsamt upp stegvis, från och med huvudområden som måste vara mer skalbart eller flexibel.

Sammanfattningsvis, är den mikrotjänster-metoden att skapa programmet små tjänsterna. Tjänsterna körs i behållare som distribueras på ett kluster på datorer. Utveckla en tjänst som fokuserar på ett scenario med mindre grupper och testa oberoende versionen, distribuera och anpassa varje tjänst så att hela programmet kan utvecklas.

## <a name="what-is-a-microservice"></a>Vad är en mikrotjänster?
Det finns olika definitioner av mikrotjänster. Om du söker på Internet, hittar du många användbara resurser som tillhandahåller egna åsikter och definitioner. Men är de flesta av följande egenskaper för mikrotjänster ofta överens:

* Kapslar in ett kunden eller scenario. Vad är den aktuella problemlösning?
* Utvecklats av en liten teknikteamet.
* Skrivs i någon programmeringsspråket och använda alla framework.
* Består av koden och (valfritt) tillstånd som är oberoende av varandra version distribueras och skalas.
* Samverka med andra mikrotjänster via väldefinierade gränssnitt och protokoll.
* Ha unika namn (URL: er) som används för att matcha deras plats.
* Förbli konsekventa och tillgängliga med fel.

Du kan sammanfatta dessa egenskaper till:

***Mikrotjänster program består av små, oberoende version och skalbar kunden fokuserar tjänster som kommunicerar med varandra via standardprotokoll med väldefinierade gränssnitt.***

Vi beskrivs de två första punkterna i föregående avsnitt och nu vi expanderar på och förtydliga övriga.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Skrivs i någon programmeringsspråket och använda alla framework
Som utvecklare ska vi frihet att välja ett annat språk eller ramverk som vi vill, beroende på vår kunskaper eller behov av tjänsten. I vissa tjänster kanske du värdet prestandafördelarna med C++ framför allt annat. I andra tjänster kanske enkel hanterad utveckling i C# eller Java viktigaste. I vissa fall kan du behöva använda en viss partner bibliotek, data lagringsteknik eller sätt att exponera tjänsten till klienter.

När du har valt en teknik du gå till hanteringskonsolen operativa eller livscykel och skalning av tjänsten.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Tillåter kod och tillstånd för att vara oberoende version distribueras och skalas
Men du vill skriva din mikrotjänster, koden och eventuellt tillståndet bör oberoende distribuera, uppgradera och skala. Detta är en av de problem som är svårare att lösa, eftersom det handlar om att valet av tekniker. För skalning, förstå hur du partition (eller Fragmentera) både koden och tillstånd är svårt. När koden och tillståndet använder olika tekniker som är vanliga i dag, måste distribution skript för din mikrotjänster för att kunna hantera skalning båda. Detta är även om rörligheten och flexibiliteten, så du kan uppgradera vissa av mikrotjänster utan att behöva uppgradera alla samtidigt.

Gå tillbaka till monolitisk jämfört med mikrotjänster metod för ett ögonblick visar följande diagram skillnaderna i metod för att lagra tillstånd.

#### <a name="state-storage-between-application-styles"></a>Tillstånd lagring mellan program format
![Service Fabric-plattformen lagring av användartillstånd][Image2]

***Metoden monolitisk till vänster har en databas och för specifika tekniker.***

***Mikrotjänster-metoden till höger har ett diagram över sammankopplade mikrotjänster där tillstånd är vanligtvis begränsad till mikrotjänster och olika tekniker används.***

I en monolitisk metod vanligtvis använder programmet en enskild databas. Fördelen är att det är en enda plats, vilket gör det enkelt att distribuera. Varje komponent kan ha en enskild tabell för att lagra dess tillstånd. Team måste separera rent tillstånd, vilket är en utmaning. Det finns oundvikligen temptations att lägga till en ny kolumn i en befintlig tabell för kunden, gör du en koppling mellan tabeller och skapa beroenden i storage-lagret. När det händer kan du skala enskilda komponenter. 

I metoden mikrotjänster varje tjänst hanterar och lagrar sin egen tillstånd. Varje tjänsten ansvarar för att skala både koden och tillstånd tillsammans för att uppfylla kraven för tjänsten. En Nackdelen är att när det är nödvändigt att skapa vyer eller frågor av programdata, måste du fråga i olika tillstånd butiker. Detta är normalt lösta genom att ha en separat mikrotjänster som skapar en vy över en mängd mikrotjänster. Om du behöver utföra flera improviserat frågor på informationen bör varje mikrotjänster dess data skrivs till en datatjänst datalagring för analys av offline.

Versionshantering är specifik för den distribuerade versionen av en mikrotjänster så att flera olika versioner distribuera och köra sida vid sida. Versionshantering adresser scenarier där en nyare version av en mikrotjänster misslyckas under uppgraderingen och behöver återställa till en tidigare version. Det andra scenariot för versionshantering utför testning av A/B-typ, där olika användare ha olika versioner av tjänsten. Det är till exempel vanligt att uppgradera en mikrotjänster för en specifik uppsättning kunder att testa nya funktioner innan den distribueras mer brett. Efter Livscykelhantering för mikrotjänster ger oss för kommunikationen mellan dem i det här nu.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Samverkar med andra mikrotjänster via väldefinierade gränssnitt och protokoll
Det här avsnittet behöver lite åtgärdas här, eftersom omfattande dokumentation om tjänstorienterad arkitektur som har publicerats under de senaste tio åren beskriver kommunikationsmönster. Tjänstkommunikation använder vanligtvis en REST-metod med HTTP- och TCP-protokoll och XML- eller JSON som serialiseringsformat. Från ett gränssnitt perspektiv handlar om omfattar Designmetoden web. Men ingenting slutar du från att använda binärt protokoll eller egna dataformat. Att förbereda för personer som ska vara svårare att använda din mikrotjänster om dessa protokoll och format inte är öppet tillgängliga.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Har ett unikt namn (URL) som används för att matcha dess plats
Kom ihåg hur vi hålla om att mikrotjänster-metoden är exempel på webben? Din mikrotjänster måste vara adresserbara där den körs som på webben. Om du funderar på datorer och vilket som kör en viss mikrotjänster, går saker felaktiga snabbt. 

Din mikrotjänster måste ha ett unikt namn så att dess aktuella plats är synlig på samma sätt som att DNS matchar en viss URL till en viss dator. Mikrotjänster behöver adresserbara namn som gör dem oberoende av den infrastruktur som de körs på. Detta innebär att det finns en interaktion mellan hur din tjänst har distribuerats och hur det upptäcks, eftersom det måste finnas ett register för tjänsten. Lika, när en dator misslyckas registry-tjänsten måste berättar där tjänsten körs nu. 

Detta leder fram till nästa avsnitt: konsekvent och återhämtning.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Förblir konsekventa och tillgängliga med fel
Oväntade fel som behandlar är en av de svåraste problem att lösa, särskilt i ett distribuerat system. Mycket av koden som vi skriva som utvecklare hanterar undantag och det är också där de mest tid det tar för testning. Problemet är mer komplicerat än att skriva kod för att hantera fel. Vad händer när den dator där mikrotjänster körs inte? Inte bara behöver du kunna identifiera felet mikrotjänster (hårda fel på en egen), men också alldeles för att starta om din mikrotjänster. 

En mikrotjänster måste vara motståndskraftiga mot fel och starta om ofta på en annan dator tillgänglighet skäl. Det kommer också till det tillstånd som har sparats för mikrotjänster, där mikrotjänster kan återställa det här tillståndet från, och om mikrotjänster kan starta korrekt. Med andra ord, måste det finnas återhämtning i den beräknade (processen startar) samt återhämtning i den region eller data (ingen dataförlust och data förblir konsekventa).

Problem med återhämtning är beredda under andra scenarier, till exempel när fel inträffa under en uppgradering av programmet. Mikrotjänster, arbetar med distributionssystem, behöver inte återställa. Det måste också sedan bestämmer om den kan fortsätta att gå vidare till den nya versionen eller i stället återställa till en tidigare version att bibehålla en konsekvent tillstånd. Frågor, till exempel om tillräckligt många datorer är tillgängliga för att hålla glidande framåt och återställa tidigare versioner av mikrotjänster måste beaktas. Detta kräver mikrotjänster att generera hälsoinformation om du vill kunna se dessa beslut.

### <a name="reports-health-and-diagnostics"></a>Rapporter hälsa och diagnostik
Det kan verka uppenbart och förbises ofta, men en mikrotjänster måste rapportera dess hälsa och diagnostik. Annars finns lite insikt från ett operations perspektiv. Korrelerar diagnostiska händelser över en uppsättning oberoende tjänster samt hantera datorn jämföra plats i ordningen som händelsen är svårt. På samma sätt som du kommunicerar med en mikrotjänster över överenskomna protokoll och dataformat uppstår det ett behov av i hur man loggar hälsa och diagnostik händelser som slutligen hamna i ett Arkiv för händelse för frågor och visa. I en mikrotjänster metod är den nyckel som olika teamen komma överens om en enda loggningsformat. Det måste finnas en konsekvent metod för att visa diagnostiska händelser i programmet i helhet.

Hälsotillstånd skiljer sig från diagnostik. Hälsotillstånd handlar om mikrotjänster reporting det aktuella tillståndet att vidta lämpliga åtgärder. Ett bra exempel arbetar med uppgradering och distribution mekanismer för att underhålla tillgänglighet. Även om en tjänst kan för närvarande inte hälsosamt eftersom en process krasch eller datorn startas om, kanske tjänsten fortfarande fungerar. Det sista du behöver är att göra detta försämras genom att utföra en uppgradering. Det bästa sättet är att göra en undersökning först eller ge tid för mikrotjänster att återställa. Hälsotillstånd händelser från en mikrotjänster hjälp oss att fatta välgrundade beslut och innebär att skapa självläkande tjänster.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric som en mikrotjänster plattform
Azure Service Fabric kläckas från en övergång av Microsoft från leverera rutan produkter, som vanligtvis monolitisk i style att leverera tjänster. Erfarenhet av byggnad och drift av stora tjänster, till exempel Azure SQL Database och Azure Cosmos DB Formats Service Fabric. Plattformen som utvecklats med tiden fler och fler tjänster antas den. Service Fabric hade allt köra inte bara i Azure, men även i fristående Windows Server-distributioner.

***Syftet med Service Fabric är att lösa hårda problem för att skapa och köra en tjänst och använda infrastrukturresurser effektivt, så att team kan hitta lösningar med hjälp av en mikrotjänster-metod.***

Service Fabric innehåller tre områden för att hjälpa dig att bygga program som använder en mikrotjänster metod:

* En plattform som tillhandahåller systemtjänster om du vill distribuera, uppgradera, identifiera, och starta om misslyckade tjänster, identifiera tjänster, vidarebefordra meddelanden, hantera tillstånd och övervaka hälsotillstånd. System i kraft som möjliggör många av egenskaperna hos mikrotjänster som beskrevs tidigare.
* Möjlighet att distribuera program antingen i behållare eller som processer som körs. Service Fabric är en behållare och processen orchestrator.
* Produktiv programmering API: er, som hjälper dig att bygga program som mikrotjänster: [ASP.NET Core och Reliable Actors Reliable Services](service-fabric-choose-framework.md). Du kan välja någon kod för att skapa din mikrotjänster. Men dessa API: er gör jobbet enklare och de integrera med en djupare nivå plattformen. På så sätt kan till exempel kan du få information om hälsa och diagnostik eller du kan dra nytta av inbyggd hög tillgänglighet.

***Du kan använda alla tekniken Service Fabric är storleksoberoende på hur du skapar din tjänst. Det ger dock inbyggda programming API: er som gör det enklare att skapa mikrotjänster.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrera befintliga program till Service Fabric
En nyckel till Service Fabric är att återanvända befintliga kod, som kan sedan moderniseras med nya mikrotjänster. Det finns fem faser till programmet modernisering och du kan starta och stoppa på någon av stegen. Dessa är;

1) Ta en traditionell monolitisk program  
2) Lyfter och SKIFT - använder behållare eller gäst körbara filer som värd för befintliga koden i Service Fabric.  
3) Modernisering - nya mikrotjänster läggs tillsammans med befintliga av koden.  
4) Förnya - Bryt den monolitisk i mikrotjänster enbart baseras på behov.  
5) Omvandlas till mikrotjänster - omvandling av befintliga monolitisk program eller skapa nya greenfield program.

![Migrering till Mikrotjänster][Image3]

Det är viktigt att betona igen som du har **starta och stoppa på någon av dessa steg**. Du är inte tvingad att gå vidare till nästa steg. Låt oss nu titta på exempel för var och en av dessa steg.

**Lyfta och flytta** -stort antal företag lyfta och rörliga befintliga monolitisk program i behållare av två skäl.

- Minskade kostnader antingen på grund av konsolidering och borttagning av befintlig maskinvara eller kör program på tätare. 
- Konsekvent distribution kontrakt mellan Utvecklingsavdelningen och åtgärder.

Kostnad minskning är att förstå och på Microsoft tillhandahåller många befintliga program är som container bara om du vill spara miljontals kronor. Konsekvent distribution är svårare att utvärdera, men som lika viktiga. Det innebär att utvecklare kan fortfarande vara fritt och välja tekniken som paket dem, men åtgärder endast accepterar ett enskilt sätt att distribuera och hantera dessa program. Det minskar åtgärder från behöva hantera komplexa många olika tekniker eller tvingas att bara välja vissa utvecklare. I princip alla program är behållare i självständiga distributions-avbildningar.

Många organisationer stoppa här. De har redan fördelarna med behållare och Service Fabric ger fullständig hantering från distribution, uppgraderingar, versionshantering, återställningar, hälsa övervakning osv.

**Modernisering** -är att lägga till nya tjänster tillsammans med befintliga av koden. Om du kommer att skriva ny kod, är det bäst att besluta att vidta åtgärder för små till mikrotjänster sökvägen. Detta kan lägger till en ny REST API-slutpunkt, eller nya affärslogik. På så sätt kan du starta transporten av skapa nya mikrotjänster och praxis som utvecklar och distribuerar dem.

**Förnya** -spara de ursprungliga ändra affärsbehov i början av den här artikeln som kör metoden mikrotjänster? Det här steget beslut är dessa är sker till mitt aktuella program och i så fall behöver starta dela monolitvolym eller införa. Här ett exempel är när en databas blir en flaskhals för bearbetning, eftersom den används som en arbetsflödeskö. Antalet arbetsflöden som behöver begäranden ökning av arbetet distribueras för att skala. Så för att viss typ av program som inte skalning, eller om du behöver uppdatera oftare, dela detta ut i en mikrotjänster och förnya. 

**Omvandlas till mikrotjänster** -är där programmet är helt består av (eller indelas i) mikrotjänster. Du har gjort mikrotjänster transporten för att nå här. Du kan börja här, men om du vill göra detta utan en mikrotjänster plattform för att du är en betydande investering. 

### <a name="are-microservices-right-for-my-application"></a>Är mikrotjänster för Mina program?
Kanske. Vi stötte var att många av dem insåg fördelarna med ett mikrotjänster-liknande sätt som fler och fler team i Microsoft började skapa för molnet för affärsskäl. Bing, till exempel har utvecklat mikrotjänster i Sök efter år. Mikrotjänster-metoden är ny för andra team. Team upptäcktes att det var problem med svårt att lösa utanför de viktiga områdena av styrka. Det är därför Service Fabric erfarenheter drivande som teknik för att skapa tjänster.

Syftet med Service Fabric är att minska svårigheter för att skapa program med en mikrotjänster, så att du inte behöver gå igenom som många kostsamma redesigns. Börja litet, skala vid behov, inaktualisera tjänster, lägga till nya och utvecklas med kunden är en metod. Vi vet att det finns många andra problem som kan lösas om du vill göra mikrotjänster mer användarvänligt för de flesta utvecklare. Behållare och aktören programmeringsmodell är exempel på små steg i den riktningen och vi vill att flera innovationer ska du se ett mönster för att göra det enklare.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
* [Översikt över Service Fabric-terminologi](service-fabric-technical-overview.md)
* [Mikrotjänster: Ett program-revolutionen har drivs av molnet](https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
