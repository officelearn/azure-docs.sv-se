---
title: Hög tillgänglighet i Azure Cosmos DB
description: Den här artikeln beskriver hur Azure Cosmos DB ger hög tillgänglighet
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 74eee3d164e7ee3831f292568da9cf0620e576e5
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399291"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Hög tillgänglighet med Azure Cosmos DB

Azure Cosmos DB replikerar data transparent i alla de Azure-regioner som är associerade med ditt Cosmos-konto. Cosmos DB använder flera lager för redundans för dina data som visas i följande bild:

![Fysiska partitionering](./media/high-availability/cosmosdb-data-redundancy.png)

- Data i Cosmos-behållare är [vågrätt partitionerad](partitioning-overview.md).

- I varje region är varje partition skyddas av en replikuppsättning med alla skrivningar replikeras och hållbarheten har säkerställts med en majoritet av repliker. Replikerna är fördelade på upp till 10-20-feldomäner.

- Varje partition i alla regioner replikeras. Varje region kan innehåller alla data-partitionerna för en Cosmos-behållare och acceptera skrivningar och leverera läsningar.  

Om ditt Cosmos-konto är fördelade på *N* Azure-regioner, kommer det finnas minst *N* x 4 kopior av dina data. Förutom att tillhandahålla dataåtkomst med låg latens och skala dataflöde för skrivning/läsning över regioner som är associerade med ditt Cosmos-konto, med fler regioner (högre *N*) ytterligare förbättrar tillgängligheten.  

## <a name="slas-for-availability"></a>Serviceavtal för tillgänglighet

Cosmos DB tillhandahåller omfattande serviceavtal som omfattar genomflöde, svarstid på 99: e percentilen, konsekvens och hög tillgänglighet som en globalt distribuerad databas. Tabellen nedan visar garantier för hög tillgänglighet som tillhandahålls av Cosmos DB för konton för enskilda och flera regioner. Konfigurera alltid Cosmos-konton om du vill ha flera Skriv-regioner för hög tillgänglighet.

|Åtgärdstyp  | En enda region |Flera regioner (skriver en enda region)|Flera regioner (flera regioner skriver) |
|---------|---------|---------|-------|
|Skrivningar    | 99.99    |99.99   |99.999|
|Läsningar     | 99.99    |99.999  |99.999|

> [!NOTE]
> I praktiken är faktiska Skriv-tillgänglighet för begränsad föråldring, session, konsekventa prefix och slutlig konsekvensmodeller betydligt högre än de publicerade serviceavtal. Den faktiska lästillgänglighet för alla konsekvensnivåer är betydligt högre än de publicerade serviceavtal.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Hög tillgänglighet med Cosmos DB i händelse av regionala avbrott

Regionala avbrott är inte ovanligt och Azure Cosmos DB gör att din databas är alltid med hög tillgänglighet. Följande information används för att samla in Cosmos DB-beteende under ett avbrott, beroende på konfigurationen för Cosmos-kontot:

- Med Cosmos DB innan skrivning bekräftas till klienten, data är hållbarheten har säkerställts genom att ett kvorum av repliker i den region som accepterar skrivåtgärder.

- Flera regioner konton som konfigurerats med flera – Skriv-regioner ska ha hög tillgänglighet för både skrivningar och läsningar. Regionala redundanstestningar är omedelbara och kräver inte några ändringar från programmet.

- **Konton för flera regioner med en enda-skrivregionen (Skriv region avbrott):** Under ett avbrott på skrivning region förblir dessa konton med hög tillgänglighet för läsningar. Men för skrivningar måste du **”aktivera automatisk redundans”** på din Cosmos-konto till redundans påverkas region till en annan region. Redundans inträffar prioritsordning region du har angett. När den berörda regionen är online igen, unreplicated data på den berörda skrivregionen under avbrottet görs tillgängliga via den [konflikter feed](how-to-manage-conflicts.md#read-from-conflict-feed). Program kan läsa konflikterna feed konfliktlösning baserat på logiken programspecifika och skriva uppdaterade data tillbaka till Cosmos-behållare efter behov. När den tidigare berörda skrivregionen återställer blir automatiskt tillgängliga som en läsregionen. Du kan anropa en manuell redundans och konfigurera den berörda regionen som skrivregionen. Igen kan du göra en manuell redundans med hjälp av [Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover). Det finns **förlora data eller tillgänglighet** före, under eller efter manuell växling vid fel. Programmet fortsätter att ha hög tillgänglighet. 

- **Konton för flera regioner med en enda-skrivregionen (läsregionen avbrott):** Under ett avbrott på läsregionen förblir dessa konton med hög tillgänglighet för läsning och skrivning. Regionen påverkas kopplas ifrån automatiskt från skrivregionen och kommer att markeras offline. Den [Cosmos DB SDK](sql-api-sdk-dotnet.md) omdirigering läser anrop till den nästa tillgängliga regionen i listan över önskad region. Om ingen av regionerna i listan över önskad region är tillgänglig kan återgår anrop automatiskt till att den aktuella skrivregionen. Det krävs inga ändringar i din programkod för att hantera läsregionen avbrott. Så småningom när den berörda regionen är online igen, tidigare berörda läsregionen synkroniseras automatiskt med den aktuella skrivregionen och blir tillgänglig igen för att hantera läsbegäranden. Efterföljande läsningar omdirigeras till den återställda regionen utan några ändringar i din programkod. Under både redundans och ansluta igen i en tidigare misslyckade region, läsningskontinuitet garanterar även i fortsättningen att användas av Cosmos DB.

- En region konton kan förlora tillgänglighet efter ett regionalt strömavbrott. Vi rekommenderar alltid att du ställer in **minst två regioner** (helst skriva minst två regioner) med ditt Cosmos-konto för att säkerställa hög tillgänglighet vid alla tidpunkter.

- Även i en sällsynt och olycklig händelse när Azure-regionen är permanent oåterkalleligt, det finns inga data går förlorade om ditt konto för flera regioner Cosmos är konfigurerad med standard-konsekvensnivå av *starka*. I händelse av en permanent oåterkalleligt skrivregionen för flera regioner Cosmos-konton som konfigurerats med begränsad föråldring, konsekvens, potentiella dataförlustfönstret är begränsad till fönstret föråldring (*K* eller *T*); för sessionen, konsekvent prefix och slutlig konsekvensnivåer är potentiella dataförlustfönstret begränsad till högst fem sekunder. 

## <a name="availability-zone-support"></a>Tillgänglighetszonen har stöd

Azure Cosmos DB är en globalt distribuerad databas för flera huvudservrar-tjänst som ger hög tillgänglighet och återhämtning vid regionala avbrott. Även om du vill mellan region återhämtning, du kan nu aktivera **zon redundans** när du väljer en region för att associera med ditt Azure Cosmos-databas. 

Med stöd för Tillgänglighetszon kan garanterar Azure Cosmos DB repliker är placerade över flera zoner inom en viss region för att tillhandahålla hög tillgänglighet och återhämtning vid zonindelad haverier. Det finns inga ändringar svarstid och andra serviceavtal i den här konfigurationen. I händelse av ett fel i samma zon, redundans ger fullständig hållbarhet rpo = 0 och tillgänglighet med RTO = 0. 

Redundans är en *kompletterande funktionen* till den [Multi-Master-replikering](how-to-multi-master.md) funktionen. Redundans enbart det går inte att förlita sig på att uppnå regional återhämtning. Till exempel har det bäst för att ha flera Skriv-regioner förutom redundans vid regionala avbrott eller låglatensåtkomst i regionerna. 

När du konfigurerar skrivningar i flera regioner för ditt Azure Cosmos-konto, du kan välja redundans utan extra kostnad. Annars finns i avsnittet nedan om priserna för stöd för zonredundans. Du kan aktivera redundans på en befintlig region för Azure Cosmos-kontot genom att ta bort regionen och lägga till den med zonredundans aktiverat.

Den här funktionen är tillgänglig i följande Azure-regioner:

* Storbritannien, södra
* Sydostasien 

> [!NOTE] 
> Aktivera Tillgänglighetszoner för en enda region för Azure Cosmos-konto resulterar i kostnader som är likvärdiga med att lägga till en ytterligare region till ditt konto. Mer information om priser finns i den [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/) och [flera regioner kostnaden i Azure Cosmos DB](optimize-cost-regions.md) artiklar. 

I följande tabell sammanfattas möjligheterna för hög tillgänglighet för olika kontokonfigurationer: 

|KPI  |Enskild Region utan Tillgänglighetszoner (icke-AZ)  |En enda Region med Tillgänglighetszoner (AZ)  |Flera regioner med Tillgänglighetszoner (AZ, 2 regioner) – mest rekommenderad inställning |
|---------|---------|---------|---------|
|Skriva tillgänglighets-SLA     |   99,99 %      |    99,99 %     |  99.999%  |
|Läsa tillgänglighets-SLA   |   99,99 %      |   99,99 %      |  99.999%       |
|Pris  |  Debiteringen för en enda region |  Debiteringen för en enda region Tillgänglighetszon |  Debiteringen för flera regioner       |
|Zon fel – förlust av data   |  Förlust av data  |   Ingen dataförlust |   Ingen dataförlust  |
|Zon fel – tillgänglighet |  Förlust av tillgänglighet  | Förlora tillgänglighet  |  Förlora tillgänglighet  |
|Läsfördröjning    |  Mellan regioner    |   Mellan regioner   |    Låg  |
|Skrivfördröjning    |   Mellan regioner   |  Mellan regioner    |   Låg   |
|Regionalt strömavbrott – förlust av data    |   Förlust av data      |  Förlust av data       |   Förlust av data <br/><br/> När du använder bunden föråldring konsekvens med multi-huvudnod och fler än en region, är förlust av data begränsad till begränsad föråldring som konfigurerats på ditt konto. <br/><br/> Förlust av data under regionalt strömavbrott kan undvikas genom att konfigurera stark konsekvens med flera regioner. Det här alternativet kommer avkall som påverkar tillgänglighet och prestanda.      |
|Regionalt strömavbrott – tillgänglighet  |  Förlust av tillgänglighet       |  Förlust av tillgänglighet       |  Förlora tillgänglighet  |
|Dataflöde    |  X RU/s etablerade dataflöde      |  X RU/s etablerade dataflöde       |  2 x RU/s etablerade dataflöde <br/><br/> Det här konfigurationsläget kräver två gånger högre genomflöde jämfört med en enda region med Azure Availability Zones eftersom det finns två regioner.   |

Du kan aktivera redundans när du lägger till en region till en ny eller befintlig Azure Cosmos-konton. Du kan för närvarande kan bara aktivera redundans med hjälp av PowerShell eller Azure Resource Manager-mallar. För att aktivera redundans för ditt Azure Cosmos-konto, bör du ange den `isZoneRedundant` flaggan till `true` för en viss plats. Du kan ange den här flaggan i egenskapen platser. Till exempel kan följande powershell-kodavsnitt redundans för regionen ”Sydostasien”:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

## <a name="building-highly-available-applications"></a>Att skapa program med hög tillgänglighet

- Konfigurera Cosmos-konto för att omfatta minst två regioner med flera – Skriv-regioner för att säkerställa hög skrivning och lästillgänglighet. Den här konfigurationen ger högsta tillgänglighet, lägsta svarstid och bästa skalbarhet för både läsningar och skrivningar uppbackat av serviceavtal. Mer information finns i så här [konfigurera Cosmos-konto med flera Skriv-regioner](tutorial-global-distribution-sql-api.md).

- För flera regioner Cosmos-konton som är konfigurerade med en enda skrivregionen, [aktivera automatisk redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#automatic-failover). När du har aktiverat automatisk redundans när det finns ett regionalt haveri redundansväxlas Cosmos DB automatiskt ditt konto.  

- Även om ditt Cosmos-konto är med hög tillgänglighet kan kanske programmet inte korrekt utformas för att fortsätta att vara tillgänglig. Om du vill testa tillgängligheten slutpunkt till slutpunkt för ditt program med jämna mellanrum anropa den [manuell redundans med hjälp av Azure CLI eller Azure-portalen](how-to-manage-database-account.md#manual-failover), som en del av din Programtestning eller haveriberedskap (DR) tester.

- I en globalt distribuerad databas för miljön, att det finns en direkt relation mellan konsekvens nivå och data hållbarhet när det finns ett avbrott på hela. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för ett program för att återställa kallas återställningstid (RTO). Du måste också att förstå den längsta tid för senaste datauppdateringar som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse. Tid då uppdateringar som du kanske har råd att förlora kallas mål för återställningspunkt (RPO). Om du vill se vilket RPO och RTO för Azure Cosmos DB, se [konsekvens nivåer och data hållbarhet](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Nästa steg

Därefter kan du läsa följande artiklar:

* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Skala globalt etablerat dataflöde](scaling-throughput.md)
* [Global distribution – under huven](global-dist-under-the-hood.md)
* [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md)
* [Så här konfigurerar du ditt Cosmos-konto med flera Skriv-regioner](how-to-multi-master.md)
