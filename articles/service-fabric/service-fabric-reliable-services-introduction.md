---
title: Översikt över programmeringsmodell för tillförlitlig tjänst för Service Fabric | Microsoft Docs
description: Läs om Service Fabric Reliable Services-programmeringsmodell och börja skriva dina egna tjänster.
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
ms.openlocfilehash: bc4e4c9137003dad9cfd96772b7ebe231cd6eace
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171731"
---
# <a name="reliable-services-overview"></a>Översikt över Reliable Services
Azure Service Fabric förenklar skriva och hantera tillståndslösa och tillståndskänsliga Reliable Services. Det här avsnittet innehåller:

* Reliable Services programmeringsmiljö för tillståndslösa och tillståndskänsliga tjänster.
* De alternativ som du behöver göra när du skriver en tillförlitlig tjänst.
* Vissa scenarier och exempel på när du ska använda Reliable Services och hur de skrivs.

Reliable Services är en av programmeringsmodellerna som är tillgängliga i Service Fabric. Den andra är tillförlitliga aktörer programmeringsmodell, vilket är en programmeringsmodell med virtuella aktören ovanpå Reliable Services-modellen. Mer information om programmeringsmodellen Reliable Actors finns [introduktion till Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric hanterar livslängden för tjänster från etablering och distribution genom uppgradering och borttagning, via [Service Fabric-programhantering](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Vad är Reliable Services?
Reliable Services får du en enkel, kraftfull och översta programmeringsmodell för att express viktig information för ditt program. Med programmeringsmodellen i Reliable Services får du:

* Åtkomst till resten av Service Fabric programming API: er. Till skillnad från Service Fabric-tjänster som toppresurser [Gästfiler](service-fabric-guest-executables-introduction.md), Reliable Services får använda rest API: er för Service Fabric direkt. På så sätt kan tjänster för att:
  * fråga systemet
  * rapporten hälsotillstånd om entiteter i klustret
  * ta emot meddelanden om konfigurations-och kod
  * hitta och kommunicera med andra tjänster
  * (valfritt) Använd den [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * ...och ge dem åtkomst till många andra funktioner, allt från en förstklassigt programmeringsmodell i flera programmeringsspråk.
* En enkel modell för att köra din egen kod som ser ut som programmeringsmodeller som du brukade. Koden har en väldefinierad startpunkt och lätthanterade livscykel.
* En modulär modell. Använda transport av föredrar, till exempel HTTP med [webb-API](service-fabric-reliable-services-communication-webapi.md), WebSockets, anpassade TCP-protokoll eller något annat. Reliable Services tillhandahåller några bra alternativ för out-of the box som du kan använda eller du kan ange dina egna.
* För tillståndskänsliga tjänster programmeringsmodellen Reliable Services kan du konsekvent och tillförlitligt lagra din delstat i din tjänst med hjälp av [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Tillförlitliga samlingar är en enkel uppsättning med hög tillgänglighet och tillförlitlighet klasser som är välkända för alla som har använt C# samlingar. Traditionellt har tjänster som krävs för externa system för tillståndshantering av tillförlitliga. Med Reliable Collections kan du lagra din delstat bredvid din databearbetning med samma hög tillgänglighet och tillförlitlighet som har du kommit till förväntar dig från med hög tillgänglighet externa butiker. Den här modellen förbättrar också svarstid eftersom du samplacera beräknings- och tillstånd som den behöver för att fungera.

## <a name="what-makes-reliable-services-different"></a>Vad gör Reliable Services olika?
Reliable Services i Service Fabric skiljer sig från tjänster som du har skrivit innan. Service Fabric tillhandahåller tillförlitlighet, tillgänglighet, konsekvens och skalbarhet.

* **Tillförlitlighet** – din tjänst håller dig även i otillförlitliga miljöer där dina datorer misslyckas eller tryck på nätverksproblem, eller i fall där tjänsterna själva stöter på fel och krasch eller misslyckas. För tillståndskänsliga tjänster bevaras din delstat även om det förekommer nätverk eller andra fel.
* **Tillgänglighet** -tjänsten är kan nås och svarar. Service Fabric underhåller din önskade antalet kopior som körs.
* **Skalbarhet** – tjänster är fristående från maskinvara och de kan utökas eller minskas vid behov genom att lägga till och ta bort maskinvara eller andra resurser. Tjänster enkelt partitioneras (särskilt i tillståndskänsliga fallet) för att säkerställa att tjänsten kan skala och hantera misslyckades delvis. Tjänster kan skapas och tas bort dynamiskt via kod, aktiverar fler instanser att vara kunde vid behov säga i svar på Återkopplingen från. Slutligen uppmuntrar services ska vara lätt att Service Fabric. Service Fabric kan tusentals tjänster som ska etableras i en enda process i stället kräver eller tilldela hela OS-instanser eller processer till en enda instans av en tjänst.
* **Konsekvens** -all information som lagras i den här tjänsten kan garanteras att vara konsekvent. Detta gäller även över flera tillförlitliga samlingar inom en tjänst. Ändringar i samlingar inom en tjänst kan göras på ett transaktionellt atomiska sätt.

## <a name="service-lifecycle"></a>Livscykel för tjänsten
Om din tjänst är tillståndskänsliga eller tillståndslösa, tillhandahåller Reliable Services en enkel livscykel som gör att du snabbt ansluta i din kod och komma igång.  Det finns en eller två metoder som du behöver implementera för att få din tjänst och drift.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** -den här metoden är där tjänsten definierar kommunikation-stack(s) som ska användas. Kommunikationen stack, till exempel [webb-API](service-fabric-reliable-services-communication-webapi.md), definieras lyssnarslutpunkten eller slutpunkter för tjänsten (hur klienter nå tjänsten). Den definierar även hur de meddelanden som visas interagerar med resten av kod.
* **RunAsync** -den här metoden är där din tjänst körs dess affärslogik och där det Sparka alla bakgrundsaktiviteter som ska köras för livslängden för tjänsten. Annulleringen token som tillhandahålls är en signal för när arbetet ska sluta. Om tjänsten behöver hämta meddelanden från en tillförlitlig kö och bearbeta dem, är det till exempel där arbetet sker.

Om du håller på att lära om reliable services för första gången, kan du läsa på! Om du letar efter en detaljerad genomgång av livscykeln för tillförlitliga tjänster, du kan gå vidare till [i den här artikeln](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exempel services
Att känna till den här programmeringsmodell, låt oss ta en titt på två olika tjänster för att se hur dessa delar hänger ihop.

### <a name="stateless-reliable-services"></a>Tillståndslösa Reliable Services
En tillståndslös tjänst är en där det finns inga tillstånd bibehålls i tjänsten mellan anrop. Tillståndsinformation som finns är helt disponibla och kräver inte synkronisering, replikering, persistence eller hög tillgänglighet.

Anta exempelvis att en beräkning som har inget minne och tar emot alla villkor och åtgärder som ska utföras på samma gång.

I det här fallet den `RunAsync()` (C#) eller `runAsync()` (Java) för tjänsten kan vara tom, eftersom det finns inga uppgift-behandling i bakgrunden som tjänsten behöver göra. När tjänsten Kalkylatorn skapas, returneras en `ICommunicationListener` (C#) eller `CommunicationListener` (Java) (till exempel [webb-API](service-fabric-reliable-services-communication-webapi.md)) som öppnas lyssnarslutpunkten på vissa port. Den här lyssnarslutpunkten skapar till av olika-beräkningsmetoder (exempel: ”Lägg till (n1, n2)”) som definierar den Kalkylatorn offentliga API: N.

När ett anrop görs från en klient, en lämplig metod har anropats och tjänsten Kalkylatorn utför åtgärder på data som tillhandahålls och returnerar resultatet. Informationen lagras inte i några tillstånd.

Inte lagrar några interna tillstånd gör den här exempel-Kalkylatorn enkelt. Men de flesta tjänster är inte helt tillståndslösa. De gör i stället deras tillstånd till några andra store. (Till exempel en webbapp som förlitar sig på att lagra sessionstillstånd i en lagringsenhet eller cache är inte tillståndslösa.)

Ett vanligt exempel på hur tillståndslösa tjänster används i Service Fabric är som en klientdel som exponerar offentliga API: et för ett webbprogram. Frontend-tjänst kommunicerar sedan tillståndskänsliga tjänster att slutföra en begäran. I det här fallet dirigeras anrop från klienter till en känd port, till exempel 80, där den tillståndslösa tjänsten lyssnar. Den tillståndslösa tjänsten tar emot anropet och avgör om anropet kommer från en betrodd part och vilken tjänst som den är avsedd för.  Sedan den tillståndslösa tjänsten vidarebefordrar anropet till rätt partition för den tillståndskänsliga tjänsten och väntar på svar. När den tillståndslösa tjänsten tar emot ett svar, svarar den ursprungliga klienten. Ett exempel på dessa tjänster är i våra exempel [ C# ](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)  /  [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Detta är endast ett exempel på det här mönstret i exemplen finns det andra i samt andra exempel.

### <a name="stateful-reliable-services"></a>Tillståndskänsliga Reliable Services
En tillståndskänslig tjänst är en som måste ha en del av tillstånd förblir konsekventa och finns i för att tjänsten ska fungera. Överväg att en tjänst som ständigt beräknar en rullande medelvärdet för ett värde baserat på den tar emot uppdateringar. Om du vill göra detta, måste den ha den aktuella uppsättningen inkommande begäranden som behövs för att bearbeta och aktuella medelvärdet. Alla tjänster som hämtar, bearbetar och lagrar information i en extern lagring (till exempel ett Azure blob eller table store idag) är tillståndskänslig. Det behåller bara sitt tillstånd i externa tillståndslagret.

De flesta tjänster lagra idag deras status externt, eftersom den externa lagringen är vad tillhandahåller tillförlitlighet, tillgänglighet, skalbarhet och konsekvens för det aktuella tillståndet. I Service Fabric kan krävs inte services för att lagra sin status externt. Service Fabric hand tar om kraven för både kod och tjänstens tillstånd.

Anta att vi vill skriva en tjänst som bearbetar avbildningar. Då tar tjänsten en bild och serie konverteringar att utföra på avbildningen. Den här tjänsten returnerar en kommunikationslyssnaren (Låt oss anta att det är en WebAPI) som exponerar ett API som `ConvertImage(Image i, IList<Conversion> conversions)`. När den får en förfrågan tjänsten lagrar den i en `IReliableQueue`, och returnerar vissa-id till klienten så att den kan spåra begäran.

I den här tjänsten `RunAsync()` kan vara mer komplex. Tjänsten har en loop i dess `RunAsync()` som tar emot begäranden av `IReliableQueue` och utför konverteringar som begärdes. Resultaten lagras i en `IReliableDictionary` så att när klienten kommer tillbaka de kan få deras konverterade bilder. För att säkerställa att även om något inte fungerar bilden försvinner inte, den här tillförlitlig tjänst skulle hämta ur kön, utföra konverteringar och lagrar resultatet i en enda transaktion. I det här fallet meddelandet tas bort från kön och resultaten lagras i ordlistan resultatet bara när konverteringar har slutförts. Tjänsten kan även hämta avbildningen från kön och lagra den direkt i ett fjärrlager. Detta minskar mängden tillstånd har tjänsten för att hantera, men ökar komplexiteten sedan tjänsten har att behålla metadata som behövs för att hantera fjärrlager. Om något fel i mitten med antingen metoden förblir begäran i kön som väntar på att bearbetas.

En sak att tänka på om den här tjänsten är att det låter som en normal .NET-tjänst! Den enda skillnaden är att datastrukturer som används (`IReliableQueue` och `IReliableDictionary`) tillhandahålls av Service Fabric och är mycket pålitlig, tillgänglig och konsekvent.

## <a name="when-to-use-reliable-services-apis"></a>När du ska använda API: er med Reliable Services
Om något av följande beskriva dina programbehov tjänsten bör du tänka Reliable Services-API: er:

* Du vill att din tjänst kod (och eventuellt tillstånd) är hög tillgänglighet och tillförlitlighet
* Du behöver transaktionell garantier över flera enheter av tillstånd (till exempel beställningar och ordning radobjekt).
* Programmets tillstånd kan modelleras naturligt som tillförlitlig ordlistor och köer.
* Ditt program, kod eller tillstånd måste ha hög tillgänglighet med låg latens vid läsning och skrivning.
* Programmet behöver för att styra samtidighet och kornigheten för överförda åtgärder i en eller flera tillförlitliga samlingar.
* Du vill hantera meddelanden eller har kontroll över partitioneringsschemat för din tjänst.
* Din kod måste en kostnadsfri-threaded körningsmiljö.
* Programmet behöver för att dynamiskt skapa eller förstör tillförlitlig ordlistor eller köer eller hela tjänster vid körning.
* Du måste kontrollera förutsatt att Service Fabric-säkerhetskopia och återställa funktioner för tjänstens tillstånd programmässigt.
* Programmet behöver för att underhålla ändringshistoriken för dess enheter av tillstånd.
* Du vill utveckla eller använda tillstånd från tredje part utvecklade, anpassade providers.

## <a name="next-steps"></a>Nästa steg
* [Snabbstart för Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
* [Programmeringsmodellen Reliable Actors](service-fabric-reliable-actors-introduction.md)
