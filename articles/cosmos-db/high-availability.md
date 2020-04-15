---
title: Hög tillgänglighet i Azure Cosmos DB
description: I den här artikeln beskrivs hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382710"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hög tillgänglighet med Azure Cosmos DB

Azure Cosmos DB replikerar dina data på ett transparent sätt i alla Azure-regioner som är associerade med ditt Cosmos-konto. Cosmos DB använder flera lager av redundans för dina data som visas i följande bild:

![Fysisk partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- Data i Cosmos-behållare [är vågrätt partitionerade](partitioning-overview.md).

- Inom varje region skyddas varje partition av en replikuppsättning med alla skrivningar replikerade och vederbörligen bekräftade av en majoritet av replikerna. Repliker distribueras över så många som 10-20 feldomäner.

- Varje partition i alla regioner replikeras. Varje region innehåller alla datapartitioner i en Cosmos-behållare och kan acceptera skrivningar och serveläsningar.  

Om ditt Cosmos-konto distribueras över *N* Azure-regioner kommer det att finnas minst *N* x 4 kopior av alla dina data. Förutom att tillhandahålla dataåtkomst med låg latens och skalning av skriv-/läsdataflöde i de regioner som är associerade med ditt Cosmos-konto, förbättrar fler regioner (högre *N)* ytterligare tillgänglighet.  

## <a name="slas-for-availability"></a>SLA för tillgänglighet

Som en globalt distribuerad databas tillhandahåller Cosmos DB omfattande SLA:er som omfattar dataflöde, latens vid den 99:e percentilen, konsekvensen och hög tillgänglighet. Tabellen nedan visar garantierna för hög tillgänglighet som tillhandahålls av Cosmos DB för konton med en och flera regioner. Konfigurera alltid dina Cosmos-konton så att de har flera skrivregioner för hög tillgänglighet.

|Typ av åtgärd  | En region |Flera regioner (en region skriver)|Flera regioner (flerregionskrivningar) |
|---------|---------|---------|-------|
|Skriver    | 99,99    |99,99   |99,999|
|Läser     | 99,99    |99,999  |99,999|

> [!NOTE]
> I praktiken är den faktiska skrivtillgängligheten för begränsad föråldring, session, konsekventprefix och slutliga konsekvensmodeller betydligt högre än de publicerade SLA:erna. Den faktiska lästillgängligheten för alla konsekvensnivåer är betydligt högre än de publicerade SLA:erna.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Cosmos DB om ett regionalt avbrott inträffar

Regionala avbrott är inte ovanliga, men med Azure Cosmos DB har din databas alltid hög tillgänglighet. Följande information fångar Cosmos DB-beteende under ett avbrott, beroende på konfigurationen av Cosmos-kontot:

- Med Cosmos DB, innan en skrivåtgärd bekräftas för klienten, är data durably begås av ett kvorum av repliker inom regionen som accepterar skrivåtgärder.

- Multiregionkonton som konfigurerats med flera skrivregioner är mycket tillgängliga för både skrivningar och läsningar. Regionala redundansväxlingar är momentana och kräver inga ändringar från programmet.

- Konton med en region kan förlora tillgängligheten efter ett regionalt avbrott. Det rekommenderas alltid att ställa in **minst två regioner** (helst minst två skrivregioner) med ditt Cosmos-konto för att säkerställa hög tillgänglighet hela tiden.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Konton med flera regioner med en enda skrivregion (avbrott i skrivregionen)

- Under ett avbrott i skrivregionen befordras en sekundär region automatiskt till den nya primära skrivregionen när **aktivera automatisk redundans** konfigureras på Azure Cosmos-kontot. När den är aktiverad sker redundansen till en annan region i den ordning som den regionprioritet som du har angett.
- När den tidigare påverkade regionen är online igen görs alla skrivdata som inte replikerades när regionen misslyckades tillgängliga via [konfliktflödet](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konfliktflödet, lösa konflikterna baserat på den programspecifika logiken och skriva tillbaka de uppdaterade data till Azure Cosmos-behållaren.
- När den tidigare påverkade skrivregionen har återställts blir den automatiskt tillgänglig som en läsregion. Du kan växla tillbaka till den återställda regionen som skrivregion. Du kan byta region med [PowerShell, Azure CLI eller Azure portal](how-to-manage-database-account.md#manual-failover). Det finns **ingen förlust av data eller tillgänglighet** före, under eller efter att du byter skrivregion och ditt program fortsätter att vara högtillgänglighet.

> [!IMPORTANT]
> Vi rekommenderar starkt att du konfigurerar Azure Cosmos-konton som används för produktionsarbetsbelastningar för att **aktivera automatisk redundans**. Manuell redundans kräver anslutning mellan sekundär och primär skrivregion för att slutföra en konsekvenskontroll för att säkerställa att det inte finns någon dataförlust under redundansen. Om den primära regionen inte är tillgänglig kan den här konsekvenskontrollen inte slutföras och den manuella redundansen kommer inte att lyckas, vilket resulterar i förlust av skrivtillgänglighet.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Konton i flera regioner med en enda skrivregion (läsregionens avbrott)

- Under ett avbrott i läsregionen förblir Cosmos-konton som använder konsekvensnivå eller stark konsekvens med tre eller fler läsregioner mycket tillgängliga för läsningar och skrivningar.
- Det påverkade området kopplas automatiskt från och markeras offline. [Azure Cosmos DB SDK:er](sql-api-sdk-dotnet.md) omdirigerar läsanrop till nästa tillgängliga region i listan över önskade regioner.
- Om ingen av regionerna i listan över föredragna regioner är tillgänglig kan återgår anrop automatiskt till den aktuella skrivregionen.
- Inga ändringar krävs i programkoden för att hantera avbrott i läsregionen. När den påverkade läsregionen är online igen synkroniseras den automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att hantera läsbegäranden.
- Efterföljande läsningar omdirigeras till den återställda regionen utan att det krävs några ändringar i din programkod. Under både redundans och återanslutande av en tidigare misslyckad region fortsätter läskonsekvensgarantier att uppfyllas av Cosmos DB.

> [!IMPORTANT]
> Azure Cosmos-konton som använder stark konsekvens med två eller färre läsregioner förlorar skrivtillgänglighet under ett avbrott i läsregionen, men behåller lästillgängligheten för återstående regioner.

- Även i en sällsynt och olycklig händelse när Azure-regionen är permanent omöjlig att återställa, finns det ingen dataförlust om ditt Cosmos-konto med flera regioner är konfigurerat med *stark* konsekvens. I händelse av en permanent oåterkallelig skrivregion, ett Cosmos-konto med flera regioner som konfigurerats med begränsad föråldringskonsekvens, är det potentiella dataförlustfönstret begränsat till det inaktuella fönstret *(K* eller *T*) där K=100 000 uppdateras och T=5 minuter. För sessions-, konsekventprefix- och eventuella konsekvensnivåer är det potentiella dataförlustfönstret begränsat till högst 15 minuter. Mer information om RTO- och RPO-mål för Azure Cosmos DB finns i [Konsekvensnivåer och datahållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>Support för tillgänglighetszon

Förutom återhämtning mellan regioner kan du nu aktivera **zonredundans** när du väljer en region som ska associeras med din Azure Cosmos-databas.

Med stöd för tillgänglighetszon säkerställer Azure Cosmos DB att repliker placeras över flera zoner inom en viss region för att ge hög tillgänglighet och återhämtning vid zonfel. Det finns inga ändringar i svarstid och andra SLA:er i den här konfigurationen. I händelse av ett fel i en zon ger zonredundans fullständig datahållbarhet med RPO=0 och tillgänglighet med RTO=0.

Zonredundans är en *kompletterande funktion* till [multi-master replikeringsfunktionen.](how-to-multi-master.md) Enbart zonredag kan inte åberopas för att uppnå regional återhämtning. I händelse av regionala avbrott eller låg latensåtkomst i regionerna rekommenderas det att ha flera skrivregioner utöver zonredundans.

När du konfigurerar skrivningar med flera regioner för ditt Azure Cosmos-konto kan du välja zonredag utan extra kostnad. Annars kan du se anmärkningen nedan om prissättningen för zonredagvägningsstöd. Du kan aktivera zonredundans på en befintlig region i ditt Azure Cosmos-konto genom att ta bort regionen och lägga till den med zonen redundans aktiverad.

Den här funktionen är tillgänglig i följande Azure-regioner:

- Storbritannien, södra

- Sydostasien

- USA, östra

- USA, östra 2

- USA, centrala

- Europa, västra

- USA, västra 2

> [!NOTE]
> Om du aktiverar tillgänglighetszoner för ett enda Azure Cosmos-konto i en region resulterar i avgifter som motsvarar att lägga till ytterligare en region i ditt konto. Mer information om priser finns på [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) och [kostnaden för flera regioner i Azure Cosmos DB-artiklar.](optimize-cost-regions.md)

I följande tabell sammanfattas funktionen för hög tillgänglighet för olika kontokonfigurationer:

|KPI  |En region utan tillgänglighetszoner (icke-AZ)  |En region med tillgänglighetszoner (AZ)  |Flerregionskrivningar med tillgänglighetszoner (AZ, 2 regioner) – Den mest rekommenderade inställningen |
|---------|---------|---------|---------|
|SLA-ar för skrivtillgänglighet | 99,99 % | 99,99 % | 99,999 % |
|Läs tillgänglighet SLA  | 99,99 % | 99,99 % | 99,999 % |
|Pris | Faktureringsgrad för en region | Faktureringsfrekvens för en region i tillgänglighetszonen | Faktureringsfrekvens för flera regioner |
|Zonfel – dataförlust | Förlust av data | Ingen dataförlust | Ingen dataförlust |
|Zonfel – tillgänglighet | Tillgänglighet förlust | Ingen tillgänglighetsförlust | Ingen tillgänglighetsförlust |
|Läs latens | Tvärregion | Tvärregion | Låg |
|Skriv latens | Tvärregion | Tvärregion | Låg |
|Regionalt avbrott – dataförlust | Förlust av data |  Förlust av data | Förlust av data <br/><br/> När du använder begränsad föråldringskonsekvens med multi master och mer än en region, är dataförlust begränsad till den begränsade föråldring som konfigurerats på ditt konto <br /><br />Du kan undvika dataförlust under ett regionalt avbrott genom att konfigurera stark konsekvens med flera regioner. Det här alternativet levereras med kompromisser som påverkar tillgänglighet och prestanda. Den kan bara konfigureras för konton som är konfigurerade för en regionskrivningar. |
|Regionala avbrott – tillgänglighet | Tillgänglighet förlust | Tillgänglighet förlust | Ingen tillgänglighetsförlust |
|Dataflöde | X RU/s etablerat dataflöde | X RU/s etablerat dataflöde | 2X RU/s etablerat dataflöde <br/><br/> Det här konfigurationsläget kräver dubbelt så mycket dataflöde jämfört med en enda region med tillgänglighetszoner eftersom det finns två regioner. |

> [!NOTE]
> För att aktivera stöd för tillgänglighetszon för ett Azure Cosmos-konto i flera regioner måste kontot ha multi-master-skrivningar aktiverade.

Du kan aktivera zonredundans när du lägger till en region i nya eller befintliga Azure Cosmos-konton. Om du vill aktivera zonredundans på `isZoneRedundant` ditt `true` Azure Cosmos-konto bör du ange flaggan till för en viss plats. Du kan ange den här flaggan på egenskapen platser. Följande PowerShell-kodavsnitt möjliggör till exempel zonredundans för regionen Sydostasien:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

Följande kommando visar hur du aktiverar zonredundans för regionerna "EastUS" och "WestUS2":

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Du kan aktivera tillgänglighetszoner med hjälp av Azure-portalen när du skapar ett Azure Cosmos-konto. När du skapar ett konto måste du aktivera **georedundansen,** **flerregionskrivningar**och välja en region där tillgänglighetszoner stöds:

![Aktivera tillgänglighetszoner med Azure-portal](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Bygga program med högtillgänge

- Konfigurera Cosmos-kontot så att det sträcker sig över minst två regioner med flera skrivregioner för att säkerställa hög skriv- och lästillgänglighet. Den här konfigurationen ger högsta tillgänglighet, lägsta svarstid och bästa skalbarhet för både läsningar och skrivningar som stöds av SLA.This configuration will provide the highest availability, lowest laten och best scalability for both reads and writes backed by SLAs. Mer information finns i hur du [konfigurerar ditt Cosmos-konto med flera skrivregioner](tutorial-global-distribution-sql-api.md).

- För Cosmos-konton med flera regioner som är konfigurerade med en enda skrivregion [aktiverar du automatisk redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans, när det inträffar en regional katastrof, kommer Cosmos DB automatiskt redundans ditt konto.  

- Även om ditt Azure Cosmos-konto är mycket tillgängligt kanske ditt program inte är korrekt utformat för att förbli mycket tillgängligt. Om du vill testa den heltäckande hög tillgängligheten för ditt program, som en del av dina dr-övningar (application testing or disaster recovery), inaktiverar du tillfälligt automatisk redundans för kontot, anropar den [manuella redundansen med hjälp av PowerShell, Azure CLI eller Azure-portal](how-to-manage-database-account.md#manual-failover)och övervakar sedan programmets redundans. När du är klar kan du växla tillbaka till den primära regionen och återställa automatisk redundans för kontot.

- I en globalt distribuerad databasmiljö finns det ett direkt samband mellan konsekvensnivå och datahållbarhet i närvaro av ett avbrott i hela regionen. När du utvecklar din kontinuitetsplan måste du förstå den maximala godtagbara tiden innan programmet återhämtar sig helt efter en störande händelse. Den tid som krävs för att ett program ska kunna återställas helt kallas återställningstidsmål (RTO). Du måste också förstå den maximala perioden för de senaste datauppdateringarna som programmet kan tolerera att förlora när du återställer efter en störande händelse. Tidsperioden för uppdateringar som du kan ha råd att förlora kallas mål för återställningspunkt (RPO). Information om hur du ser RPO och RTO för Azure Cosmos DB finns i [Konsekvensnivåer och datahållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Därefter kan du läsa följande artiklar:

- [Tillgänglighets- och prestandaavvägningar för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
- [Skala etablerat dataflöde globalt](scaling-throughput.md)
- [Global distribution](global-dist-under-the-hood.md)
- [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
- [Konfigurera ditt Cosmos-konto med flera skrivregioner](how-to-multi-master.md)
