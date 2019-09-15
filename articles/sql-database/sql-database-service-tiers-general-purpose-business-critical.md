---
title: Azure SQL Database generell användning och affärs kritiskt | Microsoft Docs
description: I artikeln beskrivs allmänna affärs kritiska tjänst nivåer i den vCore-baserade inköps modellen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: 41acef4ebe13ac6152d795db4adfae5a6ae1ad91
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995435"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database tjänst nivåer

Azure SQL Database baseras på SQL Server databas motor arkitektur som har justerats för moln miljön för att säkerställa 99,99% tillgänglighet, även om det uppstår ett infrastruktur haveri. Tre tjänst nivåer används i Azure SQL Database, var och en med en annan arkitektur modell. Dessa tjänst nivåer är:

- [Generell användning](sql-database-service-tier-general-purpose.md), som är utformad för de flesta allmänna arbets belastningar.
- [Affärs kritisk](sql-database-service-tier-business-critical.md), som är utformad för arbets belastningar med låg latens med en läsbar replik.
- [Storskalig](sql-database-service-tier-hyperscale.md), som är utformad för mycket stora databaser (upp till 100 TB) med flera läsbara repliker.

I den här artikeln beskrivs skillnaderna är · att interpolera tjänst nivåerna, lagrings-och säkerhets kopierings överväganden för de allmänna och affärs kritiska tjänst nivåerna i den vCore-baserade inköps modellen.

## <a name="service-tier-comparison"></a>Jämförelse av service nivå

I följande tabell beskrivs viktiga skillnader mellan tjänst nivåer för den senaste generationen (Gen5). Observera att tjänst nivå egenskaperna kan vara annorlunda i Enkel databas och hanterad instans.

| | Resurstyp | Generellt syfte |  Storskalig | Verksamhetskritisk |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** | |  De flesta företags arbets belastningar. Erbjuder budget orienterade balanserade beräknings-och lagrings alternativ. | Data program med stora data kapacitets krav, möjlighet att automatiskt skala lagring upp till 100 TB och skala beräknings vätskan. | OLTP-program med hög transaktions hastighet och lägsta latens i/o. Ger högsta möjliga återhämtning till problem med flera isolerade repliker.|
|  **Tillgängligt i resurs typ:** ||Enkel databas/elastisk pool/hanterad instans | Enskild databas | Enkel databas/elastisk pool/hanterad instans |
| **Beräknings storlek**|Enkel databas/elastisk pool | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor |
| | Hanterad instans | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Gäller inte | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| | Hanterade instanser av pooler | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Gäller inte | Gäller inte |
| **Lagringstyp** | Alla | Premium-Fjärrlagring (per instans) | Fristående lagring med lokal SSD-cache (per instans) | Super-fast lokal SSD-lagring (per instans) |
| **Databas storlek** | Enkel databas/elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Gäller inte | 32 GB – 4 TB |
| **Lagrings storlek** | Enkel databas/elastisk pool | 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| | Hanterad instans  | 32 GB – 8 TB | Gäller inte | 32 GB – 4 TB |
| **TempDB-storlek** | Enkel databas/elastisk pool | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | Hanterad instans  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Gäller inte | Upp till 4 TB – [begränsas av lagrings storlek](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Logg skrivnings data flöde** | Enskild databas | [1,875 MB/s per vCore (max 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | 100 MB/s | [6 MB/s per vCore (max 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | Hanterad instans | [3 MB/s per vCore (högst 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Gäller inte | [4 MB/s per vCore (max 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Tillgänglighet**|Alla| 99,99 % |  [99,95% med en sekundär replik, 99,99% med fler repliker](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995% med redundant zon i en enskild databas](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Regelbundet**|Alla|RA-GRS, 7-35 dagar (7 dagar som standard)| RA-GRS, 7 dagar, konstant tidpunkts återställning (PITR) | RA-GRS, 7-35 dagar (7 dagar som standard) |
|**Minnes intern OLTP** | | Gäller inte | Gäller inte | Tillgängligt |
|**Skrivskyddade repliker**| | 0  | 0 - 4 | 1 (inbyggt, ingår i priset) |
|**Priser/fakturering** | Enskild databas | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. | [vCore för varje replik och använt lagrings utrymme](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte.<br/>Lagring av säkerhets kopior debiteras inte ännu. | [vCore, reserverad lagring och lagring av säkerhets kopior](https://azure.microsoft.com/pricing/details/sql-database/single/) debiteras. <br/>IOPS debiteras inte. |
|| Managed Instance | [vCore och reserverad lagring](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte.<br/>Lagring av säkerhets kopior debiteras inte ännu. | Gäller inte | [vCore och reserverad lagring](https://azure.microsoft.com/pricing/details/sql-database/managed/) debiteras. <br/>IOPS debiteras inte.<br/>Lagring av säkerhets kopior debiteras inte ännu. | 
|**Rabatt modeller**| | [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid-förmån](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning| [Azure Hybrid-förmån](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning| [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid-förmån](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (inte tillgängligt för utveckling/testning-prenumerationer)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) och [betala](https://azure.microsoft.com/offers/ms-azr-0023p/) per användning-prenumerationer för utveckling och testning|

Mer information finns i de detaljerade skillnaderna mellan tjänst nivåerna i [en enskild databas (vCore)](sql-database-vcore-resource-limits-single-databases.md), vCore (Single [Database Pools](sql-database-dtu-resource-limits-single-databases.md)), [en enskild databas (DTU)](sql-database-dtu-resource-limits-single-databases.md), [en pool med ett enda databas (DTU) och en](sql-database-dtu-resource-limits-single-databases.md) [hanterad instans](sql-database-managed-instance-resource-limits.md) kodsidor.

> [!NOTE]
> Information om den storskaliga tjänst nivån i den vCore-baserade inköps modellen finns i [storskalig Service Tier](sql-database-service-tier-hyperscale.md). En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data-och logg lagring

Följande faktorer påverkar mängden lagrings utrymme som används för data-och loggfiler:

- Det allokerade lagrings utrymmet används av data filer (MDF) och loggfiler (LDF).
- Varje beräknings storlek för en enskild databas stöder en maximal databas storlek, med en maximal standard storlek på 32 GB.
- När du konfigurerar den nödvändiga storleken för enskild databas (storleken på MDF-filen) läggs 30 procent ytterligare lagrings utrymme automatiskt till för att ge stöd för LDF-filer.
- Lagrings storleken för en SQL Database Hanterad instans måste anges i multipler av 32 GB.
- Du kan välja en enskild databas storlek mellan 10 GB och den maximala storleken som stöds.
  - Öka eller minska storleken i steg om 10 GB för lagring i tjänst nivåerna standard eller allmän användning.
  - Öka eller minska storleken i steg om 250 GB för lagring på tjänst nivåerna Premium eller affärs kritisk.
- I tjänst nivån `tempdb` generell användning används en ansluten SSD och den här lagrings kostnaden ingår i vCore-priset.
- På nivån `tempdb` verksamhets kritisk tjänst delar den anslutna SSD med MDF-och ldf-filerna `tempdb` och lagrings kostnaden ingår i vCore-priset.

> [!IMPORTANT]
> Du debiteras för den totala lagring som allokerats för MDF-och LDF-filer.

Använd [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)om du vill övervaka den aktuella totala storleken på dina MDF-och LDF-filer. Om du vill övervaka den aktuella storleken på de enskilda MDF-och LDF-filerna använder du [sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Säkerhets kopiering och lagring

Lagring för säkerhets kopiering av databaser allokeras till stöd för PITR (Point-in-Time Restore) och [LTR-funktioner (långsiktig kvarhållning)](sql-database-long-term-retention.md) i SQL Database. Den här lagringen allokeras separat för varje databas och faktureras som två separata avgifter per databas.

- **PITR**: Enskilda databas säkerhets kopior kopieras till [lagring med Läs åtkomst till Geo-redundant lagring (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatiskt. Lagrings storleken ökar dynamiskt när nya säkerhets kopior skapas. Lagringen används av varje veckovis fullständig säkerhets kopiering, dagliga differentiella säkerhets kopieringar och säkerhets kopior av transaktions loggar, som kopieras var femte minut. Lagrings användningen beror på ändrings takten för databasen och kvarhållningsperioden för säkerhets kopieringar. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minsta lagrings mängd som motsvarar 100 procent (1x) av databas storleken tillhandahålls utan extra kostnad. För de flesta databaser räcker det att lagra 7 dagars säkerhets kopieringar.
- **LTR**: SQL Database ger dig möjlighet att konfigurera långsiktig kvarhållning av fullständiga säkerhets kopior i upp till 10 år. Om du konfigurerar en LTR-princip lagras dessa säkerhets kopior i RA-GRS-lagring automatiskt, men du kan styra hur ofta säkerhets kopiorna ska kopieras. För att uppfylla olika krav på efterlevnad kan du välja olika kvarhållningsperiod för säkerhets kopiering varje vecka, månatlig och per år. Konfigurationen du väljer avgör hur mycket lagrings utrymme som ska användas för säkerhets kopieringar med LTR. Om du vill uppskatta kostnaden för den LTR Storage-lagringen kan du använda pris listan för vanlig prissättning. Mer information finns i [SQL Database långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om de specifika beräknings storlekarna och lagrings storlekar som är tillgängliga för en enskild databas finns i [SQL Database vCore resurs gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- Mer information om de specifika beräknings storlekarna och lagrings storlekarna som är tillgängliga för elastiska pooler i tjänste nivåerna allmän användning och affärs kritiska tjänst finns i [SQL Database vCore resurs gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).
