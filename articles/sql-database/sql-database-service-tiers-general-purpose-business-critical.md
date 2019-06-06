---
title: Azure SQL Database – generell användning och affärskritisk | Microsoft Docs
description: Här beskrivs generella och företag kritiska-nivåer i den vCore-baserade inköpsmodellen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431342"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database-tjänstnivåer

Azure SQL Database är baserad på SQL Server database engine arkitektur som justeras för molnmiljö att säkerställa 99,99% tillgänglighet, även om det finns ett fel i infrastrukturen. Tre tjänstnivåer som används i Azure SQL Database, var och en med en annan arkitektur modell. Dessa är:

- [Generella](sql-database-service-tier-general-purpose.md), som är utformat för de flesta allmän arbetsbelastningar.
- [Affärskritisk](sql-database-service-tier-business-critical.md), som har utformats för låg fördröjning av arbetsbelastning med en läsbar replik som helst.
- [Hyperskala](sql-database-service-tier-hyperscale.md), som är utformat för mycket stora databaser (upp till 100 TB) med flera läsbara repliker.

Den här artikeln beskrivs överväganden för lagring och säkerhetskopiering för generell användning och företag kritiska-nivåer i den vCore-baserade inköpsmodellen.

> [!NOTE]
> Läs om hur hyperskala tjänstnivån i den vCore-baserade inköpsmodellen [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md). En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen i [köpa modeller och resurser för Azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data-och logglagring

Följande faktorer påverkar mängden lagringsutrymme som används för data och loggfiler:

- Allokerat lagringsutrymme används av datafiler (MDF) och loggfiler (LDF).
- Varje enskild databas beräkna storleken stöder den maximala databasstorleken med en maximal standardstorlek på 32 GB.
- När du konfigurerar den nödvändiga enda databasstorleken (storleken på MDF-filen), läggs automatiskt 30 procent mer ytterligare lagringsutrymme för LDF-filerna.
- Lagringsstorleken för en hanterad SQL Database-instans måste anges i multipler av 32 GB.
- Du kan välja valfri databasstorlek mellan 10 GB och högsta som stöds.
  - Öka eller minska storleken i steg om 10 GB för lagring på tjänstnivåerna standard eller allmänna.
  - För lagring i premium eller business kritiska tjänstnivåer, öka eller minska storleken i steg om 250 GB.
- På tjänstnivån generella `tempdb` använder anslutna SSD-disk och den här lagringskostnad ingår i priset för vCore.
- På affärsnivå kritiska- `tempdb` delar bifogade SSD med de MDF-filen och IDF-filerna och `tempdb` lagringskostnaden ingår i det vCore-priset.

> [!IMPORTANT]
> Du debiteras för det totala lagringsutrymmet som allokerats för MDF-filen och IDF-filer.

Du övervakar den aktuella totala storleken på MDF-filen och IDF-filer med [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Du övervakar den aktuella storleken för de enskilda MDF-filen och IDF-filerna med [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Säkerhetskopiering och lagring

Lagring för säkerhetskopior av databasen har allokerats för att stödja point-in-time-återställning (PITR) och [långsiktig kvarhållning av säkerhetskopior (LTR)](sql-database-long-term-retention.md) funktionerna i SQL-databas. Den här lagringen är tilldelat separat för varje databas och faktureras som två separata per databas avgifter.

- **PITR**: Säkerhetskopiering av enskilda databaser kopieras till [läsåtkomst till geografiskt redundant (RA-GRS) lagring](../storage/common/storage-designing-ha-apps-with-ragrs.md) automatiskt. Storlek ökar dynamiskt när nya säkerhetskopior har skapats. Lagringen används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopieringar och säkerhetskopieringar av transaktionsloggen, som kopieras var femte minut. Lagringsanvändningen beror på ändringsfrekvensen i databasen och kvarhållningsperioden för säkerhetskopior. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minimimängd lagringsutrymme som är lika med 100 procent (1 x) av databasens storlek tillhandahålls utan extra kostnad. För de flesta databaser är beloppet tillräckligt för att lagra säkerhetskopiorna i 7 dagar.
- **LTR**: SQL-databas kan du konfigurera långsiktig kvarhållning av fullständiga säkerhetskopior för upp till 10 år. Om du har konfigurerat en LTR-princip kan dessa säkerhetskopior lagras i RA-GRS-lagring automatiskt, men du kan styra hur ofta säkerhetskopiorna kopieras. Du kan välja olika kvarhållningsperioder för veckovisa, månatliga och årliga säkerhetskopior för att uppfylla olika standardkrav. Vilken konfiguration du väljer avgör hur mycket lagringsutrymme som ska användas för LTR säkerhetskopieringar. Du kan använda LTR priskalkylatorn för att beräkna kostnaden för LTR-lagring. Mer information finns i [SQL-databas med långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Nästa steg

- För att få information om specifikt storlekar och lagringsstorlekar som är tillgängliga för en enskild databas i generell användning och företag kritiska tjänstnivåer, se [SQL Database vCore-baserade resursbegränsningar för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- För att få information om specifikt storlekar och lagringsstorlekar som är tillgängliga för elastiska pooler i generell användning och företag kritiska tjänstnivåer, se [SQL Database vCore-baserade resursbegränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).
