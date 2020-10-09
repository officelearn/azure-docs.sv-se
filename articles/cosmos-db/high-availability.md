---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 3b9d1c8c6271d689b022a069de8e3392c0662dd6
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826888"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Hur ger Azure Cosmos DB hög tillgänglighet? 

Azure Cosmos DB transparent replikera dina data över alla Azure-regioner som är kopplade till ditt Azure Cosmos-konto. Azure Cosmos DB använder flera lager av redundans för dina data som visas i följande bild:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fysisk partitionering" border="false":::

- Data i Azure Cosmos-behållare är [vågrätt partitionerade](partitioning-overview.md).

- En partitionsuppsättning är en samling med flera replik uppsättningar. I varje region skyddas varje partition av en replik uppsättning med alla skrivningar som replikeras och varaktigt som allokeras av en majoritet av repliker. Repliker distribueras i så många som 10-20 fel domäner.

- Varje partition i alla regioner replikeras. Varje region innehåller alla datapartitioner för en Azure Cosmos-behållare och kan godkänna skrivningar och betjäna läsningar.  

Om ditt Azure Cosmos-konto är fördelat i *N* Azure-regioner finns det minst *n* x 4 kopior av alla dina data. Att ha ett Azure Cosmos-konto i fler än två regioner förbättrar tillgängligheten för ditt program och ger låg latens i de associerade regionerna. 

## <a name="slas-for-availability"></a>Service avtal för tillgänglighet

Som en globalt distribuerad databas tillhandahåller Azure Cosmos DB omfattande service avtal som omfattar data flöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. I tabellen nedan visas garantier för hög tillgänglighet som tillhandahålls av Azure Cosmos DB för enkla och flera region konton. För hög tillgänglighet konfigurerar du alltid dina Azure Cosmos-konton så att de har flera Skriv regioner.

|Åtgärdstyp  | Enskild region |Flera regioner (enstaka regions skrivningar)|Flera regioner (skrivningar i flera regioner) |
|---------|---------|---------|-------|
|Skrivningar    | 99,99    |99,99   |99,999|
|Läsningar     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är den faktiska Skriv tillgängligheten för begränsat inaktuellitet, session, konsekvent prefix och eventuella konsekvens modeller betydligt högre än den publicerade service avtal. Den faktiska Läs tillgängligheten för alla konsekvens nivåer är betydligt högre än den publicerade service avtal.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Azure Cosmos DB i händelse av regionala avbrott

I sällsynta fall av regionala avbrott ser Azure Cosmos DB till att databasen alltid är hög tillgänglig. Följande information inhämtas Azure Cosmos DB under ett avbrott, beroende på din Azure Cosmos-konto konfiguration:

- Med Azure Cosmos DB, innan en Skriv åtgärd bekräftas till klienten, så är data varaktigt allokerade av ett kvorum av repliker inom den region som accepterar Skriv åtgärder.

- Konton med flera regioner som kon figurer ATS med flera-Write-regioner är hög tillgängliga för både skrivningar och läsningar. Regionala redundanser är omedelbara och kräver inte några ändringar från programmet.

- Konton med en region kan förlora tillgänglighet efter ett regionalt avbrott. Vi rekommenderar alltid att du konfigurerar **minst två regioner** (helst, minst två Skriv regioner) med ditt Azure Cosmos-konto för att säkerställa hög tillgänglighet hela tiden.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konton för flera regioner med en enkel skrivnings region (Skriv åtgärds avbrott)

- Under ett avbrott i Skriv region befordrar Azure Cosmos-kontot automatiskt en sekundär region som är den nya primära Skriv regionen när **aktivering av automatisk redundans** har kon figurer ATS på Azure Cosmos-kontot. När den är aktive rad kommer redundansväxlingen att ske till en annan region i ordningen för regionens prioritet som du har angett.
- När den tidigare påverkade regionen är online igen görs eventuella Skriv data som inte har repliker ATS när regionen misslyckades, görs tillgängliga via [feeden för konflikter](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konflikten, lösa konflikterna baserat på programspecifik logik och skriva uppdaterade data tillbaka till Azure Cosmos-behållaren efter behov.
- När den tidigare påverkade Skriv regionen återställs blir den automatiskt tillgänglig som en Läs region. Du kan växla tillbaka till den återställda regionen som Skriv region. Du kan byta region med hjälp av [PowerShell, Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover). Det finns **Ingen förlust av data eller tillgänglighet** innan, under eller efter att du byter Skriv region och programmet fortfarande har hög tillgänglighet.

> [!IMPORTANT]
> Vi rekommenderar starkt att du konfigurerar de Azure Cosmos-konton som används för produktions arbets belastningar för att **Aktivera automatisk redundans**. Manuell redundans kräver anslutning mellan sekundär och primär Skriv region för att slutföra en konsekvens kontroll för att säkerställa att inga data går förlorade under redundansväxlingen. Om den primära regionen inte är tillgänglig kan konsekvens kontrollen inte slutföras och den manuella redundansväxlingen Miss lyckas, vilket leder till förlust av Skriv tillgänglighet.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konton med flera regioner och en enkel-eller region (Läs regions avbrott)

- Under ett avbrott i läsnings området är Azure Cosmos-konton som använder en konsekvens nivå eller en stark konsekvens med tre eller flera Läs regioner fortfarande hög tillgängliga för läsning och skrivning.
- Azure Cosmos-konton som använder stark konsekvens med två eller färre Läs regioner (som omfattar Skriv regionen läsning &) förlorar läsnings skrivnings tillgänglighet under ett Läs regions avbrott.
- Den berörda regionen kopplas från automatiskt och kommer att markeras som offline. [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) : er omdirigerar Läs anrop till nästa tillgängliga region i listan över önskade regioner.
- Om ingen av regionerna i listan över föredragna regioner är tillgänglig kan återgår anrop automatiskt till den aktuella skrivregionen.
- Inga ändringar krävs i din program kod för att hantera avbrott i läsnings området. När den berörda läsnings regionen är online synkroniseras den automatiskt med den aktuella Skriv regionen och kommer att vara tillgänglig igen för att hantera Läs begär Anden.
- Efterföljande läsningar omdirigeras till den återställda regionen utan att det krävs några ändringar i din programkod. Vid både redundans och åter anslutning av en tidigare misslyckad region, kan Läs konsekvens garantier fortsätta att följas av Azure Cosmos DB.
- Även i en sällsynt och olycklig-händelse när Azure-regionen är permanent oåterkalleligt, sker ingen data förlust om ditt Azure Cosmos-konto i flera regioner har kon figurer ATS med *stark* konsekvens. I händelse av en permanent oåterkalleligt Skriv region, ett Azure Cosmos-konto med flera regioner som kon figurer ATS med begränsad föråldrad konsekvens, är den potentiella data förlust perioden begränsad till inaktuella fönster (*k* eller *t*) där K = 100000 uppdateringar och T = 5 minuter. För session, konsekvent prefix och eventuell konsekvens, är den potentiella data förlust perioden begränsad till högst 15 minuter. Mer information om RTO och mål för Azure Cosmos DB finns i [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Support för tillgänglighets zon

Förutom återhämtning i flera regioner kan du nu aktivera **zon redundans** när du väljer en region som ska associeras med din Azure Cosmos-databas.

Med support för tillgänglighets zon kan Azure Cosmos DB se till att repliker placeras i flera zoner inom en bestämd region för att ge hög tillgänglighet och återhämtning under zonindelade-haverier. Det finns inga ändringar i svars tid och andra service avtal i den här konfigurationen. I händelse av ett enskilt zon haveri ger zon redundans fullständig data hållbarhet med återställnings punkt = 0 och tillgänglighet med RTO = 0.

Zon redundans är en *kompletterande funktion* för [replikeringen i skriv funktionen i flera regioner](how-to-multi-master.md) . Zon redundans kan inte förlita sig på att uppnå regional återhämtning. I händelse av regionala avbrott eller åtkomst med låg fördröjning i regionerna, rekommenderar vi till exempel att du har flera Skriv regioner förutom zon redundans.

När du konfigurerar flera regions skrivningar för ditt Azure Cosmos-konto kan du välja zon redundans utan extra kostnad. I annat fall kan du läsa avsnittet om prissättningen av stöd för zon redundans. Du kan aktivera zon redundans i en befintlig region för ditt Azure Cosmos-konto genom att ta bort regionen och lägga till den igen med zon redundansen aktive rad.

Den här funktionen är tillgänglig i: *Storbritannien, södra, Sydostasien, USA, östra USA, östra USA 2, centrala USA, Västeuropa, västra USA 2, Östra Japan, norra Europa, centrala Australien, östra USA 2 EUAP* regioner.

I följande tabell sammanfattas funktionen för hög tillgänglighet för olika konto konfigurationer:

|KPI  |En region utan Tillgänglighetszoner (icke-AZ)  |En region med Tillgänglighetszoner (AZ)  |Skrivningar i flera regioner med Tillgänglighetszoner (AZ, 2 regioner) – den rekommenderade inställningen |
|---------|---------|---------|---------|
|SLA för Skriv tillgänglighet | 99,99 % | 99,99 % | 99,999 % |
|SLA för Läs tillgänglighet  | 99,99 % | 99,99 % | 99,999 % |
|Pris | Fakturerings taxa för enskild region | Fakturerings takt för tillgänglighets zon för enskild region | Fakturerings taxa för flera regioner |
|Zon haverier – data förlust | Data förlust | Ingen data förlust | Ingen data förlust |
|Zon haverier – tillgänglighet | Tillgänglighets förlust | Ingen förlust av tillgänglighet | Ingen förlust av tillgänglighet |
|Läs fördröjning | Mellan region | Mellan region | Låg |
|Skriv fördröjning | Mellan region | Mellan region | Låg |
|Regionalt avbrott – data förlust | Data förlust |  Data förlust | Data förlust <br/><br/> När du använder begränsad föråldrad konsekvens med flera Skriv regioner och fler än en region begränsas data förlusten till den begränsade inaktuellheten som kon figurer ATS på ditt konto <br /><br />Du kan undvika data förlust under ett regionalt avbrott genom att konfigurera stark konsekvens med flera regioner. Det här alternativet innehåller en kompromiss som påverkar tillgänglighet och prestanda. Den kan bara konfigureras på konton som kon figurer ATS för skrivningar i en region. |
|Regionalt avbrott – tillgänglighet | Tillgänglighets förlust | Tillgänglighets förlust | Ingen förlust av tillgänglighet |
|Dataflöde | X RU/s-allokerat data flöde | X RU/s tillhandahållet data flöde * 1,25 | 2X RU/s-allokerat data flöde <br/><br/> Det här konfigurations läget kräver två gånger mängden data flöde jämfört med en region med Tillgänglighetszoner eftersom det finns två regioner. |

> [!NOTE]
> Om du vill aktivera stöd för tillgänglighets zoner för ett Azure Cosmos-konto med flera regioner måste kontot ha flera region skrivningar aktiverade.

Du kan aktivera zon redundans när du lägger till en region i nya eller befintliga Azure Cosmos-konton. Om du vill aktivera zon redundans på ditt Azure Cosmos-konto, bör du ange `isZoneRedundant` flaggan till `true` för en angiven plats. Du kan ange den här flaggan i egenskapen locations. Följande PowerShell-kodfragment aktiverar till exempel zon redundans för regionen "Sydostasien":

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Följande kommando visar hur du aktiverar zon redundans för regionerna "öster" och "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Du kan aktivera Tillgänglighetszoner genom att använda Azure Portal när du skapar ett Azure Cosmos-konto. När du skapar ett konto, se till att aktivera **GEO-redundans**, **flera regioner**och välj en region där Tillgänglighetszoner stöds:

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="Fysisk partitionering"::: 

## <a name="building-highly-available-applications"></a>Skapa program med hög tillgänglighet

- Granska det förväntade [beteendet för Azure Cosmos-SDK](troubleshoot-sdk-availability.md) : er under dessa händelser och som är de konfigurationer som påverkar den.

- För att säkerställa hög Skriv-och Läs tillgänglighet konfigurerar du ditt Azure Cosmos-konto så att det omfattar minst två regioner med flera-Write-regioner. Med den här konfigurationen får du högsta tillgänglighet, lägsta svars tid och bästa skalbarhet för både läsningar och skrivningar som backas upp av service avtal. Mer information finns i så här [konfigurerar du ditt Azure Cosmos-konto med flera Write-regioner](tutorial-global-distribution-sql-api.md).

- För Azure Cosmos-konton med flera regioner som kon figurer ATS med en enda skrivbar region [aktiverar du automatisk redundans med hjälp av Azure CLI eller Azure Portal](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det uppstår en regional katastrof, kan Cosmos DB automatiskt redundansväxla ditt konto.  

- Även om ditt Azure Cosmos-konto har hög tillgänglighet är ditt program kanske inte korrekt utformat för att hålla hög tillgänglighet. Om du vill testa hög tillgänglighet från slut punkt till slut punkt för ditt program, som en del av dina program testnings-eller haveri beredskap (DR), kan du tillfälligt inaktivera automatisk redundans för kontot, anropa den [manuella redundansväxlingen med hjälp av PowerShell, Azure CLI eller Azure Portal](how-to-manage-database-account.md#manual-failover)och sedan övervaka programmets redundans. När du är klar kan du växla tillbaka till den primära regionen och återställa automatisk redundans för kontot.

- I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för återställnings tids mål (RTO). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tidsperioden för uppdateringar som du kan ha råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se återställnings-och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Härnäst kan du läsa följande artiklar:

- [Tillgänglighets-och prestanda kompromisser för olika konsekvens nivåer](consistency-levels-tradeoffs.md)
- [Skala etablerat dataflöde globalt](scaling-throughput.md)
- [Global distribution](global-dist-under-the-hood.md)
- [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
- [Så här konfigurerar du ditt Cosmos-konto med flera Skriv regioner](how-to-multi-master.md)
- [SDK-beteende i multiregionala miljöer](troubleshoot-sdk-availability.md)
