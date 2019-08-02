---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 4b039e777748499e1b9a2a120e9498d94066b735
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688284"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hög tillgänglighet med Azure Cosmos DB

Azure Cosmos DB transparent replikera dina data över alla Azure-regioner som är kopplade till ditt Cosmos-konto. Cosmos DB använder flera lager av redundans för dina data som visas i följande bild:

![Fysisk partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- Data i Cosmos-behållare är [vågrätt partitionerade](partitioning-overview.md).

- I varje region skyddas varje partition av en replik uppsättning med alla skrivningar som replikeras och varaktigt som allokeras av en majoritet av repliker. Repliker distribueras i så många som 10-20 fel domäner.

- Varje partition i alla regioner replikeras. Varje region innehåller alla datapartitioner för en Cosmos-behållare och kan godkänna skrivningar och betjäna läsningar.  

Om ditt Cosmos-konto är fördelat i *N* Azure-regioner finns det minst *n* x 4 kopior av alla dina data. Förutom att tillhandahålla data åtkomst med låg latens och skala Skriv-och Läs data flöde i de regioner som är kopplade till ditt Cosmos-konto har fler regioner (högre *N*) bättre tillgänglighet.  

## <a name="slas-for-availability"></a>Service avtal för tillgänglighet

Som en globalt distribuerad databas tillhandahåller Cosmos DB omfattande service avtal som omfattar data flöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. I tabellen nedan visas garantier för hög tillgänglighet som tillhandahålls av Cosmos DB för enkla och flera region konton. För hög tillgänglighet konfigurerar du alltid dina Cosmos-konton så att de har flera Skriv regioner.

|Åtgärds typ  | Enskild region |Flera regioner (enstaka regions skrivningar)|Flera regioner (skrivningar i flera regioner) |
|---------|---------|---------|-------|
|Skrivningar    | 99,99    |99,99   |99,999|
|Läsningar     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är den faktiska Skriv tillgängligheten för begränsat inaktuellitet, session, konsekvent prefix och eventuella konsekvens modeller betydligt högre än den publicerade service avtal. Den faktiska Läs tillgängligheten för alla konsekvens nivåer är betydligt högre än den publicerade service avtal.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Cosmos DB i händelse av regionala avbrott

Regionala avbrott är inte ovanliga, men med Azure Cosmos DB har din databas alltid hög tillgänglighet. Följande information inhämtas Cosmos DB under ett avbrott, beroende på din Cosmos-konto konfiguration:

- Med Cosmos DB, innan en Skriv åtgärd bekräftas till klienten, så är data varaktigt allokerade av ett kvorum av repliker inom den region som accepterar Skriv åtgärder.

- Konton med flera regioner som kon figurer ATS med flera-Write-regioner är hög tillgängliga för både skrivningar och läsningar. Regionala redundanser är omedelbara och kräver inte några ändringar från programmet.

- **Konton för flera regioner med en enkel-eller regions åtgärd (Skriv åtgärds avbrott):** 
  * Under ett avbrott i Skriv regionen är de här kontona fortfarande hög tillgängliga för läsning. För att Skriv förfrågningar ska lyckas måste du aktivera alternativet **Aktivera automatisk redundans** på ditt Azure Cosmos-konto. Om du aktiverar det här alternativet aktive ras den påverkade regionen till en annan region i ordningen för regionens prioritet som du har angett. 
  * När den tidigare påverkade regionen är online igen görs eventuella Skriv data som inte har repliker ATS när regionen misslyckades, görs tillgängliga via feeden för [konflikter](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konflikten, lösa konflikterna baserat på programspecifik logik och skriva uppdaterade data tillbaka till Azure Cosmos-behållaren efter behov. 
  * När den tidigare påverkade Skriv regionen återställs blir den automatiskt tillgänglig som en Läs region. Du kan växla tillbaka till den återställda regionen som Skriv region. Du kan byta region med hjälp av [Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover). Det finns **Ingen förlust av data eller tillgänglighet** innan, under eller efter att du byter Skriv region och programmet fortfarande har hög tillgänglighet. 

- **Konton för flera regioner med en enkel-eller regions åtgärd (Läs regions avbrott):** 
  * Under ett avbrott i läsnings området är dessa konton hög tillgängliga för läsning och skrivning. 
  * Den berörda regionen kopplas automatiskt från Skriv regionen och kommer att markeras som offline. [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) : er omdirigerar Läs anrop till nästa tillgängliga region i listan över önskade regioner. 
  * Om ingen av regionerna i listan över önskade regioner är tillgänglig, återgår anrop automatiskt till den aktuella Skriv regionen. 
  * Inga ändringar krävs i din program kod för att hantera avbrott i läsnings området. När den påverkade regionen är online, synkroniseras den tidigare berörda läsnings regionen automatiskt med den aktuella Skriv regionen och kommer att vara tillgänglig igen för att hantera Läs begär Anden. 
  * Efterföljande läsningar omdirigeras till den återställda regionen utan att det krävs några ändringar i program koden. Vid både redundans och åter anslutning av en tidigare misslyckad region, kan Läs konsekvens garantier fortsätta att följas av Cosmos DB.

- Konton med en region kan förlora tillgänglighet efter ett regionalt avbrott. Vi rekommenderar alltid att du konfigurerar **minst två regioner** (helst, minst två Skriv regioner) med ditt Cosmos-konto för att säkerställa hög tillgänglighet hela tiden.

- Även i en sällsynt och olycklig-händelse när Azure-regionen är permanent oåterkalleligt, sker ingen data förlust om ditt Cosmos-konto i flera regioner har kon figurer ATS med standard konsekvens nivån *Strong*. I händelse av en permanent oåterkalleligt Skriv region, för Cosmos-konton med flera regioner som kon figurer ATS med begränsad föråldrad konsekvens, är den potentiella data förlust perioden begränsad till inaktuella fönster (*K* eller *T*). för session, konsekvent prefix och eventuell konsekvens, är den potentiella data förlust perioden begränsad till högst fem sekunder. 

## <a name="availability-zone-support"></a>Support för tillgänglighets zon

Azure Cosmos DB är en globalt distribuerad databas tjänst med flera huvud tjänster som ger hög tillgänglighet och återhämtning under regionala avbrott. Förutom återhämtning i flera regioner kan du nu aktivera **zon redundans** när du väljer en region som ska associeras med din Azure Cosmos-databas. 

Med support för tillgänglighets zon kan Azure Cosmos DB se till att repliker placeras i flera zoner inom en bestämd region för att ge hög tillgänglighet och återhämtning under zonindelade-haverier. Det finns inga ändringar i svars tid och andra service avtal i den här konfigurationen. I händelse av ett enskilt zon haveri ger zon redundans fullständig data hållbarhet med återställnings punkt = 0 och tillgänglighet med RTO = 0. 

Zon redundans är en *kompletterande funktion* för funktionen för [multi-master-replikering](how-to-multi-master.md) . Zon redundans kan inte förlita sig på att uppnå regional återhämtning. I händelse av regionala avbrott eller åtkomst med låg fördröjning i regionerna, rekommenderar vi till exempel att du har flera Skriv regioner förutom zon redundans. 

När du konfigurerar flera regions skrivningar för ditt Azure Cosmos-konto kan du välja zon redundans utan extra kostnad. I annat fall kan du läsa avsnittet om prissättningen av stöd för zon redundans. Du kan aktivera zon redundans i en befintlig region för ditt Azure Cosmos-konto genom att ta bort regionen och lägga till den igen med zon redundansen aktive rad.

Den här funktionen är tillgänglig i följande Azure-regioner:

* Storbritannien, södra
* Sydostasien 
* East US
* USA, östra 2 
* Centrala USA
* Västra Europa
* Västra USA 2

> [!NOTE] 
> Om du aktiverar Tillgänglighetszoner för ett Azure Cosmos-konto med en region kommer det att leda till kostnader som motsvarar att lägga till ytterligare en region i ditt konto. Mer information om priser finns på [sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) och [kostnaden för flera regioner i Azure Cosmos DB](optimize-cost-regions.md) artiklar. 

I följande tabell sammanfattas funktionen för hög tillgänglighet för olika konto konfigurationer: 

|KPI  |En region utan Tillgänglighetszoner (icke-AZ)  |En region med Tillgänglighetszoner (AZ)  |Skrivningar i flera regioner med Tillgänglighetszoner (AZ, 2 regioner) – den rekommenderade inställningen |
|---------|---------|---------|---------|
|SLA för Skriv tillgänglighet     |   99,99 %      |    99,99 %     |  99.999%  |
|SLA för Läs tillgänglighet   |   99,99 %      |   99,99 %      |  99.999%       |
|Pris  |  Fakturerings taxa för enskild region |  Fakturerings takt för tillgänglighets zon för enskild region |  Fakturerings taxa för flera regioner       |
|Zon haverier – data förlust   |  Data förlust  |   Ingen data förlust |   Ingen data förlust  |
|Zon haverier – tillgänglighet |  Tillgänglighets förlust  | Ingen förlust av tillgänglighet  |  Ingen förlust av tillgänglighet  |
|Läs fördröjning    |  Mellan region    |   Mellan region   |    Låg  |
|Skriv fördröjning    |   Mellan region   |  Mellan region    |   Låg   |
|Regionalt avbrott – data förlust    |   Data förlust      |  Data förlust       |   Data förlust <br/><br/> När du använder begränsad föråldrad konsekvens med flera huvud servrar och mer än en region begränsas data förlusten till den begränsade inaktuellheten som kon figurer ATS på ditt konto. <br/><br/> Data förlust under regionalt avbrott kan undvikas genom att konfigurera stark konsekvens med flera regioner. Det här alternativet innehåller kompromisser som påverkar tillgänglighet och prestanda.      |
|Regionalt avbrott – tillgänglighet  |  Tillgänglighets förlust       |  Tillgänglighets förlust       |  Ingen förlust av tillgänglighet  |
|Dataflöde    |  X RU/s-allokerat data flöde      |  X RU/s-allokerat data flöde       |  2X RU/s-allokerat data flöde <br/><br/> Det här konfigurations läget kräver två gånger mängden data flöde jämfört med en region med Tillgänglighetszoner eftersom det finns två regioner.   |

> [!NOTE] 
> Om du vill aktivera stöd för tillgänglighets zoner för ett Azure Cosmos-konto med flera regioner måste kontot ha flera huvud skrivningar aktiverade.


Du kan aktivera zon redundans när du lägger till en region i nya eller befintliga Azure Cosmos-konton. För närvarande kan du bara aktivera zon redundans genom att använda Azure Portal, PowerShell och Azure Resource Manager mallar. Om du vill aktivera zon redundans på ditt Azure Cosmos-konto, bör du `isZoneRedundant` ange flaggan `true` till för en angiven plats. Du kan ange den här flaggan i egenskapen locations. Följande PowerShell-kodfragment aktiverar till exempel zon redundans för regionen "Sydostasien":

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Du kan aktivera Tillgänglighetszoner genom att använda Azure Portal när du skapar ett Azure Cosmos-konto. När du skapar ett konto, se till att aktivera **GEO-redundans**, **flera regioner**och välj en region där Tillgänglighetszoner stöds: 

![Aktivera Tillgänglighetszoner med Azure Portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Skapa program med hög tillgänglighet

- För att säkerställa hög Skriv-och Läs tillgänglighet kan du konfigurera ditt Cosmos-konto så att det omfattar minst två regioner med flera Skriv regioner. Med den här konfigurationen får du högsta tillgänglighet, lägsta svars tid och bästa skalbarhet för både läsningar och skrivningar som backas upp av service avtal. Mer information finns i så här [konfigurerar du ditt Cosmos-konto med flera Write-regioner](tutorial-global-distribution-sql-api.md).

- För Cosmos-konton med flera regioner som har kon figurer ATS med en enda skrivbar region [aktiverar du automatisk redundansväxling med hjälp av Azure CLI eller Azure Portal](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det uppstår en regional katastrof, kan Cosmos DB automatiskt redundansväxla ditt konto.  

- Även om ditt Cosmos-konto har hög tillgänglighet är ditt program kanske inte korrekt utformat för att hålla hög tillgänglighet. Om du vill testa hög tillgänglighet för ditt program från slut punkt till slut punkt anropar du regelbundet [manuell redundansväxling med hjälp av Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover), som en del av din program testning eller haveri beredskap (haveri beredskap).

- I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för återställnings tids mål (RTO). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tidsperioden för uppdateringar som du kan ha råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se återställnings-och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Härnäst kan du läsa följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Globalt skalning av allokerat data flöde](scaling-throughput.md)
* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)
* [Så här konfigurerar du ditt Cosmos-konto med flera Skriv regioner](how-to-multi-master.md)
