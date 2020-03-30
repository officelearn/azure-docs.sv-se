---
title: Översikt över programmeringsmodellen för tillförlitliga tjänster
description: Lär dig mer om Service Fabric's Reliable Service programmeringsmodell och kom igång med att skriva dina egna tjänster.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083518"
---
# <a name="reliable-services-overview"></a>Översikt över Reliable Services

Azure Service Fabric förenklar skrivning och hantering av tillståndslösa och tillståndskänsliga tjänster. I detta avsnitt:

* Programmeringsmodellen För tillförlitliga tjänster för tillståndslösa och tillståndskänsliga tjänster.
* De val du måste göra när du skriver en tillförlitlig tjänst.
* Vissa scenarier och exempel på när du ska använda tillförlitliga tjänster och hur de skrivs.

*Reliable Services* är en av de programmeringsmodeller som finns på Service Fabric. En annan är *den tillförlitliga aktören* programmeringsmodell, som ger en [virtuell aktör](https://research.microsoft.com/en-us/projects/orleans/) programram ovanpå Reliable Services-modellen. Mer information om reliable actors finns i [Introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric hanterar livslängden för tjänster, från etablering och distribution till uppgradering och borttagning, via [Service Fabric-programhantering](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Vad är tillförlitliga tjänster

Reliable Services ger dig en enkel, kraftfull programmeringsmodell på högsta nivå som hjälper dig att uttrycka vad som är viktigt för ditt program. Med programmeringsmodellen Reliable Services får du:

* Tillgång till API:er för serviceinfrastruktur. Till skillnad från Service Fabric-tjänster som modelleras som [gäst körbara](service-fabric-guest-executables-introduction.md)filer kan reliable services använda Service Fabric API:er direkt. Detta gör det möjligt för tjänster att:
  * Fråga systemet
  * Rapportera hälsotillstånd om entiteter i klustret
  * Ta emot meddelanden om konfiguration och kodändringar
  * Hitta och kommunicera med andra tjänster,
  * Använd tillförlitliga [samlingar](service-fabric-reliable-services-reliable-collections.md)
  * Få tillgång till många andra funktioner, allt från en förstklassig programmeringsmodell i flera programmeringsspråk.
* En enkel modell för att köra din egen kod som känns som andra välbekanta programmeringsmodeller. Koden har en väldefinierad startpunkt och en lätthanterad livscykel.
* En plugable kommunikationsmodell. Använd valfri transport, till exempel HTTP med [webb-API,](service-fabric-reliable-services-communication-webapi.md)WebSockets, anpassade TCP-protokoll eller något annat. Reliable Services erbjuder några bra färdiga alternativ som du kan använda, eller så kan du tillhandahålla dina egna.
* För tillståndskänsliga tjänster kan du med programmeringsmodellen Reliable Services konsekvent och tillförlitligt lagra ditt tillstånd direkt i tjänsten med hjälp av [Tillförlitliga samlingar.](service-fabric-reliable-services-reliable-collections.md) Reliable Collections är en enkel uppsättning högtillgängliga och tillförlitliga insamlingsklasser som kommer att vara bekanta för alla som har använt C# samlingar. Traditionellt behövde tjänster externa system för tillförlitlig statlig hantering. Med tillförlitliga samlingar kan du lagra ditt tillstånd bredvid din beräkning med samma höga tillgänglighet och tillförlitlighet som du har kommit att förvänta dig från externa butiker med hög tillgänglighet. Den här modellen förbättrar också svarstiden eftersom du samlokaliserar den beräkning och tillstånd som den behöver för att fungera.

## <a name="what-makes-reliable-services-different"></a>Vad gör reliable services annorlunda

Reliable Services skiljer sig från tjänster som du kan ha skrivit tidigare, eftersom Service Fabric tillhandahåller:

* **Tillförlitlighet** - Tjänsten förblir uppe även i opålitliga miljöer där dina datorer misslyckas eller träffar nätverksproblem, eller i de fall där tjänsterna själva stöter på fel och kraschar eller misslyckas. För tillståndskänsliga tjänster bevaras ditt tillstånd även i närvaro av nätverk eller andra fel.
* **Tillgänglighet** - Din tjänst kan nås och svarar. Service Fabric behåller önskat antal kopior som körs.
* **Skalbarhet** - Tjänsterna frikopplas från specifik maskinvara och de kan växa eller krympa vid behov genom tillägg eller borttagning av maskinvara eller andra resurser. Tjänster partitioneras enkelt (särskilt i det tillståndskänsliga fallet) för att säkerställa att tjänsten kan skala och hantera partiella fel. Tjänster kan skapas och tas bort dynamiskt via kod, vilket gör att fler instanser kan snurras upp vid behov, till exempel som svar på kundförfrågningar. Slutligen uppmuntrar Service Fabric tjänster att vara lätta. Service Fabric gör att tusentals tjänster kan etableras inom en enda process, i stället för att kräva eller avsätta hela OS-instanser eller processer till en enda instans av en tjänst.
* **Konsekvens** - All information som lagras i en tillförlitlig tjänst kan garanteras vara konsekvent. Detta gäller även för flera tillförlitliga samlingar inom en tjänst. Ändringar mellan samlingar inom en tjänst kan göras på ett transaktionellt atomärt sätt.

## <a name="service-lifecycle"></a>Tjänstens livscykel

Oavsett om din tjänst är tillståndskänslig eller tillståndslös tillhandahåller reliable services en enkel livscykel som gör att du snabbt kan koppla in koden och komma igång.  För att få igång en ny tjänst måste du implementera två metoder:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - Den här metoden är där tjänsten definierar de kommunikationsstaplar som den vill använda. Kommunikationsstacken, till exempel [webb-API,](service-fabric-reliable-services-communication-webapi.md)är det som definierar lyssningsslutpunkten eller slutpunkterna för tjänsten (hur klienter når tjänsten). Den definierar också hur de meddelanden som visas interagerar med resten av servicekoden.
* **RunAsync** - Den här metoden är där din tjänst kör sin affärslogik och där den skulle starta alla bakgrundsuppgifter som ska köras under tjänstens livstid. Annulleringstoken som tillhandahålls är en signal för när det arbetet ska sluta. Om tjänsten till exempel behöver hämta meddelanden från en tillförlitlig kö och bearbeta dem, är det här det arbetet sker.

Om du lär dig om tillförlitliga tjänster för första gången, läs vidare! Om du letar efter en detaljerad genomgång av livscykeln för tillförlitliga tjänster kan du läsa [översikt över reliable services livscykel](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exempel på tjänster

Låt oss ta en närmare titt på hur reliable services-modellen fungerar med både tillståndslösa och tillståndskänsliga tjänster.

### <a name="stateless-reliable-services"></a>Tillståndslösa tillförlitliga tjänster

En *tillståndslös tjänst* är en tjänst där det inte finns något tillstånd som upprätthålls inom tjänsten över samtal. Alla tillstånd som finns är helt disponibel och kräver inte synkronisering, replikering, persistens eller hög tillgänglighet.

Tänk dig till exempel en kalkylator som inte har något minne och tar emot alla villkor och åtgärder för att utföra på en gång.

I det här `RunAsync()` fallet kan `runAsync()` tjänstens (C#) eller (Java) vara tomma, eftersom det inte finns någon bakgrundsaktivitetsbearbetning som tjänsten behöver göra. När räknartjänsten skapas returneras `ICommunicationListener` en (C#) eller `CommunicationListener` (Java) (till exempel [webb-API)](service-fabric-reliable-services-communication-webapi.md)som öppnar en lyssningsslutpunkt på vissa portar. Den här lyssningsslutpunkten ansluts till de olika beräkningsmetoderna (exempel: "Add(n1, n2)") som definierar räknarens offentliga API.

När ett samtal görs från en klient anropas lämplig metod och räknartjänsten utför åtgärderna på de data som tillhandahålls och returnerar resultatet. Den lagrar ingen stat.

Inte lagra något internt tillstånd gör detta exempel kalkylator enkel. Men de flesta tjänster är inte riktigt statslösa. Istället externaliserar de sitt tillstånd till någon annan butik. (Alla webbappar som är beroende av att hålla sessionstillståndet i ett stödarkiv eller cache är till exempel inte tillståndslösa.)

Ett vanligt exempel på hur tillståndslösa tjänster används i Service Fabric är som en frontend som exponerar det offentliga API:et för ett webbprogram. Front-end-tjänsten talar sedan med tillståndskänsliga tjänster för att slutföra en användarbegäran. I det här fallet dirigeras samtal från klienter till en känd port, till exempel 80, där den tillståndslösa tjänsten lyssnar. Den här tillståndslösa tjänsten tar emot anropet och avgör om samtalet kommer från en betrodd part och vilken tjänst den är avsedd för.  Sedan vidarebefordrar den tillståndslösa tjänsten anropet till rätt partition för den tillståndskänsliga tjänsten och väntar på ett svar. När den tillståndslösa tjänsten får ett svar svarar den på den ursprungliga klienten. Ett exempel på en sådan tjänst är exempel på *Service Fabric Komma igång* [(C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)), bland andra Service Fabric-exempel i repoen.

### <a name="stateful-reliable-services"></a>Tillståndskänsliga tillförlitliga tjänster

En *tillståndskänslig tjänst* är en tjänst som måste ha en del av tillståndet hålls konsekvent och närvarande för att tjänsten ska fungera. Överväg en tjänst som ständigt beräknar ett rullande medelvärde av ett visst värde baserat på uppdateringar som den tar emot. För att göra detta måste den ha den aktuella uppsättningen inkommande begäranden som den behöver bearbeta och det aktuella genomsnittet. Alla tjänster som hämtar, bearbetar och lagrar information i ett externt arkiv (till exempel en Azure-blob eller tabellarkiv idag) är tillståndskänsliga. Det håller bara sitt tillstånd i den externa staten butiken.

De flesta tjänster lagrar idag sitt tillstånd externt, eftersom det externa arkivet är det som ger tillförlitlighet, tillgänglighet, skalbarhet och konsekvens för det tillståndet. I Service Fabric behöver tjänsterna inte lagra sitt tillstånd externt. Service Fabric tar hand om dessa krav för både servicekoden och servicetillståndet.

Låt oss säga att vi vill skriva en tjänst som bearbetar bilder. För att göra detta tar tjänsten in en bild och serien med konverteringar som ska utföras på den bilden. Den här tjänsten returnerar en kommunikationsavlyssnare (låt oss anta `ConvertImage(Image i, IList<Conversion> conversions)`att det är en WebAPI) som exponerar ett API som . När den tar emot en begäran `IReliableQueue`lagrar tjänsten den i ett och returnerar ett visst ID till klienten så att den kan spåra begäran.

I den `RunAsync()` här tjänsten kan vara mer komplex. Tjänsten har en loop `RunAsync()` inuti den som `IReliableQueue` hämtar begäranden ur och utför de begärda konverteringarna. Resultaten får lagras `IReliableDictionary` i en så att när klienten kommer tillbaka de kan få sina konverterade bilder. Om du vill vara säker på att även om något misslyckas går avbildningen inte förlorad, drar den här tillförlitliga tjänsten ut ur kön, utför konverteringarna och lagrar resultatet i en enda transaktion. I det här fallet tas meddelandet bort från kön och resultaten lagras i resultatordlistan endast när konverteringarna är slutförd. Alternativt kan tjänsten dra bilden ur kön och omedelbart lagra den i ett fjärrlager. Detta minskar mängden tillstånd som tjänsten har att hantera, men ökar komplexiteten eftersom tjänsten måste behålla nödvändiga metadata för att hantera fjärrarkivet. Med någon av tillvägagångssätten, om något misslyckades i mitten begäran kvar i kön väntar på att bearbetas.

Även om den här tjänsten låter som en typisk .NET-tjänst, `IReliableDictionary`är skillnaden att de datastrukturer som används (och)`IReliableQueue` tillhandahålls av Service Fabric och är mycket tillförlitliga, tillgängliga och konsekventa.

## <a name="when-to-use-reliable-services-apis"></a>När du ska använda Reliable Services API:er

Tänk på api:er för tillförlitliga tjänster om:

* Du vill att tjänstens kod (och eventuellt tillstånd) ska vara mycket tillgänglig och tillförlitlig.
* Du behöver transaktionsgarantier över flera enheter av tillstånd (till exempel order och orderradartiklar).
* Programmets tillstånd kan naturligtvis modelleras som tillförlitliga ordlistor och köer.
* Programkoden eller ditt programtillstånd måste vara mycket tillgängligt med läs- och skrivningar med låg latens.
* Ditt program måste styra samtidigheten eller granulariteten för verkställde åtgärder över en eller flera tillförlitliga samlingar.
* Du vill hantera kommunikationen eller styra partitioneringsschemat för din tjänst.
* Din kod behöver en kostnadsfri körningsmiljö.
* Ditt program måste dynamiskt skapa eller förstöra tillförlitliga ordlistor eller köer eller hela tjänster vid körning.
* Du måste programmatiskt styra Funktioner för säkerhetskopiering och återställning av Service Fabric för tjänstens tillstånd.
* Ditt program måste underhålla ändringshistoriken för sina tillståndsenheter.
* Du vill utveckla eller konsumera tredjepartsutvecklade, anpassade tillståndsleverantörer.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för tillförlitliga tjänster](service-fabric-reliable-services-quick-start.md)
* [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
* [Programmeringsmodellen Reliable Actors](service-fabric-reliable-actors-introduction.md)
