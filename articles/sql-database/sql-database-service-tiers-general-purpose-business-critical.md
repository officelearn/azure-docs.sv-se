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
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566706"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database tjänst nivåer

Azure SQL Database baseras på SQL Server databas motor arkitektur som har justerats för moln miljön för att säkerställa 99,99% tillgänglighet, även om det uppstår ett infrastruktur haveri. Tre tjänst nivåer används i Azure SQL Database, var och en med en annan arkitektur modell. Dessa tjänst nivåer är:

- [Generell användning](sql-database-service-tier-general-purpose.md), som är utformad för de flesta allmänna arbets belastningar.
- [Affärs kritisk](sql-database-service-tier-business-critical.md), som är utformad för arbets belastningar med låg latens med en läsbar replik.
- [Storskalig](sql-database-service-tier-hyperscale.md), som är utformad för mycket stora databaser (upp till 100 TB) med flera läsbara repliker.

Den här artikeln beskriver lagrings-och säkerhets kopierings överväganden för tjänst nivåerna allmänt och affärs kritisk i den vCore-baserade inköps modellen.

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
