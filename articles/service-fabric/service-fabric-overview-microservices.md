---
title: Introduktion till mikrotjänster på Azure | Microsoft Docs
description: En översikt över att skapa molnprogram med ett mikrotjänstperspektiv Varför är viktiga för moderna programutveckling och hur Azure Service Fabric tillhandahåller en plattform för att uppnå detta.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcb52165c7cae18b807eff03c80b51eae8e2717
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204795"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Varför ska jag använda ett mikrotjänstperspektiv att bygga program?

För programutvecklare är ta hänsyn till ett program i komponentdelar inget nytt. Vanligtvis används en nivåbaserad metod som med en backend-butik, affärslogik på mellannivå och en frontend användargränssnittet (UI). Vad *har* ändrats de senaste åren är att utvecklare skapar distribuerade program för molnet.

Här följer några varierande affärsbehov:

* En tjänst som har skapats och drivs i stor skala för att nå kunder i nya geografiska regioner.
* Snabbare leverans av funktioner och möjligheter att svara på kundkrav på ett smidigt sätt.
* Bättre resursutnyttjande att minska kostnaderna.

Dessa affärsbehov påverkar *hur* vi bygga program.

Läs mer om Azure-metoden till mikrotjänster [Mikrotjänster: En application revolution som drivs i molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitisk kontra metod för mikrotjänster

Program som utvecklas med tiden. Framgångsrika program utvecklas genom att vara praktiskt att personer. Misslyckade program utvecklas inte och så småningom är inaktuella. Här är frågan: hur mycket vet du om dina krav idag och vad de ska vara i framtiden? Anta exempelvis att du skapar en reporting program för en avdelning i företaget. Är du säker på att programmet gäller endast inom omfånget för ditt företag och att rapporterna inte hållas lång. Din kommer att skiljer sig från den av, säg, att skapa en tjänst som levererar videoinnehåll till tiotals miljoner kunder.

Ibland kan komma åt något ut som ett konceptbevis är drivande faktorn. Du vet att du kan vara designats om programmet senare. Det finns lite punkten i tekniska över något som aldrig används. När företag skapa för molnet, är förväntar sig att växa och användning. Tillväxt och skala är oförutsägbar. Vi vill prototyp snabbt samtidigt som du vet också att vi är på en sökväg som kan hantera fortsatta framgång. Detta är den smidiga Start-metoden: bygg, mät, Läs och iterera.

Under klient/server-era görs vi fokusera på att skapa nivåindelade program med hjälp av specifika tekniker i varje nivå. Termen *monolitisk* program är här för att beskriva dessa metoder. Gränssnitt som är avsett att vara mellan nivåerna och en mer tätt kopplade design användes mellan komponenter inom varje nivå. Utvecklare utformad och inberäknade klasser som har kompileras i bibliotek och länkas samman i några körbara filer och DLL-filer.

Det finns fördelar med att en metod för den monolitiska utformningen. Oflexibla tillämpningar är ofta enklare att utforma och anrop mellan komponenterna är snabbare eftersom dessa anrop är ofta över kommunikation mellan processer (IPC). Dessutom testar alla en enda produkt, som kan vara en mer effektiv användning av personal. Nackdelen är att det finns ett nära koppling mellan nivåindelade lager och du inte skala enskilda komponenter. Om du behöver göra korrigeringar och uppgraderingar kan behöva du vänta så att andra kan slutföra deras testning. Det är svårare för att effektivt.

Mikrotjänster åtgärda dessa nackdelarna med och mer överensstämmer nära med de föregående verksamhetskrav. Men de har också både fördelar och skulder. Fördelarna med mikrotjänster är att var och en vanligtvis innehåller enklare affärsfunktioner som du kan skala upp eller ned, testa, distribuera och hantera oberoende av varandra. En viktig fördel med ett mikrotjänstperspektiv är att team styrs mer genom att affärsscenarier än av teknik. Mindre team utveckla en mikrotjänst baserat på ett och använda alla tekniker som de vill använda.

Organisationen behöver alltså inte att standardisera teknisk för att upprätthålla mikrotjänstprogram. Enskilda team att egna tjänster kan göra vad passar dem. baserat på kunskap team eller vad som är mest lämpliga för att lösa problemet. I praktiken är en uppsättning rekommenderade tekniker, som ett visst NoSQL-Arkiv eller webbprogramsramverk, kan bättre.

Nackdelen av mikrotjänster är att du behöver hantera mer separata entiteter och hantera mer komplexa distributioner och versionshantering. Nätverkstrafiken mellan mikrotjänster ökar, som gör motsvarande nätverksfördröjningar. Många av trafikintensiva, detaljerade tjänster kan orsaka prestanda utmaning. Utan verktyg för att visa dessa beroenden, är det svårt att se hela systemet.

Standarder göra mikrotjänster arbetet genom att ange hur du kommunicerar och att de saker som du behöver från en tjänst, i stället för fasta kontrakt. Det är viktigt att definiera dessa kontrakt direkt i utformningen eftersom services uppdatera oberoende av varandra. En annan beskrivning uttrycket för hur man designar med ett mikrotjänstperspektiv är ”detaljerade tjänstorienterad arkitektur (SOA)”.

***I sin enklaste handlar design mikrotjänstperspektiv om en fristående federation av tjänster, med oberoende ändringar för varje och överenskomna standarder för kommunikation.***

Eftersom mer molnprogram produceras har personer identifierat att den här uppdelning av övergripande programmet till oberoende, scenario designmiljöer tjänster är en bättre långsiktig strategi.

## <a name="comparison-between-application-development-approaches"></a>Jämförelse mellan programutveckling närmar sig

![Programutveckling för Service Fabric-plattformen][Image1]

1) Normalt är uppdelad i funktionella lager som webb-, affärs- och ett monolitiskt program eftersom det innehåller domän-specifika funktioner.

2) Du skalar ett monolitiskt program genom att klona den på flera servrar/virtuella datorer/behållare.

3) En mikrotjänst program separerar funktioner i separata mindre tjänster.

4) Mikrotjänster metoden skalas ut genom att distribuera respektive tjänst oberoende av varandra, skapa instanser av dessa tjänster via servrar/virtuella datorer/behållare.

Designa med en mikrotjänster metoden är inte lämplig för alla projekt, men uppfyller närmare affärsmål som beskrivs ovan. Från och med en monolitiska metoden kan vara klokt om du vet att du har möjlighet att omarbeta koden senare till en. Vanligare, börja med ett monolitiskt program och dela långsamt upp stegvis, från och med funktionsområden som måste vara mer skalbar eller flexibel.

När du använder ett mikrotjänstperspektiv kan skapa du ditt program av många små tjänster. De här tjänsterna körs i behållare som distribueras på ett kluster med datorer. Utveckla en tjänst som fokuserar på ett scenario med mindre team och testa oberoende versionen, distribuera och skala varje tjänst så att hela programmet kan utvecklas.

## <a name="what-is-a-microservice"></a>Vad är en mikrotjänst?

Det finns olika definitioner av mikrotjänster. Men de flesta av dessa egenskaper för mikrotjänster är certifikatformatet:

* Kapslar in ett kunden eller scenario. Vilka problem löser du?
* Utvecklat av en liten tekniker.
* Skrivna i alla programmeringsspråk, med valfritt ramverk.
* Består av kod och du kan också tillstånd, som båda är självständig, distribueras och skalas.
* Interagera med andra mikrotjänster via väldefinierade gränssnitt och protokoll.
* Ha unika namn (URL: er) som används för att matcha deras plats.
* Förbli konsekventa och tillgängliga om det förekommer fel.

Ska summeras som:

***Mikrotjänstprogram består av små, självständig och skalbara kund-fokuserade tjänster som kommunicerar med varandra via standardprotokoll med väldefinierade gränssnitt.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Skrivna i alla programmeringsspråk, med valfritt ramverk

Som utvecklare vill vi ha möjlighet att välja ett annat språk eller ramverk, beroende på vår kunskaper och behov av tjänsten som vi skapar. För vissa tjänster kanske du värdet prestandafördelarna med C++ ovan något annat. För andra hanterade utveckling som du får från enkel C# eller Java kan vara viktigare. I vissa fall kan behöva du använda en viss partner-biblioteket eller datalagringstekniken metod för att visa tjänster till klienter.

När du har valt en teknik behöver du tänka på för driftdatabasen eller livscykeln för hantering och skalning av tjänsten.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Tillåter kod och tillståndet är självständig, distribueras och skalas

Oavsett hur du skriver dina mikrotjänster koden och du kan också tillstånd, bör oberoende distribuera, uppgradera och skala. Det här problemet är svårt att lösa eftersom det handlar om att ett urval av tekniker. För skalning, förstå hur du partition (eller shardnyckeln) både koden och tillståndet är en utmaning. När koden och tillstånd använder olika tekniker, som är vanliga i dag, måste distribueringsskripten för dina mikrotjänster för att kunna skala dem båda. Den här separationen är också om rörligheten och flexibiliteten, så du kan uppgradera vissa av mikrotjänster utan att behöva uppgradera alla på samma gång.

Nu ska vi gå tillbaka till våra jämförelse av metoder monolitisk och mikrotjänster Stopp. Det här diagrammet visar skillnaderna mellan de olika sätten att lagra tillstånd:

#### <a name="state-storage-for-the-two-approaches"></a>Statliga lagring för de två metoderna

![Service Fabric-plattformen state-lagringsutrymme][Image2]

***Den monolitiska metoden har till vänster, en databas och nivåer av specifika tekniker.***

***Mikrotjänster-metoden har till höger, ett diagram över sammankopplade mikrotjänster där tillstånd är vanligtvis begränsad till mikrotjänst och olika tekniker som används.***

I en monolitiska metoden använder programmet en enskild databas. Fördelen med att använda en databas är att det är i en enda plats, vilket gör det enkelt att distribuera. Varje komponent kan ha en enskild tabell för att lagra sitt tillstånd. Team behöver strikt separera tillstånd, vilket är en utmaning. Oundvikligen, kommer någon att tro att lägga till en kolumn i en befintlig kundtabell, göra en koppling mellan tabeller och skapa beroenden i storage-lagret. När detta inträffar kan skala du inte enskilda komponenter.

Mikrotjänster-metoden kan varje tjänst hanterar och lagrar den egna systemtillståndet. Varje tjänst är ansvarig för att skala både kod och tillstånd tillsammans för att uppfylla kraven från tjänsten. En nackdel är när du behöver skapa vyer eller frågor av programdata, måste du fråga över flera butiker i tillståndet. Det här problemet är vanligtvis kan lösas genom en separat mikrotjänst som bygger en vy över en samling av mikrotjänster. Om du vill köra flera improviserat frågor på data bör du skriva varje mikrotjänst data till en olika tjänst för analys av offline.

Mikrotjänster är en ny version. Det är möjligt för olika versioner av en mikrotjänst att sida vid sida. En nyare version av en mikrotjänst kan misslyckas vid en uppgradering och behöver återställas till en tidigare version. Versionshantering är också bra för A / B-testning, där olika användare få olika versioner av tjänsten. Det är till exempel vanligt att uppgradera en mikrotjänst för en specifik uppsättning kunderna att testa nya funktioner innan den distribueras mer brett.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Samverkar med andra mikrotjänster via väldefinierade gränssnitt och protokoll

Under de senaste 10 åren har omfattande information publicerats som beskriver kommunikationsmönster i tjänst-orienterade arkitekturer. Tjänstkommunikation använder normalt en REST-metod med HTTP- och TCP-protokoll och XML eller JSON som serialiseringsformatet. En ur gränssnittet handlar det om att utföra en metod för webben. Men ingenting bör du inte längre med hjälp av binär protokoll eller dina egna dataformat. Bara vara medveten om att personer som har en svårare tid med dina mikrotjänster om dessa protokoll och format är inte öppet tillgängliga.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Har ett unikt namn (URL) som används för att lösa dess plats

Din mikrotjänst måste vara adresserbara oavsett var den körs. Om du tänker om virtuella datorer och vilken som kör en viss mikrotjänst, går saker felaktiga snabbt.

På samma sätt som att DNS matchar en viss URL till en viss dator, måste din mikrotjänst ett unikt namn så att dess nuvarande plats är synliga. Mikrotjänster måste adresserbara namn som är oberoende av den infrastruktur som de körs på. Detta innebär att det finns en interaktion mellan hur din tjänst har distribuerats och hur den identifieras, eftersom det måste finnas ett tjänstregister. När en dator misslyckas, måste berättar där tjänsten har flyttats till registry-tjänsten.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Förblir konsekventa och tillgängliga om det förekommer fel

Hantering av oväntade fel är en av de svåraste problemen för att lösa, särskilt i ett distribuerat system. Mycket av koden som vi skriva som utvecklare är för att hantera undantag. Under testningen ta vi också mest tid på undantagshantering. Processen är mer komplicerat än att skriva kod för att hantera fel. Vad händer när den datorn som kör mikrotjänst misslyckas? Du måste identifiera fel, vilket är svåra problem på egen hand. Men du måste också starta om din mikrotjänst.

En mikrotjänst måste vara elastisk vid fel och starta om på en annan dator för tillgänglighet. Förutom kraven återhämtning data bör inte förlorade och data behöver förblir konsekventa.

Elasticitet är svårt att uppnå när fel uppstår under en uppgradering av programmet. Mikrotjänst, arbeta med distributionssystem behöver inte återställa. Den måste avgöra om den kan fortsätta att gå vidare till en nyare version eller återställa en tidigare version att upprätthålla ett konsekvent tillstånd. Du behöver tänka på några frågor, t.ex. om tillräckligt många datorer är tillgängliga för att flytta framåt och hur du kan återställa tidigare versioner av mikrotjänst. Om du vill göra dessa beslut måste du mikrotjänst kan generera hälsoinformation.

### <a name="reports-health-and-diagnostics"></a>Rapporter hälsa och diagnostik

Det kan verka uppenbart, och förbises ofta, men en mikrotjänst behöver rapportera dess hälsa och diagnostik. Annars kan ha du lite insikt i dess hälsa ur ett operations. Korrelera diagnostikhändelser över en uppsättning oberoende tjänster och ta itu med datorn klockavvikelser faller orderns händelse, är en utmaning. På samma sätt som du interagerar med en mikrotjänst över överenskomna protokoll och dataformat, måste du standardisera hur du loggar in hälso- och som slutligen kommer hamnar i ett händelselager för frågor och visa. Med ett mikrotjänstperspektiv måste olika team komma överens om en enda loggningsformat. Det måste finnas en konsekvent metod för att visa diagnostikhändelser i programmet som helhet.

Hälsotillstånd skiljer sig från diagnostik. Hälsotillstånd handlar om mikrotjänst reporting det aktuella tillståndet vidtar lämpliga åtgärder. Ett bra exempel fungerar uppgradering och distribution mekanismer för att upprätthålla tillgänglighet. Även om en tjänst kanske inte felfri på grund av en processkrasch eller machine omstart, kanske tjänsten fortfarande fungerar. Det sista du behöver är att göra situationen sämre genom att starta en uppgradering. Den bästa metoden är att undersöka först eller väntar tills mikrotjänst att återställa. Health-händelser från en mikrotjänst hjälper oss att fatta välgrundade beslut och i praktiken hjälper dig att skapa självåterställande tjänster.

## <a name="guidance-for-designing-microservices-on-azure"></a>Vägledning för att utforma mikrotjänster på Azure 
Besök Azure architecture center för vägledning på [utforma och skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric som en plattform för mikrotjänster

Azure Service Fabric kläckas när Microsoft övergick från leverera Inramad produkter, som vanligtvis monolitisk att leverera tjänster. Upplevelsen av att bygga och driva stora tjänster, till exempel Azure SQL Database och Azure Cosmos DB kan utformas på Service Fabric. Plattformen som utvecklats med tiden fler tjänster antas den. Service Fabric var tvungen att köra inte bara i Azure, utan även i fristående Windows Server-distributioner.

***Syftet med Service Fabric är att lösa de svåra problem med att skapa och köra en tjänst och kan använda infrastrukturresurser effektivt, så att teamen kan lösa affärsproblem med hjälp av ett mikrotjänstperspektiv.***

Service Fabric hjälper dig att bygga program som använder ett mikrotjänstperspektiv genom att tillhandahålla:

* En plattform som tillhandahåller systemtjänster om du vill distribuera, uppgradera, upptäcka och starta om misslyckade tjänsterna, identifiera tjänster, dirigera meddelanden, hantera tillstånd och övervaka hälsa.
* Möjligheten att distribuera program antingen körs i behållare eller processer. Service Fabric är en behållare och processen orchestrator.
* Produktiv programmering API: er för att hjälpa dig att bygga program som mikrotjänster: [ASP.NET Core, tillförlitliga aktörer och Reliable Services](service-fabric-choose-framework.md). Exempel: du kan få information om hälsa och diagnostik eller du kan dra nytta av inbyggd hög tillgänglighet.

***Service Fabric är oberoende om hur du skapar din tjänst och du kan använda teknik. Men det ger inbyggd programming API: er som gör det enklare att skapa mikrotjänster.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrering av befintliga program till Service Fabric

Service Fabric kan du återanvända befintlig kod och modernisera med nya mikrotjänster. Det finns fem faser till programmodernisering och du kan starta och stoppa under alla stadier. Stegen är:

1) Börja med en traditionell monolitiskt program.  
2) Migrera. Använda behållare eller körbara gäster som värd för befintliga koden i Service Fabric.  
3) Modernisera. Lägga till nya mikrotjänster tillsammans med befintliga behållare koden.  
4) Skapa. Bryt monolitisk App i mikrotjänster efter behov.  
5) Omvandla program till mikrotjänster. Omvandla befintliga monolitiskt program eller utveckla nya med grönt fält.

![Migrering till mikrotjänster][Image3]

Kom ihåg att du kan *starta och stoppa på någon av dessa steg*. Du behöver inte kan fortsätta till nästa steg. 

Nu ska vi titta på exempel för var och en av dessa steg.

**Migrera**  
Två skäl migrerar många företag befintliga monolitiskt program i behållare:

* Minskade kostnader, antingen på grund av konsolidering och borttagning av befintlig maskinvara eller på grund av att köra program med högre densitet.
* Ett konsekvent distribution avtal mellan utveckling och drift.

Kostnad sänkt är enkelt. På Microsoft är många befintliga program som behållare, vilket leder till miljontals dollar i besparingar. Konsekvent distribution är svårare att utvärdera men lika viktigt. Det innebär att utvecklare kan välja de tekniker som passar dem, men operations accepterar bara en enda metod för att distribuera och hantera program. Det minskar åtgärder från behöva arbeta med komplexiteten med stöd för olika tekniker utan att utvecklare kan välja att bara vissa värden. I princip alla program finns i en behållare i fristående distributionsavbildningar.

Många organisationer slutar här. De har redan fördelarna med behållare och Service Fabric innehåller den fullständiga hanteringsupplevelsen, inklusive distribution, uppgraderingar, versionshantering, återställningar och hälsoövervakning.

**Modernize**  
Modernisering är att lägga till nya tjänster tillsammans med befintliga behållare koden. Om du planerar att skriva ny kod, är det bäst att ta små steg vidare mikrotjänster. Detta kan innebära att lägga till en ny REST API-slutpunkt eller nya affärslogik. På så sätt kan starta du processen med att skapa nya mikrotjänster och praxis utveckla och distribuera dem.

**Utveckla**  
Ett mikrotjänstperspektiv kan hantera varierande affärsbehov. I det här skedet måste du bestämma om du vill starta dela monolitisk App i tjänster eller innovation. Här ett exempel är när en databas som du använder som ett arbetsflöde för kön blir en flaskhals för bearbetning. Eftersom antalet arbetsflödet begär ökar, måste arbetet som ska distribueras för att skala. Ta den viss typ av program som inte skalning eller som behöver uppdateras oftare, och delas ut som en mikrotjänst och utveckla.

**Omvandla program i mikrotjänster**  
I det här skedet programmet är helt består av (eller dela upp till) mikrotjänster. Du har gjort mikrotjänster vägen för att nå den här punkten. Du kan börja här om du vill göra det utan en mikrotjänster plattform som hjälper dig att kräver dock en betydande investering.

### <a name="are-microservices-right-for-my-application"></a>Är mikrotjänster behörighet för mitt program?

Kanske. På Microsoft, som fler lag började skapa för molnet för affärsskäl, realiserade många av dem fördelarna med att utföra en mikrotjänst-liknande metod. Bing, till exempel har använt mikrotjänster i år. För andra grupper var mikrotjänstperspektiv nya. Team upptäcktes att det var problem med svårt att lösa utanför deras viktigaste områdena inom styrka. Det är därför Service Fabric fått dirigera som teknik för att skapa tjänster.

Målet med Service Fabric är att minska komplexiteten i att skapa mikrotjänstprogram så att du inte behöver gå igenom så många kostsamma redesigns. Börja i liten skala, skala vid behov, avverka tjänster, lägga till nya och utvecklas med KUNDANVÄNDNING. Vi vet att det finns många andra problem med att lösas om du vill göra mikrotjänster mer programmera för de flesta utvecklare. Behållare och aktören programmeringsmodell är exempel på steg på den fronten. Vi vet mer innovationer kommer dyker upp för att göra ett mikrotjänstperspektiv enklare.


## <a name="next-steps"></a>Nästa steg

* [Mikrotjänster: En application revolution som drivs i molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Metodtips för Azure Service Fabric-program och -kluster](service-fabric-best-practices-overview.md)
* [Översikt över Service Fabric-terminologi](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
