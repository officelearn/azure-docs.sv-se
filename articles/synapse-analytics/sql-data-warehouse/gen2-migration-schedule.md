---
title: Migrera SQL-poolen till Gen2
description: Instruktioner för att migrera en befintlig SQL-pool till Gen2 och migreringsschemat per region.
services: synapse-analytics
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: synapse-analytics
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4714d5908fffb6f5c1440c3ec512fb8173da4b57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346765"
---
# <a name="upgrade-your-sql-pool-to-gen2"></a>Uppgradera din SQL-pool till Gen2

Microsoft hjälper till att minska kostnaden på ingångsnivå för att köra en SQL-pool.  Lägre beräkningsnivåer som kan hantera krävande frågor är nu tillgängliga för SQL-pool. Läs hela meddelandet [Lägre stöd för beräkningsnivå för Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Det nya erbjudandet finns i de regioner som anges i tabellen nedan. För regioner som stöds kan befintliga Gen1 SQL-pooler uppgraderas till Gen2 genom antingen:

- **Den automatiska uppgraderingsprocessen:** Automatiska uppgraderingar startar inte så snart tjänsten är tillgänglig i en region.  När automatiska uppgraderingar startar i en viss region sker enskilda uppgraderingar av informationslager under det valda underhållsschemat.
- [**Självuppgradering till Gen2:**](#self-upgrade-to-gen2) Du kan styra när du ska uppgradera genom att göra en självuppgradering till Gen2. Om din region ännu inte stöds kan du återställa från en återställningspunkt direkt till en Gen2-instans i en region som stöds.

## <a name="automated-schedule-and-region-availability-table"></a>Automatisk tillgänglighetstabell för schema och region

Följande tabell sammanfattar efter region när beräkningsnivån Nedre Gen2 är tillgänglig och när automatiska uppgraderingar startar. Datumen kan komma att ändras. Kom tillbaka för att se när din region blir tillgänglig.

\*anger att ett visst schema för regionen för närvarande inte är tillgängligt.

| **Regionen** | **Lägre Gen2 tillgänglig** | **Automatiska uppgraderingar börjar** |
|:--- |:--- |:--- |
| Kanada, östra |den 1 juni 2020 |den 1 juli 2020 |
| Kina, östra |\* |\* |
| Kina, norra |\* |\* |
| Tyskland, centrala |\* |\* |
| Tyskland Västra Central |Tillgängligt |Den 1 maj 2020 |
| Indien, västra |Tillgängligt |Den 1 maj 2020  |

## <a name="automatic-upgrade-process"></a>Automatisk uppgraderingsprocess

Baserat på tillgänglighetsdiagrammet ovan schemalägger vi automatiska uppgraderingar för dina Gen1-instanser. För att undvika oväntade avbrott i sql-poolens tillgänglighet schemaläggs de automatiska uppgraderingarna under underhållsschemat. Möjligheten att skapa en ny Gen1-instans inaktiveras i regioner som genomgår automatisk uppgradering till Gen2. Gen1 kommer att inaktuella när de automatiska uppgraderingarna har slutförts. Mer information om scheman finns i [Visa ett underhållsschema](maintenance-scheduling.md#view-a-maintenance-schedule)

Uppgraderingsprocessen kommer att innebära en kort minskning av anslutningen (ca 5 min) när vi startar om din SQL-pool.  När SQL-poolen har startats om är den fullt tillgänglig för användning. Du kan dock uppleva en försämring av prestanda medan uppgraderingsprocessen fortsätter att uppgradera datafilerna i bakgrunden. Hur länge prestandaförsämringen pågår beror på storleken på dina filer.

Du kan också påskynda uppgraderingsprocessen för datafiler genom att köra [Alter Index återskapa](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller med hjälp av en större SLO- och resursklass efter omstarten.

> [!NOTE]
> Alter Index återskapa är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän ombyggnaden är klar.

## <a name="self-upgrade-to-gen2"></a>Självuppgradering till Gen2

Du kan välja att självuppgradera genom att följa dessa steg på en befintlig Gen1 SQL-pool. Om du väljer att självuppgradera måste du slutföra den innan den automatiska uppgraderingen börjar i din region. Detta säkerställer att du undviker risken för att automatiska uppgraderingar orsakar en konflikt.

Det finns två alternativ när du utför en självuppgradering.  Du kan antingen uppgradera din nuvarande SQL-pool på plats eller så kan du återställa en Gen1 SQL-pool till en Gen2-instans.

- [Uppgradera på plats](upgrade-to-latest-generation.md) - Det här alternativet uppgraderar din befintliga Gen1 SQL-pool till Gen2. Uppgraderingsprocessen kommer att innebära en kort minskning av anslutningen (ca 5 min) när vi startar om din SQL-pool.  När SQL-poolen har startats om är den fullt tillgänglig för användning. Om du får problem under uppgraderingen öppnar du en [supportbegäran](sql-data-warehouse-get-started-create-support-ticket.md) och refererar till "Gen2-uppgradering" som möjlig orsak.
- [Uppgradera från återställningspunkten](sql-data-warehouse-restore-points.md) - Skapa en användardefinierad återställningspunkt på din aktuella Gen1 SQL-pool och återställ sedan direkt till en Gen2-instans. Den befintliga Gen1 SQL-poolen kommer att finnas kvar. När återställningen har slutförts kommer Din Gen2 SQL-pool att vara fullt tillgänglig för användning.  När du har kört alla test- och valideringsprocesser på den återställda Gen2-instansen kan den ursprungliga Gen1-instansen tas bort.

   - Steg 1: Skapa [en användardefinierad återställningspunkt från Azure-portalen](sql-data-warehouse-restore-active-paused-dw.md).
   - Steg 2: När du återställer från en användardefinierad återställningspunkt ställer du in "prestandanivå" på önskad Gen2-nivå.

Det kan uppstå en prestandaförsämring när datafilerna uppgraderas i bakgrunden. Hur länge prestandaförsämringen pågår beror på storleken på dina filer.

Om du vill påskynda migreringsprocessen för bakgrundsdata kan du omedelbart tvinga fram dataförflyttningar genom att köra [Alter Index återskapa](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller som du skulle fråga på en större SLO- och resursklass.

> [!NOTE]
> Alter Index återskapa är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän ombyggnaden är klar.

Om du stöter på några problem med din SQL-pool kan du skapa en [supportbegäran](sql-data-warehouse-get-started-create-support-ticket.md) och referera till "Gen2-uppgradering" som möjlig orsak.

Mer information finns i [Uppgradera till Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Vanliga frågor och svar om migrering

**F: Kostar Gen2 samma som Gen1?**

- S: Ja.

**F: Hur påverkar uppgraderingarna mina automatiseringsskript?**

- S: Alla automatiseringsskript som refererar till ett servicenivåmål bör ändras så att det motsvarar gen2-motsvarigheten.  Se detaljer [här](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**F: Hur lång tid tar en självuppgradering normalt?**

- S: Du kan uppgradera på plats eller uppgradera från en återställningspunkt.  
   - Om du uppgraderar på plats pausas och återupptas SQL-poolen tillfälligt.  En bakgrundsprocess fortsätter medan SQL-poolen är online.  
   - Det tar längre tid om du uppgraderar via en återställningspunkt, eftersom uppgraderingen kommer att gå igenom hela återställningsprocessen.

**F: Hur lång tid tar den automatiska uppgraderingen?**

- S: Den faktiska driftstopp för uppgraderingen är bara den tid det tar att pausa och återuppta tjänsten, vilket är mellan 5 och 10 minuter. Efter det korta driftstoppet körs en lagringsmigrering i en bakgrundsprocess. Hur lång tid som ska tas för bakgrundsprocessen beror på storleken på SQL-poolen.

**F: När kommer den automatiska uppgraderingen att ske?**

- S: Under underhållsschemat. Genom att utnyttja ditt valda underhållsschema minimeras störningar i ditt företag.

**F: Vad ska jag göra om min bakgrundsuppgraderingsprocess verkar ha fastnat?**

 - S: Starta en omindexering av dina Columnstore-tabeller. Observera att omindexeringen av tabellen kommer att vara offline under den här åtgärden.

**F: Vad händer om Gen2 inte har det servicenivåmål jag har på Gen1?**
- S: Om du kör en DW600 eller DW1200 på Gen1, rekommenderas att använda DW500c respektive DW1000c eftersom Gen2 ger mer minne, resurser och högre prestanda än Gen1.

**F: Kan jag inaktivera geo-säkerhetskopiering?**
- S: Nej. Geo-backup är en företagsfunktion för att bevara din SQL-pool tillgänglighet i händelse av att en region blir otillgänglig. Öppna en [supportbegäran](sql-data-warehouse-get-started-create-support-ticket.md) om du har ytterligare problem.

**F: Finns det en skillnad i T-SQL syntax mellan Gen1 och Gen2?**

- S: Det finns ingen förändring i syntaxen för T-SQL-språk från Gen1 till Gen2.

**F: Stöder Gen2 Underhåll Windows?**

- S: Ja.

**F: Kommer jag att kunna skapa en ny Gen1-instans efter att min region har uppgraderats?**

- S: Nej. När en region har uppgraderats inaktiveras skapandet av nya Gen1-instanser.

## <a name="next-steps"></a>Nästa steg

- [Uppgradera steg](upgrade-to-latest-generation.md)
- [Underhållsfönster](maintenance-scheduling.md)
- [Övervakare av resurshälsa](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Granska innan du påbörjar en migrering](upgrade-to-latest-generation.md#before-you-begin)
- [Uppgradera på plats och uppgradera från en återställningspunkt](upgrade-to-latest-generation.md)
- [Skapa en användardefinierad återställningspunkt](sql-data-warehouse-restore-points.md)
- [Lär dig hur du återställer till Gen2](sql-data-warehouse-restore-active-paused-dw.md)
- [Öppna en begäran om SQL Data Warehouse-stöd](https://go.microsoft.com/fwlink/?linkid=857950)
