---
title: Introduktion till mikrotjänster på Azure
description: En översikt över varför det är viktigt att skapa molnprogram med en mikrotjänstmetod för modern programutveckling och hur Azure Service Fabric tillhandahåller en plattform för att uppnå detta.
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: af18a6cb45808c0af5ec2782a3fd2100e3b7bf99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750630"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Varför använda en mikrotjänstmetod för att skapa program

För mjukvaruutvecklare är factoring ett program i komponenter inget nytt. Vanligtvis används en nivåindelad metod, med ett backend-arkiv, affärslogik på mellannivå och ett användargränssnitt (frontend user interface). Det *som har* förändrats under de senaste åren är att utvecklare bygger distribuerade program för molnet.

Här är några förändrade affärsbehov:

* En tjänst som byggs och drivs i stor skala för att nå kunder i nya geografiska regioner.
* Snabbare leverans av funktioner och funktioner för att svara på kundernas krav på ett flexibelt sätt.
* Förbättrad resursutnyttjande för att minska kostnaderna.

Dessa affärsbehov påverkar *hur* vi bygger applikationer.

Mer information om Azure-metoden för mikrotjänster finns i [Mikrotjänster: En programrevolution som drivs av molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/).

## <a name="monolithic-vs-microservices-design-approach"></a>Monolitisk kontra mikrotjänster design metod

Program utvecklas med tiden. Framgångsrika program utvecklas genom att vara användbara för människor. Misslyckade program utvecklas inte och är så småningom inaktuella. Här är frågan: hur mycket vet du om dina krav idag och vad de kommer att vara i framtiden? Anta till exempel att du skapar en rapporteringsansökan för en avdelning i företaget. Du är säker på att programmet endast gäller inom företagets omfattning och att rapporterna inte kommer att sparas länge. Din strategi kommer att skilja sig från, säg, bygga en tjänst som levererar videoinnehåll till tiotals miljoner kunder.

Ibland, att få ut något genom dörren som ett proof of concept är den drivande faktorn. Du vet att programmet kan göras om senare. Det är ingen mening med att över-engineering något som aldrig får användas. Å andra sidan, när företag bygger för molnet, är förväntningarna tillväxt och användning. Tillväxt och skala är oförutsägbara. Vi vill prototyp snabbt samtidigt veta att vi är på en väg som kan hantera framtida framgång. Detta är lean startup strategi: bygga, mäta, lära och iterera.

Under klient-server-eran tenderade vi att fokusera på att skapa nivåindelade program med hjälp av specifika tekniker på varje nivå. Termen *monolitisk* tillämpning har dykt upp för att beskriva dessa metoder. Gränssnitten tenderade att vara mellan nivåerna, och en mer tätt kopplad design användes mellan komponenter inom varje nivå. Utvecklare utformade och vägde klasser som kompilerades till bibliotek och länkades samman till några körbara filer och DLL-filer.

Det finns fördelar med en monolitisk design strategi. Monolitiska program är ofta enklare att utforma, och anrop mellan komponenter är snabbare eftersom dessa samtal ofta är över interprocesskommunikation (IPC). Dessutom testar alla en enda produkt, som tenderar att vara en mer effektiv användning av mänskliga resurser. Nackdelen är att det finns en tät koppling mellan nivåindelade lager, och du kan inte skala enskilda komponenter. Om du behöver göra korrigeringar eller uppgraderingar, måste du vänta på andra att avsluta sina tester. Det är svårare att vara smidig.

Mikrotjänster adresserar dessa nackdelar och ligger närmare varandra i förhållande till föregående affärskrav. Men de har också både fördelar och skulder. Fördelarna med mikrotjänster är att var och en vanligtvis kapslar in enklare affärsfunktioner, som du kan skala upp eller ned, testa, distribuera och hantera oberoende av varandra. En viktig fördel med en mikrotjänstmetod är att teamen drivs mer av affärsscenarier än av teknik. Mindre team utvecklar en mikrotjänst baserat på ett kundscenario och använder alla tekniker som de vill använda.

Med andra ord behöver organisationen inte standardisera teknik för att underhålla mikrotjänstprogram. Enskilda team som äger tjänster kan göra vad som är meningsfullt för dem baserat på teamets expertis eller vad som är mest lämpligt för att lösa problemet. I praktiken är en uppsättning rekommenderade tekniker, som en viss NoSQL-butik eller webbapplikationsramverk, att föredra.

Nackdelen med mikrotjänster är att du måste hantera mer separata entiteter och hantera mer komplexa distributioner och versionshantering. Nätverkstrafiken mellan mikrotjänsterna ökar, liksom motsvarande nätverksdynningar. Massor av pratsamma, granulat tjänster kan orsaka en prestanda mardröm. Utan verktyg som hjälper dig att visa dessa beroenden är det svårt att se hela systemet.

Standarder gör mikrotjänster strategi arbete genom att ange hur man kommunicerar och tolererar bara de saker du behöver från en tjänst, snarare än stela kontrakt. Det är viktigt att definiera dessa kontrakt i form i designen eftersom tjänsterna uppdateras oberoende av varandra. En annan beskrivning som myntas för att utforma med en mikrotjänstmetod är "finkornig serviceinriktad arkitektur (SOA)."

***I sin enklaste, mikrotjänster design strategi handlar om en frikopplad federation av tjänster, med oberoende ändringar av varje och överenskomna standarder för kommunikation.***

När fler molnprogram produceras har människor upptäckt att den här nedbrytningen av det övergripande programmet till oberoende, scenariofokuserade tjänster är en bättre långsiktig metod.

## <a name="comparison-between-application-development-approaches"></a>Jämförelse mellan metoder för applikationsutveckling

![Utveckling av programutveckling för Service Fabric-plattformen][Image1]

1) Ett monolitisk program innehåller domänspecifika funktioner och är normalt indelat i funktionella lager som webb, företag och data.

2) Du skalar ett monolitiskt program genom att klona det på flera servrar/virtuella datorer/behållare.

3) Ett mikrotjänstprogram separerar funktionaliteten i separata mindre tjänster.

4) Metoden för mikrotjänster skalas ut genom att distribuera varje tjänst oberoende av varandra, vilket skapar instanser av dessa tjänster mellan servrar/virtuella datorer/behållare.

Design med en mikrotjänstmetod är inte lämplig för alla projekt, men det stämmer bättre överens med de affärsmål som beskrivits tidigare. Från och med en monolitisk metod kan vara meningsfullt om du vet att du har möjlighet att omarbeta koden senare till en mikrotjänst design. Vanligare, du börjar med en monolitisk ansökan och långsamt bryta upp det i etapper, börjar med de funktionella områden som behöver vara mer skalbara eller smidig.

När du använder en mikrotjänstmetod skriver du din tillämpning av många små tjänster. Dessa tjänster körs i behållare som distribueras över ett kluster av datorer. Mindre team utvecklar en tjänst som fokuserar på ett scenario och självständigt testar, version, distribuerar och skalar varje tjänst så att hela programmet kan utvecklas.

## <a name="what-is-a-microservice"></a>Vad är en mikrotjänst?

Det finns olika definitioner av mikrotjänster. Men de flesta av dessa egenskaper hos mikrotjänster är allmänt accepterade:

* Kapsla in ett kund- eller affärsscenario. Vilket problem löser du?
* Utvecklad av ett litet ingenjörsteam.
* Skrivet på alla programmeringsspråk, med hjälp av alla ramar.
* Består av kod och eventuellt tillstånd, som båda är oberoende av versionsversioner, distribueras och skalas.
* Interagera med andra mikrotjänster över väldefinierade gränssnitt och protokoll.
* Har unika namn (URL:er) som används för att matcha deras plats.
* Förbli konsekvent och tillgänglig i närvaro av fel.

Sammanfattningsvis:

***Mikrotjänstprogram består av små, oberoende versionerade och skalbara kundfokuserade tjänster som kommunicerar med varandra via standardprotokoll med väldefinierade gränssnitt.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Skrivet på alla programmeringsspråk, med hjälp av någon ram

Som utvecklare vill vi vara fria att välja ett språk eller ramverk, beroende på våra färdigheter och behoven hos den tjänst som vi skapar. För vissa tjänster kan du värdera prestandafördelarna med C++ över allt annat. För andra kan den enkla hanterade utveckling som du får från C # eller Java vara viktigare. I vissa fall kan du behöva använda ett visst partnerbibliotek, datalagringsteknik eller en metod för att exponera tjänsten för klienter.

När du har valt en teknik måste du överväga drift- eller livscykelhantering och skalning av tjänsten.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Gör att kod och tillstånd kan versionsas, distribueras och skalas oberoende av kod

Oavsett hur du skriver dina mikrotjänster, koden, och eventuellt tillståndet, bör självständigt distribuera, uppgradera och skala. Detta problem är svårt att lösa eftersom det handlar om ditt val av teknik. För skalning är det svårt att förstå hur du partitionerar (eller fragmentar) både koden och tillståndet. När koden och tillståndet använder olika tekniker, vilket är vanligt i dag, måste distributionsskripten för din mikrotjänst kunna skala dem båda. Denna separation handlar också om smidighet och flexibilitet, så du kan uppgradera några av mikrotjänsterna utan att behöva uppgradera dem alla på en gång.

Låt oss återgå till vår jämförelse av monolitiska och mikrotjänster metoder för ett ögonblick. Det här diagrammet visar skillnaderna i metoderna för lagring av tillstånd:

#### <a name="state-storage-for-the-two-approaches"></a>Statlig lagring för de två tillvägagångssätten

![Lagring av plattformstillstånd för Service Fabric][Image2]

***Den monolitiska metoden, till vänster, har en enda databas och nivåer av specifik teknik.***

***Metoden för mikrotjänster till höger har ett diagram över sammankopplade mikrotjänster där tillståndet vanligtvis begränsas till mikrotjänsten och olika tekniker används.***

I en monolitisk metod använder programmet vanligtvis en enda databas. Fördelen med att använda en databas är att den är på en enda plats, vilket gör det enkelt att distribuera. Varje komponent kan ha en enda tabell för att lagra sitt tillstånd. Lagen måste strikt separera staten, vilket är en utmaning. Oundvikligen kommer någon att frestas att lägga till en kolumn i en befintlig kundtabell, göra en koppling mellan tabeller och skapa beroenden på lagringslagret. När detta händer kan du inte skala enskilda komponenter.

I metoden mikrotjänster hanterar och lagrar varje tjänst sitt eget tillstånd. Varje tjänst ansvarar för att skala både kod och tillstånd tillsammans för att möta kraven från tjänsten. En nackdel är att när du behöver skapa vyer, eller frågor, av programmets data, måste du fråga över flera tillståndsarkiv. Det här problemet löses vanligtvis av en separat mikrotjänst som bygger en vy över en samling mikrotjänster. Om du behöver köra flera improviserade frågor om data bör du överväga att skriva varje mikrotjänsts data till en datalagringstjänst för offlineanalys.

Mikrotjänster är versionsversionerade. Det är möjligt för olika versioner av en mikrotjänst att köra sida vid sida. En nyare version av en mikrotjänst kan misslyckas under en uppgradering och måste återställas till en tidigare version. Versionshantering är också användbart för A / B-testning, där olika användare upplever olika versioner av tjänsten. Det är till exempel vanligt att uppgradera en mikrotjänst för en viss uppsättning kunder för att testa nya funktioner innan du distribuerar den i större utsträckning.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Interagerar med andra mikrotjänster över väldefinierade gränssnitt och protokoll

Under de senaste 10 åren har omfattande information publicerats som beskriver kommunikationsmönster i serviceinriktade arkitekturer. I allmänhet använder tjänstkommunikation en REST-metod med HTTP- och TCP-protokoll och XML eller JSON som serialiseringsformat. Ur ett gränssnittsperspektiv handlar det om att ta en webbdesign strategi. Men ingenting bör hindra dig från att använda binära protokoll eller dina egna dataformat. Tänk bara på att människor kommer att ha svårare att använda dina mikrotjänster om dessa protokoll och format inte är öppet tillgängliga.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Har ett unikt namn (URL) som används för att matcha dess plats

Din mikrotjänst måste vara adresserbar var den än körs. Om du tänker på maskiner och vilken som kör en viss mikrotjänst, kan det gå dåligt snabbt.

På samma sätt som DNS matchar en viss URL till en viss dator behöver din mikrotjänst ett unikt namn så att dess aktuella plats kan identifieras. Mikrotjänster behöver adresserbara namn som är oberoende av den infrastruktur de körs på. Detta innebär att det finns en interaktion mellan hur din tjänst distribueras och hur den upptäcks, eftersom det måste finnas ett tjänstregister. När en dator misslyckas måste registertjänsten tala om var tjänsten har flyttats till.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Förblir konsekvent och tillgänglig i närvaro av fel

Att hantera oväntade fel är ett av de svåraste problemen att lösa, särskilt i ett distribuerat system. Mycket av koden som vi skriver som utvecklare är för hantering av undantag. Under testningen lägger vi också mest tid på undantagshantering. Processen är mer involverad än att skriva kod för att hantera fel. Vad händer när datorn som mikrotjänsten körs på misslyckas? Du måste upptäcka felet, vilket är ett svårt problem på egen hand. Men du måste också starta om din mikrotjänst.

För tillgänglighet måste en mikrotjänst vara motståndskraftig mot fel och kunna starta om på en annan dator. Utöver dessa krav på återhämtning bör data inte gå förlorade och data måste vara konsekventa.

Återhämtning är svårt att uppnå när fel inträffar under en programuppgradering. Mikrotjänsten, som arbetar med distributionssystemet, behöver inte återställas. Den måste avgöra om den kan fortsätta att gå vidare till den nyare versionen eller återställa till en tidigare version för att upprätthålla ett konsekvent tillstånd. Du måste överväga några frågor, till exempel om det finns tillräckligt med datorer för att fortsätta framåt och hur du återställer tidigare versioner av mikrotjänsten. För att fatta dessa beslut behöver du mikrotjänsten för att avge hälsoinformation.

### <a name="reports-health-and-diagnostics"></a>Rapporter hälsa och diagnostik

Det kan tyckas självklart, och det är ofta förbises, men en mikrotjänst måste rapportera sin hälsa och diagnostik. Annars har du liten inblick i dess hälsa ur ett verksamhetsperspektiv. Korrelera diagnostiska händelser över en uppsättning oberoende tjänster, och hantera fel på maskinklockan för att förstå händelseordningen, är utmanande. På samma sätt som du interagerar med en mikrotjänst över överenskomna protokoll och dataformat måste du standardisera hur du loggar hälso- och diagnostikhändelser som i slutändan hamnar i ett händelsearkiv för att fråga och visa. Med en mikrotjänstmetod måste olika team komma överens om ett enda loggningsformat. Det måste finnas en konsekvent metod för att visa diagnostiska händelser i programmet som helhet.

Hälsa skiljer sig från diagnostik. Hälsa handlar om att mikrotjänsten rapporterar sitt aktuella tillstånd för att vidta lämpliga åtgärder. Ett bra exempel är att arbeta med uppgraderings- och distributionsmekanismer för att upprätthålla tillgängligheten. Även om en tjänst för närvarande kan vara felaktig på grund av en processkrasch eller omstart av datorn, kan tjänsten fortfarande vara i drift. Det sista du behöver är att göra situationen värre genom att starta en uppgradering. Det bästa sättet är att undersöka först eller ge tid för mikrotjänsten att återhämta sig. Hälsohändelser från en mikrotjänst hjälper oss att fatta välgrundade beslut och i själva verket bidra till att skapa självläkande tjänster.

## <a name="guidance-for-designing-microservices-on-azure"></a>Vägledning för att utforma mikrotjänster på Azure

Besök Azure-arkitekturcentret för vägledning om [hur du utformar och skapar mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric som mikrotjänstplattform

Azure Service Fabric uppstod när Microsoft övergick från att leverera förpackade produkter, som vanligtvis var monolitiska, till att leverera tjänster. Erfarenheten av att skapa och driva stora tjänster, som Azure SQL Database och Azure Cosmos DB, formad Service Fabric. Plattformen utvecklades med tiden när fler tjänster antog den. Service Fabric var tvungen att köras inte bara i Azure utan även i fristående Windows Server-distributioner.

***Syftet med Service Fabric är att lösa de svåra problemen med att bygga och driva en tjänst och att använda infrastrukturresurser effektivt, så att team kan lösa affärsproblem med hjälp av en mikrotjänstmetod.***

Service Fabric hjälper dig att skapa program som använder en mikrotjänstmetod genom att tillhandahålla:

* En plattform som tillhandahåller systemtjänster för att distribuera, uppgradera, identifiera och starta om misslyckade tjänster, identifiera tjänster, dirigera meddelanden, hantera tillstånd och övervaka hälsotillstånd.
* Möjligheten att distribuera program som antingen körs i behållare eller som processer. Service Fabric är en behållare och process orchestrator.
* Api:er för produktiv programmering som hjälper dig att skapa program som mikrotjänster: [ASP.NET Core, Reliable Actors och Reliable Services](service-fabric-choose-framework.md). Du kan till exempel få information om hälso- och diagnostik, eller så kan du dra nytta av inbyggd hög tillgänglighet.

***Service Fabric är agnostiker om hur du bygger din tjänst, och du kan använda vilken teknik som helst. Men det ger inbyggda programmering API: er som gör det lättare att bygga mikrotjänster.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Migrera befintliga program till Service Fabric

Service Fabric kan du återanvända befintlig kod och modernisera den med nya mikrotjänster. Det finns fem steg till applikationsmodernisering, och du kan starta och stoppa när som helst. Stadierna är:

1) Börja med en traditionell monolitisk applikation.  
2) Migrera. Använd behållare eller gästblanter för att vara värd för befintlig kod i Service Fabric.  
3) Modernisera. Lägg till nya mikrotjänster tillsammans med befintlig containerkod.  
4) Innovera. Bryt det monolitiska programmet i mikrotjänster baserat på behov.  
5) Omvandla program till mikrotjänster. Omvandla befintliga monolitiska program eller skapa nya greenfield-program.

![Migrering till mikrotjänster][Image3]

Kom ihåg att du kan *börja och stanna vid någon av dessa steg*. Du behöver inte gå vidare till nästa steg. 

Låt oss titta på exempel för var och en av dessa steg.

**Migrera**  
Av två skäl migrerar många företag befintliga monolitiska applikationer till behållare:

* Kostnadsminskning, antingen på grund av konsolidering och borttagning av befintlig maskinvara eller på grund av program med högre densitet.
* Ett konsekvent distributionskontrakt mellan utveckling och verksamhet.

Kostnadsminskningar är enkla. På Microsoft, många befintliga program håller på att containerized, vilket leder till miljontals dollar i besparingar. Konsekvent distribution är svårare att utvärdera men lika viktigt. Det innebär att utvecklare kan välja de tekniker som passar dem, men åtgärder accepterar bara en enda metod för att distribuera och hantera programmen. Det lindrar verksamheten från att behöva ta itu med komplexiteten i att stödja olika tekniker utan att tvinga utvecklare att välja endast vissa. I huvudsak är varje program containerized till fristående distributionsavbildningar.

Många organisationer stannar här. De har redan fördelarna med behållare och Service Fabric ger fullständig hanteringsupplevelse, inklusive distribution, uppgraderingar, versionshantering, återställningar och hälsoövervakning.

**Modernisera**  
Modernisering är tillägg av nya tjänster tillsammans med befintlig containeriserad kod. Om du ska skriva ny kod är det bäst att ta små steg ner för mikrotjänstsökvägen. Detta kan innebära att lägga till en ny REST API-slutpunkt eller ny affärslogik. På så sätt startar du processen med att skapa nya mikrotjänster och öva på att utveckla och distribuera dem.

**Förnya**  
En mikrotjänstmetod tillgodoser förändrade affärsbehov. I detta skede måste du bestämma om du vill börja dela upp det monolitiska programmet i tjänster eller nyskapande. Ett klassiskt exempel här är när en databas som du använder som en arbetsflödeskö blir en flaskhals för bearbetning. När antalet arbetsflödesbegäranden ökar måste arbetet fördelas för skala. Ta den delen av programmet som inte skalas, eller som behöver uppdateras oftare, och dela upp den som en mikrotjänst och förnya.

**Omvandla program till mikrotjänster**  
I det här skedet består ditt program helt av (eller delas upp i) mikrotjänster. För att nå denna punkt, har du gjort mikrotjänster resa. Du kan börja här, men att göra det utan en mikrotjänst plattform för att hjälpa dig kräver en betydande investering.

### <a name="are-microservices-right-for-my-application"></a>Är mikrotjänster rätt för min ansökan?

Kanske. På Microsoft, när fler team började bygga för molnet av affärsmässiga skäl, insåg många av dem fördelarna med att ta en microservice-liknande strategi. Bing har till exempel använt mikrotjänster i flera år. För andra team var mikrotjänstmetoden ny. Team fann att det fanns svåra problem att lösa utanför sina kärnområden av styrka. Det är därför Service Fabric fick dragkraft som teknik för att bygga tjänster.

Syftet med Service Fabric är att minska komplexiteten i att bygga mikrotjänstapplikationer så att du inte behöver gå igenom så många kostsamma omkonstruktioner. Starta små, skala när det behövs, föråldra tjänster, lägg till nya och utvecklas med kundanvändning. Vi vet också att det finns många andra problem som ännu inte lösts för att göra mikrotjänster mer lättillgängliga för de flesta utvecklare. Behållare och aktören programmering modell är exempel på små steg i den riktningen. Vi är säkra på att fler innovationer kommer att dyka upp för att göra en mikrotjänst strategi lättare.

## <a name="next-steps"></a>Nästa steg

* [Mikrotjänster: En programrevolution som drivs av molnet](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Skapa mikrotjänster på Azure](https://docs.microsoft.com/azure/architecture/microservices/)
* [Metodtips för Azure Service Fabric-program och kluster](service-fabric-best-practices-overview.md)
* [Översikt över terminologi för Service Fabric](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png
