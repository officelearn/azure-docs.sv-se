---
title: Översikt över tillförlitlig tjänst för Service Fabric-programmeringsmodell | Microsoft Docs
description: Lär dig mer om programmeringsmodell för tillförlitlig tjänst för Service Fabric och börja skriva egna tjänster.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 474cc78a4ceb872742ca7eb10837eeb89dcc1bdb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-overview"></a>Översikt över Reliable Services
Azure Service Fabric gör det enklare att skriva och hantera tillståndslösa och tillståndskänsliga Reliable Services. Detta avsnitt:

* Reliable Services programmeringsmiljö för tillståndslösa och tillståndskänsliga tjänster.
* De alternativ som du behöver göra när du skriver en tillförlitlig tjänst.
* Vissa scenarier och exempel på när du ska använda Reliable Services och hur de skrivs.

Reliable Services är en av programmeringsmodeller finns på Service Fabric. Den andra är tillförlitlig aktören programmeringsmiljö, som är en programmeringsmodell med virtuella aktören på Reliable Services-modell. Läs mer på programmeringsmodell för Reliable Actors [introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric hanterar livslängden för tjänster, från etablering och distribution via uppgradering och borttagning, via [Service Fabric programhantering](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Vad är Reliable Services?
Reliable Services ger en enkel, kraftfulla översta programmeringsmodell som hjälper dig att express vad som är viktigt för ditt program. Med tillförlitlig tjänsterna programmeringsmodellen, får du:

* Åtkomst till resten av den Service Fabric programming API: er. Till skillnad från Service Fabric Services modelleras som [gäst körbara filer](service-fabric-guest-executables-introduction.md), Reliable Services komma att använda resten av Service Fabric-API: er direkt. Detta tillåter tjänster:
  * frågan systemet
  * hälsa för rapporten om enheter i klustret
  * få meddelanden om ändringar i konfigurationen och kod
  * hitta och kommunicera med andra tjänster
  * (valfritt) använda den [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
  * ...och ge dem åtkomst till många andra funktioner, allt från en förstklassigt programmeringsmodell i flera programmeringsspråk.
* En enkel modell för att köra din kod som ser ut som programmeringsmodeller som används för att. Koden har en väldefinierad startpunkt och lätthanterade livscykel.
* En modell med pluggable kommunikation. Använda transport av du väljer, till exempel HTTP med [Web API](service-fabric-reliable-services-communication-webapi.md), WebSockets, anpassade TCP-protokoll eller något annat. Reliable Services innehåller några bra out box-alternativ som du kan använda eller ange en egen.
* För tillståndskänsliga tjänster programmeringsmiljö Reliable Services kan du konsekvent och tillförlitligt sätt lagra din tillstånd i din tjänst med hjälp av [tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md). Tillförlitliga samlingar är en enkel uppsättning hög tillgänglighet och tillförlitlig Samlingsklasser som är bekant för alla som har använt C#-samlingar. Traditionellt tjänster som krävs för externa system för tillståndshantering av tillförlitliga. Med tillförlitlig samlingar kan du lagra ditt tillstånd bredvid din beräkning med samma hög tillgänglighet och tillförlitlighet du förväntar dig från högtillgänglig externa butiker. Den här modellen förbättrar också latens eftersom du samordna beräknings- och tillstånd som behövs för att fungera.

Det här videoklippet Microsoft Virtual Academy en översikt över Reliable services: <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>Vad gör Reliable Services olika?
Reliable Services i Service Fabric skiljer sig från tjänster som du har skrivit innan. Service Fabric ger tillförlitlighet, tillgänglighet, konsekvens och skalbarhet.

* **Tillförlitlighet** – din tjänsten förblir in även i instabilt miljöer där dina datorer misslyckas eller tryck på nätverksproblem eller i fall där tjänsterna själva uppstår fel och krascher eller misslyckas. För tillståndskänsliga tjänster bevaras din även med nätverket eller andra fel.
* **Tillgänglighet** -tjänsten är kan nås och svarstid. Service Fabric underhåller din önskade antalet kopior som körs.
* **Skalbarhet** - tjänster är fristående från maskinvara, och de kan öka eller minska vid behov genom tillägg och borttagning av maskinvara eller andra resurser. Tjänster enkelt partitioneras (särskilt i tillståndskänslig fallet) för att säkerställa att tjänsten kan skala och hantera misslyckades delvis. Tjänster kan skapas och tas bort dynamiskt via kod, aktivera flera instanser som du vill att de kan vid behov Säg som svar på kundernas önskemål. Slutligen uppmuntrar Service Fabric att lightweight-tjänster. Service Fabric kan tusentals tjänster som ska etableras i en enda process snarare än kräver eller trafikklass hela OS-instanser eller processer till en enda instans av en tjänst.
* **Konsekvenskontroll** -all information som lagras i den här tjänsten kan garanteras vara konsekvent. Detta gäller även över flera tillförlitliga samlingar i en tjänst. Över samlingarna i en tjänst kan ändras i ett transaktionellt atomiska sätt.

## <a name="service-lifecycle"></a>Tjänsten livscykel
Om din tjänst är tillståndskänslig eller tillståndslösa, tillhandahåller tillförlitlig en enkel livscykel kan du snabbt ansluta din kod och komma igång.  Det finns en eller två metoder som du behöver implementera för att få din tjänst körs.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -den här metoden är där tjänsten definierar kommunikation stack(s) som du vill använda. Kommunikationen stack, till exempel [Web API](service-fabric-reliable-services-communication-webapi.md), är det definierar lyssnarslutpunkten eller slutpunkter för tjänsten (hur klienter nå tjänsten). Den definierar hur de meddelanden som visas interagera med resten av kod.
* **RunAsync** -metoden är där tjänsten körs dess affärslogik och där det skulle startar alla bakgrundsuppgifter som ska köras för livslängden för tjänsten. Annullering-token som har angetts är en signal för när arbetet ska sluta. Till exempel om tjänsten behöver hämta meddelanden från en tillförlitlig kö och bearbeta dem, är där som fungerar som händer.

Om du lär dig reliable services för första gången, läsa på! Om du letar efter en detaljerad genomgång av livscykeln för tillförlitlig tjänster, du kan gå till [i den här artikeln](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exempel services
Att veta den här programmeringsmodell ska vi ta en titt på två olika tjänster för att se hur dessa delar hänger ihop.

### <a name="stateless-reliable-services"></a>Tillståndslösa Reliable Services
Tillståndslösa tjänsten är en där det finns ingen delstat bibehålls inom tjänsten mellan anrop. Alla lägen som finns är enbart tillgänglig och behöver inte synkronisering, replikering, beständiga eller hög tillgänglighet.

Anta till exempel att en kalkylator som har inget minne och tar emot alla villkor och åtgärder som ska utföras samtidigt.

I det här fallet den `RunAsync()` (C#) eller `runAsync()` (Java) för tjänsten kan vara tomt, eftersom det finns ingen aktivitet-behandling i bakgrunden som tjänsten behöver göra. När tjänsten Kalkylatorn skapas, returneras ett `ICommunicationListener` (C#) eller `CommunicationListener` (Java) (till exempel [Web API](service-fabric-reliable-services-communication-webapi.md)) som öppnar en lyssnarslutpunkten på vissa port. Den här lyssnarslutpunkten skapar till av olika-beräkningsmetoder (exempel: ”Lägg till (n1, n2)”) som definierar den Kalkylatorn offentliga API: N.

När ett anrop görs från en klient, en lämplig metod har anropats och tjänsten Kalkylatorn utför åtgärder på data som anges och returnerar resultatet. Informationen lagras inte i några tillstånd.

Inte lagrar några interna tillstånd enkelt kalkylator för det här exemplet. Men de flesta tjänster är inte helt tillståndslösa. I stället gör de tillståndet till några andra store. (Till exempel ett webbprogram som förlitar sig på att hålla sessionstillstånd i en säkerhetskopiering eller cache är inte tillståndslösa.)

Ett vanligt exempel på hur tillståndslösa tjänster används i Service Fabric är som en klientdel som exponerar offentliga API: et för ett webbprogram. Frontend-tjänsten sedan pratar till tillståndskänsliga tjänster för att slutföra en begäran. I det här fallet dirigeras anrop från klienter till en känd port, till exempel 80, där tillståndslösa tjänsten lyssnar. Tillståndslösa tjänsten tar emot anropet och avgör om anropet kommer från en betrodd part och vilken tjänst som den är avsedd för.  Tillståndslösa tjänsten vidarebefordrar anropet till rätt partition på tillståndskänslig service och väntar på svar. När den tillståndslösa tjänsten tar emot ett svar, svarar den ursprungliga klienten. Ett exempel på dessa tjänster finns i våra exempel [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Detta är endast ett exempel på detta mönster i exemplen finns det andra i samt andra prover.

### <a name="stateful-reliable-services"></a>Tillståndskänsliga Reliable Services
En tillståndskänslig service är en som måste ha en del av tillstånd hålls konsekvent och finns i för att tjänsten ska fungera. Överväg att en tjänst som ständigt beräknar en rullande medelvärdet för ett värde baserat på den tar emot uppdateringar. Om du vill göra det, måste den ha den aktuella uppsättningen av inkommande begäranden måste processen och aktuella medelvärdet. Alla tjänster som hämtar bearbetar och lagrar information i en extern butik (till exempel ett Azure blob eller tabell store idag) är tillståndskänslig. Den bevarar bara dess tillstånd i externa tillståndslagret.

De flesta tjänster lagra idag deras tillstånd externt, eftersom den externa lagringen är vad tillhandahåller tillförlitlighet, tillgänglighet, skalbarhet och konsekvens för det aktuella tillståndet. I Service Fabric krävs inte services för att lagra sina externt tillstånd. Service Fabric hand tar om kraven för både kod och tjänstens tillstånd.

Anta att vi vill att skriva en tjänst som bearbetar bilder. Detta gör tar tjänsten i en avbildning och Antal konverteringar att utföra på avbildningen. Den här tjänsten returnerar en lyssnare för kommunikation (vi anta att det är en WebAPI) som visar en API som `ConvertImage(Image i, IList<Conversion> conversions)`. När den tar emot en begäran om tjänsten lagrar den i en `IReliableQueue`, och returnerar vissa-id till klienten så att den kan spåra begäran.

I den här tjänsten `RunAsync()` kan vara mer komplexa. Tjänsten har en loop i dess `RunAsync()` som tar emot förfrågningar från `IReliableQueue` och utför konverteringar som begärdes. Resultatet lagras i en `IReliableDictionary` så att när klienten kommer tillbaka kan de få deras konverterade bilder. För att säkerställa att även om något misslyckas bilden inte förlorade, tillförlitlig tjänsten skulle dra ur kön utför konverteringar och spara resultatet i en enda transaktion. I det här fallet meddelandet tas bort från kön och resultatet lagras i ordlistan resultatet bara när konverteringar har slutförts. Tjänsten kan även pull-avbildningen från kön och omedelbart lagra det i ett Arkiv för fjärråtkomst. Detta minskar mängden tillstånd har tjänsten för att hantera, men ökar komplexiteten eftersom tjänsten har att nödvändiga metadata för att hantera arkivet för fjärråtkomst. Om något fel i mitten med antingen metoden förblir begäran i kön som väntar på att bearbetas.

Observera om den här tjänsten är att det låter som en normal .NET-tjänsten! Den enda skillnaden är att datastrukturer som används (`IReliableQueue` och `IReliableDictionary`) tillhandahålls av Service Fabric och är tillförlitliga, hög tillgänglighet och konsekvent.

## <a name="when-to-use-reliable-services-apis"></a>När du ska använda tillförlitliga Services API: er
Om något av följande karaktäriserar behöver ditt program bör du överväga Reliable Services API: er:

* Du vill din tjänst kod (och alternativt tillstånd) ska vara mycket hög tillgänglighet och tillförlitlig
* Du behöver transaktionella garantier över flera enheter av tillstånd (till exempel order och ordning artiklar).
* Programmets tillstånd kan modelleras naturligt som tillförlitliga ordböcker och köer.
* Ditt program, kod eller tillstånd måste ha hög tillgänglighet med låg latens läsningar och skrivningar.
* Programmet måste styra samtidighet eller Granulariteten för överförda åtgärder för en eller flera tillförlitliga samlingar.
* Du vill hantera meddelanden eller kontrollera partitioneringsschemat för din tjänst.
* Din kod måste en fritrådade körningsmiljö.
* Programmet måste att dynamiskt skapa eller förstöra tillförlitliga ordlistor eller köer eller hela tjänster vid körning.
* Du behöver programmässigt styr förutsatt att Service Fabric-säkerhetskopia och återställa funktioner för din tjänst tillstånd.
* Ditt program behöver underhålla ändringshistoriken för dess enheter av tillstånd.
* Du vill utveckla eller använda tillstånd för tredje part utvecklade, anpassade providers.

## <a name="next-steps"></a>Nästa steg
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
* [Programmeringsmodell för Reliable Actors](service-fabric-reliable-actors-introduction.md)
