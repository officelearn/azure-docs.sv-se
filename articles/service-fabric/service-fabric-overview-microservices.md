---
title: Introduktion till mikrotjänster på Azure | Microsoft Docs
description: En översikt över att skapa molnprogram med ett mikrotjänstperspektiv Varför är viktiga för moderna programutveckling och hur Azure Service Fabric tillhandahåller en plattform för att uppnå detta.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: fae2be85-0ab4-4cd3-9d1f-e0d95fe1959b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: msfussell
ms.openlocfilehash: 04342be06430747ef64cb69c27ee93e6896775e5
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070891"
---
# <a name="why-a-microservices-approach-to-building-applications"></a>Varför använda mikrotjänster för att bygga program?
Som programutvecklare finns det inget nytt i hur vi tror att om ta hänsyn till ett program i komponentdelar. Är det centrala paradigmet av Objektorientering och programvara abstraktioner Komponentindelning. Idag är tenderar det här factorization att vara i formatet av klasser och gränssnitt mellan delade bibliotek och tekniklager. Vanligtvis är ett nivåindelat tillvägagångssätt upptaget med en backend-butik, affärslogik på mellannivå och en frontend användargränssnittet (UI). Vad *har* ändrats de senaste åren är att vi, som utvecklare är att skapa distribuerade program för molnet och drivs av verksamheten.

Varierande affärsbehov är:

* En tjänst som bygger och fungerar i stor skala för att nå kunder i nya geografiska regioner (till exempel).
* Snabbare leverans av funktioner och möjligheter för att kunna svara på kundkrav på ett smidigt sätt.
* Bättre resursutnyttjande att minska kostnaderna.

Dessa affärsbehov påverkar *hur* vi bygga program.

Mer information om metoden Azure för att mikrotjänster [Mikrotjänster: en application revolution som drivs i molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservice-design-approach"></a>Monolitisk jämfört med mikrotjänst Designmetoden
Alla program som utvecklas med tiden. Framgångsrika program utvecklas genom att vara praktiskt att personer. Misslyckade program att utvecklas inte och så småningom är inaktuella. Frågan blir: hur mycket vet du om dina krav idag och vad de är i framtiden? Anta exempelvis att du skapar en reporting program för en avdelning. Du är säker på att programmet fortfarande är inom omfånget för ditt företag och att rapporterna är tillfällig. Ditt val av metoden skiljer sig från, säg, att skapa en tjänst som levererar videoinnehåll till tiotals miljoner kunder. 

Ibland kan komma åt något lansera som konceptbevis är drivande faktorn när du vet att programmet kan vara ny utformning senare. Det finns lite punkten i tekniska över något som aldrig används. Det är vanligt engineering kompromiss. När företagen pratar om att bygga för molnet, är förväntar sig att växa och användning. Problemet är att tillväxt och skala är oförutsägbar. Vi vill kunna skapa prototyper snabbt samtidigt som du vet också att vi finns på en sökväg utan fortsatta framgång. Detta är den smidiga Start-metoden: bygg, mät, Läs och iterera.

Under klient-server-era görs vi fokusera på att skapa nivåindelade program med hjälp av specifika tekniker i varje nivå. Termen *monolitisk* program är här för dessa metoder. Gränssnitt som är avsett att vara mellan nivåerna och en mer tätt kopplade design användes mellan komponenter inom varje nivå. Utvecklare som har utformats och beräknade klasser som har kompileras i bibliotek och länkas samman i några körbara filer och DLL-filer. 

Det finns fördelar med att en sådan metod för den monolitiska utformningen. Det är ofta enklare att utforma och den har snabbare anrop mellan komponenter, eftersom dessa anrop är ofta över kommunikation mellan processer (IPC). Dessutom testar alla en enda produkt, som kan vara mer personer-resurs effektivt. Nackdelen är att det finns ett nära koppling mellan nivåindelade lager och du inte skala enskilda komponenter. Om du behöver utföra korrigeringar och uppgraderingar kan behöva du vänta så att andra kan slutföra deras testning. Det är svårare att effektivt.

Mikrotjänster åtgärda dessa nackdelarna med och mer överensstämmer nära med de föregående verksamhetskrav, men de har också både fördelar och skulder. Fördelarna med mikrotjänster är att var och en vanligtvis innehåller enklare affärsfunktioner som du skala upp eller ned, testa, distribuera och hantera oberoende av varandra. En viktig fördel med en mikrotjänst metod är att team styrs mer genom att affärsscenarier än av teknik som uppmuntrar att denna riskbedömning. I praktiken mindre team utveckla en mikrotjänst baserat på ett och använda alla tekniker som de har valt. 

Organisationen behöver alltså inte att standardisera teknisk för att upprätthålla mikrotjänstprogram. Enskilda team att egna tjänster kan göra vad passar dem. baserat på kunskap team eller vad som är mest lämpliga för att lösa problemet. I praktiken lagra en uppsättning rekommenderade tekniker, till exempel en viss NoSQL eller webbprogramsramverk, bättre.

Nackdelen av mikrotjänster finns i hantera ökad antalet separata entiteter och ta itu med mer komplexa distributioner och versionshantering. Nätverkstrafiken mellan mikrotjänster ökar samt motsvarande nätverksfördröjningar. Många av trafikintensiva, detaljerade tjänster är ett recept för prestanda utmaning. Utan verktyg för att visa dessa beroenden är det svårt att ”se” hela systemet. 

Standarder göra mikrotjänst arbetet genom att hur du kommunicerar och som feltoleranta av de saker som du behöver från en tjänst, i stället för fasta kontrakt. Det är viktigt att definiera dessa kontrakt direkt i designen, eftersom services uppdatera oberoende av varandra. En annan beskrivning uttrycket för hur man designar med ett mikrotjänstperspektiv är ”detaljerade tjänstorienterad arkitektur (SOA)”.

***I sin enklaste handlar design mikrotjänstperspektiv om en fristående federation av tjänster, med oberoende ändringar för varje och överenskomna standarder för kommunikation.***

Eftersom fler molnappar produceras identifiera personer att den här uppdelning av övergripande appen till oberoende, scenario designmiljöer services är en bättre långsiktig strategi.

## <a name="comparison-between-application-development-approaches"></a>Jämförelse mellan programutveckling närmar sig
![Programutveckling för Service Fabric-plattformen][Image1]

1) Normalt har dividerats med funktionella lager, till exempel webb-, affärs- och en monolitisk app eftersom den innehåller domän-specifika funktioner.

2) Du skalar en monolitisk app genom att klona den på flera servrar/virtuella datorer/behållare.

3) En mikrotjänst program separerar funktioner i separata mindre tjänster.

4) Mikrotjänster metoden skalas ut genom att distribuera respektive tjänst oberoende av varandra, skapa instanser av dessa tjänster via servrar/virtuella datorer/behållare.

Designa med en mikrotjänst metoden är inte en panacea för alla projekt, men uppfyller närmare affärsmål som beskrivs ovan. Från och med en monolitiska metoden kan användas om du vet att du har möjlighet att omarbeta koden senare till en. Vanligare, börja med ett monolitiskt program och dela långsamt upp stegvis, från och med funktionsområden som måste vara mer skalbar eller flexibel.

Sammanfattningsvis är den mikrotjänst-metoden att skapa ditt program av många små tjänster. Tjänsterna körs i behållare som distribueras på ett kluster med datorer. Utveckla en tjänst som fokuserar på ett scenario med mindre team och testa oberoende versionen, distribuera och skala varje tjänst så att hela programmet kan utvecklas.

## <a name="what-is-a-microservice"></a>Vad är en mikrotjänst?
Det finns olika definitioner av mikrotjänster. Om du söka på Internet, hittar du många användbara resurser som tillhandahåller egna översiktsvyer och definitioner. Men är de flesta av följande egenskaper för mikrotjänster mycket överenskomna:

* Kapslar in ett kunden eller scenario. Vad är problemet som du vill lösa?
* Utvecklat av en liten tekniker.
* Skrivna i något programmeringsspråk och använda valfritt ramverk.
* Består av kod och (frivilligt) tillstånd, som båda självständig, distribueras och skalas.
* Interagera med andra mikrotjänster via väldefinierade gränssnitt och protokoll.
* Ha unika namn (URL: er) som används för att matcha deras plats.
* Förbli konsekventa och tillgängliga om det förekommer fel.

Du kan sammanfatta följande egenskaper till:

***Mikrotjänstprogram består av små, självständig och skalbara kund-fokuserade tjänster som kommunicerar med varandra via standardprotokoll med väldefinierade gränssnitt.***

Vi har gått igenom de första två punkterna i föregående avsnitt och nu vi expanderar på och förtydliga de andra.

### <a name="written-in-any-programming-language-and-use-any-framework"></a>Skrivna i något programmeringsspråk och använda valfritt ramverk
Som utvecklare kan vara vi själv välja ett annat språk eller ramverk som vi vill att, beroende på vår kunskaper eller behov av tjänsten. I vissa tjänster kanske du värde prestandafördelarna med C++ framför allt annat. I andra tjänster vara enkel hanterad utveckling i C# eller Java viktigaste. I vissa fall kan behöva du använda en viss partner-biblioteket eller datalagringstekniken sätt för att visa tjänster till klienter.

När du har valt en teknik du kommit till den operativa eller lifecycle management och skalning av tjänsten.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Tillåter kod och tillståndet är självständig, distribueras och skalas
Men du vill skriva dina mikrotjänster, koden och eventuellt tillståndet bör oberoende distribuera, uppgradera och skala. Detta är faktiskt en av de problem som är svårare att lösa, eftersom det handlar om att ett urval av tekniker. För skalning, förstå hur du partition (eller shardnyckeln) både kod och tillstånd är en utmaning. När koden och tillstånd använder separata tekniker, som är vanliga i dag, måste distribueringsskripten för dina mikrotjänster för att kunna hantera skalning båda. Detta är även om rörligheten och flexibiliteten, så du kan uppgradera vissa av mikrotjänster utan att behöva uppgradera alla på samma gång.

Tillbaka till monolitisk jämfört med mikrotjänst metod under en kort stund kan visar följande diagram skillnaderna i metod för att lagra tillstånd.

#### <a name="state-storage-between-application-styles"></a>Statliga lagring mellan program format
![Service Fabric-plattformen state-lagringsutrymme][Image2]

***Den monolitiska metoden på vänster sida har en databas och nivåer av specifika tekniker.***

***Mikrotjänstperspektiv till höger har ett diagram över sammankopplade mikrotjänster där tillstånd är vanligtvis begränsad till mikrotjänst och olika tekniker som används.***

I en monolitiska metoden vanligtvis använder programmet en enskild databas. Fördelen är att det är en enda plats, vilket gör det enkelt att distribuera. Varje komponent kan ha en enskild tabell för att lagra sitt tillstånd. Team behöver strikt separera tillstånd, vilket är en utmaning. Det finns oundvikligen temptations att lägga till en ny kolumn i en befintlig kundtabell, göra en koppling mellan tabeller och skapa beroenden i storage-lagret. När detta inträffar kan skala du inte enskilda komponenter. 

Mikrotjänster-metoden kan varje tjänst hanterar och lagrar den egna systemtillståndet. Varje tjänst är ansvarig för att skala både kod och tillstånd tillsammans för att uppfylla kraven från tjänsten. En nackdel är när det är nödvändigt att skapa vyer eller frågor av programdata, måste du fråga över olika tillstånd butiker. Detta är vanligtvis, kan lösas genom att använda en separat mikrotjänst som bygger en vy över en samling av mikrotjänster. Om du behöver utföra flera improviserat frågor på data bör varje mikrotjänst skriva data till en olika tjänst för analys av offline.

Versionshantering är specifik för den distribuerade versionen av en mikrotjänst så att flera olika versioner distribuera och köra sida vid sida. Versionshantering adresser scenarier där en nyare version av en mikrotjänst misslyckas under uppgraderingen och behöver återställa en tidigare version. Det andra scenariot för versionshantering genomför testning av A/B-format, där olika användare få olika versioner av tjänsten. Det är till exempel vanligt att uppgradera en mikrotjänst för en specifik uppsättning kunderna att testa nya funktioner innan den distribueras mer brett. Efter livscykelhantering av mikrotjänster ger oss att kommunikationen mellan dem i det här nu.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Samverkar med andra mikrotjänster via väldefinierade gränssnitt och protokoll
Det här avsnittet kräver lite uppmärksamhet här, eftersom omfattande dokumentation om tjänstorienterad arkitektur som har publicerats under de senaste 10 åren beskriver kommunikationsmönster. Tjänstkommunikation använder normalt en REST-metod med HTTP- och TCP-protokoll och XML eller JSON som serialiseringsformatet. En ur gränssnittet handlar det om omfattar Designmetoden web. Men ingenting slutar du från att använda binära protokoll eller dina egna dataformat. Förberedas för personer som ska vara svårare att med hjälp av dina mikrotjänster om dessa protokoll och format inte är öppet tillgängliga.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Har ett unikt namn (URL) som används för att lösa dess plats
Kom ihåg hur hålla vi säger att mikrotjänst metoden liknar webben? Din mikrotjänst måste vara adresserbara oavsett var den körs som på webben. Om du funderar på datorer och vilken som kör en viss mikrotjänst, går saker felaktiga snabbt. 

Din mikrotjänst måste ha ett unikt namn så att dess nuvarande plats är synliga på samma sätt som att DNS matchar en viss URL till en viss dator. Mikrotjänster behöver adresserbara namn som gör dem oberoende av den infrastruktur som de körs på. Detta innebär att det finns en interaktion mellan hur din tjänst har distribuerats och hur den identifieras, eftersom det måste finnas ett tjänstregister. Lika, när en dator inte registry-tjänsten måste berättar där tjänsten körs nu. 

Detta leder fram till nästa ämne: ökad flexibilitet och konsekvens.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Förblir konsekventa och tillgängliga om det förekommer fel
Hantering av oväntade fel är en av de svåraste problemen för att lösa, särskilt i ett distribuerat system. Mycket av koden som vi skriva som utvecklare hantering av undantag och det är också var mest tid går åt i testet. Problemet är mer komplicerat än att skriva kod för att hantera fel. Vad händer när den dator där mikrotjänst körs misslyckas? Inte bara behöver du identifiera felet mikrotjänst (hårda fel på egen hand), men du måste också något att starta om din mikrotjänst. 

En mikrotjänst måste vara elastisk vid fel och starta om ofta på en annan dator tillgänglighet skäl. Det kommer också till det tillstånd som sparades åt mikrotjänst, där mikrotjänst kan återställa det här tillståndet från, och om mikrotjänst är att starta om har. Med andra ord måste det finnas återhämtning i beräkningen (processen startar) samt återhämtning i den region eller data (inga data går förlorade och data förblir konsekvent).

Problem med återhämtning är beredda under andra scenarier, till exempel när fel inträffa under en uppgradering av programmet. Mikrotjänst, arbeta med distributionssystem behöver inte återställa. Dessutom behövs sedan bestämmer om det kan fortsätta att gå vidare till den nya versionen eller i stället återställa till en tidigare version att upprätthålla ett konsekvent tillstånd. Frågor, till exempel om tillräckligt många datorer är tillgängliga för att flytta framåt och hur du kan återställa tidigare versioner av mikrotjänst måste beaktas. Detta kräver mikrotjänst kan generera hälsoinformation för att kunna fatta följande beslut.

### <a name="reports-health-and-diagnostics"></a>Rapporter hälsa och diagnostik
Det kan verka uppenbart, och förbises ofta, men en mikrotjänst måste rapportera dess hälsa och diagnostik. Annars är lite insikt från en operations perspektiv. Korrelera diagnostikhändelser över en uppsättning oberoende tjänster och ta itu med datorn klockavvikelser faller orderns händelse är en utmaning. På samma sätt som du interagerar med en mikrotjänst över överenskomna protokoll och dataformat, uppstår det ett behov av i hur du loggar in hälso- och som slutligen hamnar i ett händelselager för frågor och visa. I ett mikrotjänstperspektiv, det är viktigt att olika team komma överens om en enda loggningsformat. Det måste finnas en konsekvent metod för att visa diagnostikhändelser i programmet som helhet.

Hälsotillstånd skiljer sig från diagnostik. Hälsotillstånd handlar om mikrotjänst reporting det aktuella tillståndet vidtar lämpliga åtgärder. Ett bra exempel fungerar uppgradering och distribution mekanismer för att upprätthålla tillgänglighet. Även om en tjänst kan vara inte felfri på grund av en processkrasch eller machine omstart, kanske tjänsten fortfarande fungerar. Det sista du behöver är att göra detta sämre genom att utföra en uppgradering. Det bästa sättet är att göra en undersökning först eller väntar tills mikrotjänst att återställa. Health-händelser från en mikrotjänst hjälper oss att fatta välgrundade beslut och i praktiken hjälper dig att skapa självåterställande tjänster.

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric som en plattform för mikrotjänster
Azure Service Fabric kläckas från en övergång av Microsoft från att leverera box-produkter, som vanligtvis monolitisk i style att leverera tjänster. Upplevelsen av att bygga och driva stora tjänster, till exempel Azure SQL Database och Azure Cosmos DB kan utformas på Service Fabric. Plattformen som utvecklats med tiden fler tjänster antas den. Service Fabric hade det viktiga är att köra inte bara i Azure, utan även i fristående Windows Server-distributioner.

***Syftet med Service Fabric är att lösa tuffa problem med att skapa och köra en tjänst och använda resurser i infrastrukturen effektivt, så att teamen kan lösa affärsproblem med hjälp av ett mikrotjänstperspektiv.***

Service Fabric tillhandahåller tre områden för att hjälpa dig att bygga program som använder ett mikrotjänstperspektiv:

* En plattform som tillhandahåller systemtjänster om du vill distribuera, uppgradera, upptäcka och starta om misslyckade tjänsterna, identifiera tjänster, dirigera meddelanden, hantera tillstånd och övervaka hälsa. Dessa systemtjänster kan i praktiken många av egenskaperna för mikrotjänster som beskrivs ovan.
* Möjlighet att distribuera program antingen körs i behållare eller processer. Service Fabric är en behållare och processen orchestrator.
* Produktiv programmering API: er, att hjälpa dig att bygga program som mikrotjänster: [ASP.NET Core och Reliable Actors Reliable Services](service-fabric-choose-framework.md). Du kan välja valfri kod att bygga din mikrotjänst. Men dessa API: er gör jobbet enklare, och de kan integreras med plattformen på en djupare nivå. På så sätt kan till exempel du kan få information om hälsa och diagnostik eller du kan dra nytta av inbyggd hög tillgänglighet.

***Service Fabric är oberoende på hur du skapar din tjänst och du kan använda teknik. Det tillhandahåller dock inbyggda programmeringsspråk API: er som gör det enklare att skapa mikrotjänster.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrering av befintliga program till Service Fabric
En metod som är viktiga för att Service Fabric är att återanvända befintliga kod, som kan sedan moderniserade med nya mikrotjänster. Det finns fem faser till programmodernisering och du kan starta och stoppa på någon av stegen. Dessa är:

1) Börja med en traditionell monolitiskt program.  
2) Lyfta och skifta - använder behållare eller körbara gäster som värd för befintliga koden i Service Fabric.  
3) Modernisering - nya mikrotjänster har lagts till tillsammans med befintliga behållare koden.  
4) Förnya – dela upp den monolitiska i mikrotjänster helt och hållet baserat på behov.  
5) Omvandlas till mikrotjänster - omvandling av befintliga monolitiskt program eller bygga nya med grönt fält-program.

![Migrering till Mikrotjänster][Image3]

Det är viktigt att betona igen som du har **starta och stoppa på någon av dessa steg**. Du är inte vi är tvungna att fortsätta till nästa steg. Nu ska vi nu titta på exempel för var och en av dessa steg.

**Flytta över** -stort antal företag lyftning och växling av befintliga monolitiskt program i behållare av två skäl:

- Minskade kostnader antingen på grund av konsolidering och borttagning av befintliga maskinvara eller som kör program med högre densitet. 
- Konsekvent distribution avtal mellan utveckling och drift.

Kostnad sänkt är att förstå och inom Microsoft, är stort antal befintliga program som behållare enbart för att spara miljontals dollar. Konsekvent distribution är svårare att utvärdera, men som lika viktiga. Det innebär att utvecklare kan fortfarande vara själv välja den teknik som passar dem, men åtgärderna bara ska ta emot ett enskilt sätt att distribuera och hantera dessa program. Det minskar åtgärder från behöva arbeta med komplexiteten i många olika tekniker eller tvinga utvecklare kan bara välja vissa. I stort sett alla program finns i en behållare i fristående distributionsavbildningar.

Många organisationer slutar här. De har redan fördelarna med behållare och Service Fabric innehåller den fullständiga hanteringsupplevelsen från distribution, uppgraderingar, versionshantering, återställningar, health övervakning osv.

**Modernisering** -är att lägga till nya tjänster tillsammans med befintliga behållare koden. Om du ska skriva ny kod, rekommenderas du att du bestämmer dig för att ta små steg vidare mikrotjänster. Det gick att lägga till en ny REST API-slutpunkt, eller nya affärslogik. På så sätt kan du börja på din resa av studiematerial utveckla och distribuera dem och skapa nya mikrotjänster.

**Utveckla** -Kom ihåg att de ursprungliga varierande affärsbehov i början av den här artikeln som driver mikrotjänstperspektiv? På det här skedet beslutet är är dessa händer till mitt aktuella program och i så, fall jag behöver starta dela upp en monolitisk app i tjänster eller innovation. Här ett exempel är när en databas som används som en worflow kö blir en flaskhals för bearbetning. Eftersom antalet arbetsflödet begär ökar, måste arbetet som ska distribueras för att skala. Så för att viss typ av program som inte skalning, eller om du behöver uppdatera oftare kan dela upp detta i en mikrotjänst och innovera. 

**Omvandlas till mikrotjänster** – detta är där ditt program är helt består av (eller delats upp i) mikrotjänster. Du har gjort mikrotjänster vägen för att nå här. Du kan börja här, men om du vill göra detta utan en mikrotjänster är plattformen som hjälper dig att en betydande investering. 

### <a name="are-microservices-right-for-my-application"></a>Är mikrotjänster behörighet för mitt program?
Kanske. Vad vi stötte på var att många av dem eftersom fler och fler team i Microsoft började skapa för molnet för affärsskäl, kan realiserade fördelarna med att ta en mikrotjänst-liknande metod. Bing, till exempel har utvecklat mikrotjänster i Sök efter år. För andra grupper var mikrotjänstperspektiv nya. Team upptäcktes att det var problem med svårt att lösa utanför deras viktigaste områdena inom styrka. Det är därför Service Fabric fått dirigera som teknik med för att skapa tjänster.

Syftet med Service Fabric är att minska komplexiteten i att skapa program med en mikrotjänst-metod, så att du inte behöver gå igenom som många kostsamma redesigns. Börja i liten skala, skala vid behov, avverka tjänster, lägga till nya och utvecklas med kundens användning är metoden. Vi vet att det finns många andra problem med att lösas om du vill göra mikrotjänster mer programmera för de flesta utvecklare. Behållare och aktören programmeringsmodell är exempel på steg i den riktningen och vi vill att flera innovationer kommer dyker upp för att göra det enklare.
 
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Nästa steg
* [Översikt över Service Fabric-terminologi](service-fabric-technical-overview.md)
* [Mikrotjänster: En program-revolution har drivs i molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
