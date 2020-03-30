---
title: Allmänt syfte och affärskritisk
description: I artikeln beskrivs de allmänna ändamålen och affärskritiska tjänstnivåerna i den vCore-baserade inköpsmodellen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937844"
---
# <a name="azure-sql-database-service-tiers"></a>Tjänstnivåer för Azure SQL Database

Azure SQL Database baseras på SQL Server-databasmotorarkitektur som har justerats för molnmiljön för att säkerställa 99,99 procents tillgänglighet, även om det finns ett infrastrukturfel. Tre tjänstnivåer används i Azure SQL Database, var och en med en annan arkitektonisk modell. Dessa tjänstnivåer är:

- [Allmänt ändamål](sql-database-service-tier-general-purpose.md), som är utformad för budgetorienterade arbetsbelastningar.
- [Hyperskala](sql-database-service-tier-hyperscale.md), som är utformad för de flesta affärsarbetsbelastningar, vilket ger mycket skalbar lagring, lässkalning och snabb databasåterställning.
- [Affärskritisk](sql-database-service-tier-business-critical.md),som är utformad för arbetsbelastningar med låg latens med hög motståndskraft mot fel och snabba redundans.

I den här artikeln beskrivs skillnader mellan tjänstnivåer, lagrings- och säkerhetskopieringsöverväganden för allmänna och affärskritiska tjänstnivåer i den vCore-baserade inköpsmodellen.

## <a name="service-tier-comparison"></a>Jämförelse på tjänstnivå

I följande tabell beskrivs de viktigaste skillnaderna mellan tjänstnivåer för den senaste generationen (Gen5). Observera att egenskaper på tjänstnivå kan vara olika i en enda databas och hanterad instans.

| | Resurstyp | Generell användning |  Hyperskala | Affärskritiskt |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** | |  Erbjuder budgetorienterade balanserade beräknings- och lagringsalternativ. | De flesta arbetsbelastningar för företag. Lagringsstorlek för automatisk skalning upp till 100 TB, flytande vertikal och horisontell beräkningsskalning, snabb databasåterställning. | OLTP-program med hög transaktionshastighet och låg IO-svarstid. Erbjuder högsta motståndskraft mot fel och snabba redundans med flera synkront uppdaterade repliker.|
|  **Tillgänglig i resurstyp:** ||Enkel databas / elastisk pool / hanterad instans | Enskild databas | Enkel databas / elastisk pool / hanterad instans |
| **Beräkna storlek**|Enkel databas / elastisk pool | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor |
| | Hanterad instans | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Ej tillämpligt | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| | Hanterade instanspooler | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Ej tillämpligt | Ej tillämpligt |
| **Lagringstyp** | Alla | Premium fjärrlagring (per instans) | Avkopplad lagring med lokal SSD-cache (per instans) | Supersnabb lokal SSD-lagring (per instans) |
| **Databasstorlek** | Enkel databas / elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Ej tillämpligt | 32 GB – 4 TB |
| **Lagringsstorlek** | Enkel databas / elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Ej tillämpligt | 32 GB – 4 TB |
| **TempDB-storlek** | Enkel databas / elastisk pool | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Hanterad instans  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Ej tillämpligt | Upp till 4 TB – [begränsad av lagringsstorlek](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Logga skrivgenomflöde** | Enskild databas | [1,875 MB/s per vCore (max 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (max 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Hanterad instans | [3 MB/s per vCore (max 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Ej tillämpligt | [4 MB/s per vcore (max 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Tillgänglighet**|Alla| 99,99 % |  [99.95% med en sekundär replika, 99.99% med fler repliker](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99.995% med zon redundant enda databas](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Säkerhetskopior**|Alla|RA-GRS, 7-35 dagar (7 dagar som standard)| RA-GRS, 7 dagar, konstant tidsåtgångsåterhämtning (PITR) | RA-GRS, 7-35 dagar (7 dagar som standard) |
|**OLTP i minnet** | | Ej tillämpligt | Ej tillämpligt | Tillgängligt |
|**Skrivskyddade repliker**| | 0 inbyggd <br> 0 - 4 med hjälp av [geo-replikering](sql-database-active-geo-replication.md) | 0 - 4 inbyggda | 1 inbyggd, ingår i pris <br> 0 - 4 med hjälp av [geo-replikering](sql-database-active-geo-replication.md) |
|**Prissättning/fakturering** | Enskild databas | [vCore, reserverad lagring och lagring av säkerhetskopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. | [vCore för varje replik och använt lagringsutrymme](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS ännu inte debiteras. | [vCore, reserverad lagring och lagring av säkerhetskopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. |
|| Managed Instance | [vCore, reserverad lagring och lagring av säkerhetskopiering](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte| Ej tillämpligt | [vCore, reserverad lagring och lagring av säkerhetskopiering](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte.| 
|**Rabattmodeller**| | [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (inte tillgängligt för dev/test-prenumerationer)<br/>[Företags-](https://azure.microsoft.com/offers/ms-azr-0148p/) och [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0023p/) utveckling/testprenumerationer| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (inte tillgängligt för dev/test-prenumerationer)<br/>[Företags-](https://azure.microsoft.com/offers/ms-azr-0148p/) och [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0023p/) utveckling/testprenumerationer| [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (inte tillgängligt för dev/test-prenumerationer)<br/>[Företags-](https://azure.microsoft.com/offers/ms-azr-0148p/) och [användningsbaserad](https://azure.microsoft.com/offers/ms-azr-0023p/) utveckling/testprenumerationer|

Mer information finns i de detaljerade skillnaderna mellan tjänstnivåerna i [virtuella databaser (vCore),](sql-database-vcore-resource-limits-single-databases.md) [virtuella databaspooler (vCore),](sql-database-dtu-resource-limits-single-databases.md) [DTU (Single Database),](sql-database-dtu-resource-limits-single-databases.md) [DTU (Single Database Pools)](sql-database-dtu-resource-limits-single-databases.md)och [Hanterade instanssidor.](sql-database-managed-instance-resource-limits.md)

> [!NOTE]
> Information om den hyperskala tjänstnivån i den vCore-baserade inköpsmodellen finns i [hyperskalatjänstnivå](sql-database-service-tier-hyperscale.md). En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen finns i [Azure SQL Database som köper modeller och resurser](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data- och logglagring

Följande faktorer påverkar mängden lagringsutrymme som används för data och loggfiler och gäller för allmänt syfte och affärskritiskt. Mer information om data och logglagring i Hyperskala finns i [Hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).

- Allokerad lagring används av datafiler (MDF) och loggfiler (LDF).
- Varje enskild databasberäkningsstorlek stöder en maximal databasstorlek, med en maximal standardstorlek på 32 GB.
- När du konfigurerar den enhetsstorlek som krävs (storleken på MDF-filen) läggs 30 procent mer lagringsutrymme automatiskt till för att stödja LDF-filer.
- Lagringsstorleken för en SQL Database-hanterad instans måste anges i multiplar på 32 GB.
- Du kan välja en enskild databasstorlek mellan 10 GB och det maximala antalet som stöds.
  - För lagring på standard- eller tjänstnivåerna för allmänt bruk ökar eller minskar du storleken i steg om 10 GB.
  - För lagring på premium- eller affärskritiska tjänstnivåer ökar eller minskar du storleken i steg om 250 GB.
- På den allmänna servicenivån `tempdb` använder du en bifogad SSD och den här lagringskostnaden ingår i vCore-priset.
- På den affärskritiska `tempdb` tjänstnivån delar den bifogade SSD-filen `tempdb` med MDF- och LDF-filerna och lagringskostnaden ingår i vCore-priset.

> [!IMPORTANT]
> Du debiteras för den totala lagring som allokerats för MDF- och LDF-filer.

Om du vill övervaka den aktuella totala storleken på MDF- och LDF-filerna använder [du sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Om du vill övervaka den aktuella storleken på de enskilda MDF- och LDF-filerna använder [du sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Säkerhetskopior och lagring

Lagring för säkerhetskopiering av databaser allokeras för att stödja point-in-time restore (PITR) och [långsiktiga bevarandefunktioner (LTR)](sql-database-long-term-retention.md) i SQL Database. Den här lagringen allokeras separat för varje databas och faktureras som två separata avgifter per databas.

- **PITR**: Enskilda databassäkerhetskopior kopieras automatiskt till geouppundant lagring (läsåtkomst).PITR : Individual database backups are copied to [read-access geo-redundant (RA-GRS) storage](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatically. Lagringsstorleken ökar dynamiskt när nya säkerhetskopior skapas. Lagringen används av veckovisa fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopiering av transaktionsloggar, som kopieras var femte minut. Lagringsförbrukningen beror på hur hög ändringshastigheten för databasen och kvarhållningsperioden för säkerhetskopiering. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. Ett minsta lagringsbelopp som motsvarar 100 procent (1x) av databasstorleken tillhandahålls utan extra kostnad. För de flesta databaser är det här beloppet tillräckligt för att lagra 7 dagars säkerhetskopior.
- **LTR:** SQL Database erbjuder dig möjligheten att konfigurera långsiktig lagring av fullständiga säkerhetskopior i upp till 10 år. Om du ställer in en LTR-princip lagras dessa säkerhetskopior automatiskt i RA-GRS-lagring, men du kan styra hur ofta säkerhetskopiorna kopieras. För att uppfylla olika efterlevnadskrav kan du välja olika kvarhållningsperioder för veckovisa, månatliga och/eller årliga säkerhetskopior. Den konfiguration du väljer avgör hur mycket lagringsutrymme som ska användas för LTR-säkerhetskopior. Om du vill uppskatta kostnaden för LTR-lagring kan du använda LTR-priskalkylatorn. Mer information finns i [SQL Database långtidslodande](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om specifika beräkningsstorlekar och lagringsstorlekar som är tillgängliga för en enskild databas på de allmänna nivåerna och affärskritiska tjänstnivåer finns i [SQL Database vCore-baserade resursgränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- Mer information om specifika beräkningsstorlekar och lagringsstorlekar som är tillgängliga för elastiska pooler på de allmänna nivåerna och affärskritiska tjänstnivåer finns i [SQL Database vCore-baserade resursgränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).
