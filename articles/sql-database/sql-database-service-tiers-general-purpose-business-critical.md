---
title: Azure SQL Database – generell användning och affärskritisk | Microsoft Docs
description: Här beskrivs generella och kritiska-affärsnivå i vCore-köp av modellen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 2a9b84b20ab1480c9b0222980cb36c40e8017973
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989323"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database-tjänstnivåer

Azure SQL Database är baserad på SQL Server Database Engine-arkitektur som justeras för molnmiljön för att säkerställa 99,99% tillgänglighet även i fall av infrastrukturfel. Det finns tre arkitekturmodeller som används i Azure SQL Database:

- [Generella](sql-database-service-tier-general-purpose.md) utformad för de flesta av de allmänna arbetsbelastningarna.
- [Alternativet affärskritisk](sql-database-service-tier-business-critical.md) utformad för arbetsbelastningar med låg latens med en läsbar replik som helst.
- [Hyperskala](sql-database-service-tier-hyperscale.md) utformat för mycket stora databaser (upp till 100 TB) med flera läsbara repliker.

Den här artikeln beskrivs överväganden för lagring och säkerhetskopiering för generell användning och affärskritisk-tjänstnivåer i den vCore-baserade inköpsmodellen.

> [!NOTE]
> Mer information om tjänstnivån hyperskala i den vCore-baserade inköpsmodellen finns [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md). En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data-och logglagring

Tänk också på följande:

- Allokerat lagringsutrymme används av datafiler (MDF) och loggfiler för filer (LDF).
- Varje enskild databas beräkna storleken stöder den maximala databasstorleken med en maximal standardstorlek på 32 GB.
- När du konfigurerar den nödvändiga enda databasstorleken (storleken på MDF), läggs 30% av ytterligare lagringsutrymme automatiskt till stöd för LDF
- Lagringsstorlek i Managed Instance måste anges i multipler av 32 GB.
- Du kan välja valfri databasstorlek mellan 10 GB och högsta som stöds
  - För lagring på tjänstnivåerna standard eller allmänna, öka eller minska storleken i steg om 10 GB
  - För lagring på premium- eller buxiness kritiska-nivåer, öka eller minska storleken i steg om 250 GB
- På tjänstnivån generella `tempdb` använder anslutna SSD-disk och den här lagringskostnad ingår i priset för vCore.
- På tjänstnivån affärskritisk `tempdb` resurser anslutna SSD med MDF-filen och IDF-filer och tempDB lagringskostnad ingår i det vCore-priset.

> [!IMPORTANT]
> Du debiteras för det totala lagringsutrymmet som allokerats för MDF-filen och IDF.

Du övervakar den aktuella totala storleken på MDF-filen och IDF med [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Du övervakar den aktuella storleken för de enskilda MDF-filen och IDF-filerna med [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Säkerhetskopiering och lagring

Lagring för säkerhetskopior av databasen har allokerats för att stödja punkten i tiden återställa (PITR) och [Long Term Retention (LTR)](sql-database-long-term-retention.md) funktionerna i SQL-databas. Den här lagringen är tilldelat separat för varje databas och faktureras som två separata per databas avgifter.

- **PITR**: Säkerhetskopiering av enskilda databaser kopieras till [RA-GRS-lagring](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatiskt. Storlek ökar dynamiskt när nya säkerhetskopior har skapats.  Lagringsutrymmet används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopior av transaktionsloggar var femte minut. Lagringsanvändningen beror på ändringsfrekvensen i databasen och kvarhållningsperioden. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minimimängd lagringsutrymme som är lika med 1 x av storleken på data tillhandahålls utan extra kostnad. För de flesta databaser är beloppet tillräckligt för att lagra säkerhetskopiorna i 7 dagar.
- **LTR**: SQL Database erbjuder alternativet att konfigurera långsiktig kvarhållning av fullständiga säkerhetskopior för upp till 10 år. Om LTR principen är aktiverad, dessa säkerhetskopior lagras i RA-GRS-lagring automatiskt, men du kan styra hur ofta säkerhetskopiorna kopieras. Du kan välja olika kvarhållningsperioder för veckovisa, månatliga och årliga säkerhetskopior för att uppfylla olika krav. Den här konfigurationen definierar hur mycket lagringsutrymme som ska användas för LTR-säkerhetskopieringar. Du kan använda LTR priskalkylatorn för att beräkna kostnaden för LTR-lagring. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Nästa steg

- För att få information om specifika storlekar och lagring som kan användas för enkel databas på nivåerna för generell användning och kritiska företagstjänst, se [SQL Database vCore-baserade resursbegränsningar för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- För att få information om specifika storlekar och lagring som kan användas för elastiska pooler på nivåerna för generell användning och kritiska företagstjänst, se [SQL Database vCore-baserade resursbegränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
