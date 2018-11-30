---
title: Automatisk, geo-redundanta säkerhetskopieringar i Azure SQL Database | Microsoft Docs
description: SQL-databas och skapar en lokal databassäkerhetskopia några minuters mellanrum och använder Azure läsåtkomst till geografiskt redundant lagring för geo-redundans automatiskt.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 9c5cdf6c2baf4197b693b522848fc1fd04db7abf
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422518"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Mer information om automatisk SQL Database-säkerhetskopior

SQL Database skapar säkerhetskopior av databasen och använder Azure read-access geo-redundant lagring (RA-GRS) för att tillhandahålla geografisk redundans automatiskt. Dessa säkerhetskopior skapas automatiskt och utan extra kostnad. Du behöver inte göra något för att det ska hända. Säkerhetskopior av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Om din säkerhetsregler kräver att dina säkerhetskopior som är tillgängliga under en längre tid kan konfigurera du principen för långsiktig kvarhållning av säkerhetskopior. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Vad är en SQL Database-säkerhetskopia

SQL Database använder SQL Server-teknik för att skapa [fullständig](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [differentiell](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server), och [transaktionsloggen](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) säkerhetskopieringar för Point-in-time-återställning (PITR). Säkerhetskopieringarna av transaktionsloggen vanligtvis sker var 5 – 10 minuter och differentiella säkerhetskopieringar inträffar vanligen var tolfte timme med den frekvens som är baserat på beräkningsstorleken och mängden Databasaktivitet. Fullständig, differentiell och transaktionen loggsäkerhetskopior kan du återställa en databas till en specifik point-in-time till samma server som är värd för databasen. Säkerhetskopiorna lagras i RA-GRS storage-blobbar som replikeras till en [kopplat Datacenter](../best-practices-availability-paired-regions.md) för skydd mot avbrott i datacentret. När du återställer en databas kan databas tjänsten där fullständig, differentiell och transaktionen loggfiler säkerhetskopieringar ska återställas.

Du kan använda dessa säkerhetskopior till:

- Återställa en databas till point-in-time inom kvarhållningsperioden. Den här åtgärden skapar en ny databas på samma server som den ursprungliga databasen.
- Återställa en borttagen databas till den tid som den har tagits bort eller när som helst inom kvarhållningsperioden. Den borttagna databasen kan bara återställas på samma server som var den ursprungliga databasen har skapats.
- Återställa en databas till en annan geografisk region. På så sätt kan du återställa en geografisk katastrofåterställning när du inte åtkomst till din server och databas. En ny databas skapas i en befintlig server var som helst i världen.
- Återställa en databas från en specifik långsiktig säkerhetskopiering om databasen har konfigurerats med en princip för långsiktig kvarhållning av säkerhetskopior (LTR). På så sätt kan du återställa en äldre version av databasen att uppfylla en begäran om efterlevnad eller för att köra en äldre version av programmet. Se [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Om du vill utföra en återställning, se [återställa databasen från säkerhetskopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure storage termen *replikering* syftar på kopiering av filer från en plats till en annan. SQL: s *databasreplikering* refererar till att hålla flera sekundära databaser synkroniseras med en primär databas.

## <a name="how-long-are-backups-kept"></a>Hur lång tid hålls säkerhetskopior

Varje SQL-databas har en standard-kvarhållningsperiod mellan 7 och 35 dagar som är beroende av den [inköpsmodellen och tjänstnivå](#pitr-retention-period). Du kan uppdatera kvarhållningsperioden för säkerhetskopior för en databas på Azure logisk Server (den här funktionen kommer att aktiveras snart i Managed Instance). Se [ändra kvarhållningsperiod](#how-to-change-backup-retention-period) för mer information.

Om du tar bort en databas, behåller SQL Database säkerhetskopiorna på samma sätt som den skulle ha gjort för en online-databas. Om du tar bort en Basic-databas som har en kvarhållningsperiod på sju dagar, till exempel sparas en säkerhetskopia som är fyra dagar gamla i tre dagar.

Om du vill behålla säkerhetskopior under längre tid än den maximala kvarhållningsperioden för PITR kan ändra du egenskaper för säkerhetskopiering för att lägga till en eller flera långsiktiga kvarhållningsperioder till din databas. Se [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) för mer information.

> [!IMPORTANT]
> Om du tar bort Azure SQL-servern som är värd för SQL-databaser, raderas också alla elastiska pooler och databaser som hör till servern och kan inte återställas. Du kan inte återställa en borttagen server. Men om du har konfigurerat långsiktig kvarhållning kommer inte att ta bort säkerhetskopior för databaser med LTR och dessa databaser kan återställas.

### <a name="pitr-retention-period"></a>PITR kvarhållningsperiod

#### <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

Standardkvarhållningsperioden för en databas som skapats med den DTU-baserade inköpsmodellen beror på tjänstnivån:

- Grundläggande tjänstenivå är 1 vecka.
- Standard-tjänstnivå är fem veckor.
- Premiumnivån är fem veckor.

#### <a name="vcore-based-purchasing-model"></a>Virtuell kärna-baserad inköpsmodell

Om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), standard-kvarhållningsperiod är 7 dagar (både på logiska servrar och hanterade instanser).

- För enkel och delade databaser, kan du [ändra kvarhållningsperioden för säkerhetskopior upp till 35 dagar](#how-to-change-backup-retention-period).
- Ändra kvarhållningsperiod för säkerhetskopiering är inte tillgänglig i hanterade instanser.

Om du minskar den aktuella kvarhållningsperioden är alla befintliga säkerhetskopior som är äldre än den nya kvarhållningen tidsperiod inte längre tillgänglig. Om du ökar den aktuella kvarhållningsperioden, behåller SQL Database befintliga säkerhetskopior tills den längre kvarhållningsperioden har uppnåtts.

## <a name="how-often-do-backups-happen"></a>Hur ofta händer säkerhetskopior

### <a name="backups-for-point-in-time-restore"></a>Säkerhetskopieringar för point-in-time-återställning

SQL Database stöder Självbetjäning för point-in-time-återställning (PITR) genom att automatiskt skapa fullständig säkerhetskopiering, differentiella säkerhetskopieringar och säkerhetskopieringar av transaktionsloggen. Fullständiga databassäkerhetskopieringar skapas varje vecka, differentiella säkerhetskopieringar skapas vanligtvis var 12: e timme och säkerhetskopieringar av transaktionsloggen skapas vanligtvis var 5-10 minut, med frekvensen baserat på beräkningsstorleken och mängden Databasaktivitet. Den första fullständiga säkerhetskopian schemaläggs omedelbart efter att en databas har skapats. Slutförs vanligtvis inom 30 minuter, men det kan ta längre tid när databasen är av betydande storlek. Den första säkerhetskopieringen kan till exempel ta längre tid på en återställd databas eller en databaskopia. Efter den första fullständiga säkerhetskopian är alla ytterligare säkerhetskopieringar schemalagda automatiskt och hanteras tyst i bakgrunden. Exakta tidsinställningen för alla databassäkerhetskopior bestäms av tjänsten SQL Database som balanseras övergripande systemets arbetsbelastning.

PITR säkerhetskopiorna är geo-redundant och skyddas av [tvärregional Azure Storage-replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Mer information finns i [Point-in-time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Säkerhetskopior för långsiktig kvarhållning

SQL-databas som finns på logisk Server erbjuder alternativet att konfigurera långsiktig kvarhållning av säkerhetskopior (LTR) av fullständiga säkerhetskopior för upp till 10 år i Azure blob storage. Om LTR principen är aktiverad, kopieras de veckovisa, fullständiga säkerhetskopiorna automatiskt till en annan behållare för RA-GRS-lagring. Du kan välja olika kvarhållningsperioder för veckovisa, månatliga och årliga säkerhetskopior för att uppfylla olika krav. Lagringsanvändningen beror på den valda frekvensen för säkerhetskopiering och kvarhållning längd. Du kan använda den [LTR priskalkylator](https://azure.microsoft.com/pricing/calculator/?service=sql-database) att uppskatta kostnaden för LTR-lagring.

Precis som PITR, LTR-säkerhetskopior är geo-redundant och skyddas av [Azure Storage-replikering mellan tillgänglighetszoner](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Mer information finns i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Krypteras säkerhetskopior

Om databasen är krypterad med transparent Datakryptering krypteras automatiskt säkerhetskopior i vila, inklusive LTR säkerhetskopior. Säkerhetskopiorna krypteras också när TDE är aktiverat för en Azure SQL-databas. Alla nya Azure SQL-databaser är konfigurerade med TDE aktiverat som standard. Läs mer på TDE [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Hur säkerställa integriteten för säkerhetskopiering av Microsoft

Med jämna mellanrum testar det tekniska teamet för Azure SQL Database automatiskt återställning av automatiska säkerhetskopior av databaser för tjänsten. Vid en återställning får databaser även integritetskontroller med hjälp av DBCC CHECKDB. Eventuella problem som hittades under integritetskontrollen resulterar i en avisering till teknikteamet. Mer information om dataintegriteten i Azure SQL Database finns i [dataintegriteten i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Hur påverkas Mina efterlevnad av automatiska säkerhetskopieringar

När du migrerar din databas från en DTU-baserade tjänstnivå med standard PITR kvarhållning av 35 dagar till en vCore-baserade tjänstnivå bevaras PITR kvarhållning för att säkerställa att ditt programs data återställningsprincipen inte komprometteras. Om Standardkvarhållning inte uppfyller efterlevnadskraven kan ändra du kvarhållningsperioden PITR med PowerShell eller REST API. Se [ändra kvarhållningsperiod](#how-to-change-backup-retention-period) för mer information.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Så här ändrar du kvarhållningsperiod för säkerhetskopiering

> [!Note]
> Standard kvarhållningsperiod (7 dagar) kan inte ändras på hanterad instans.

Du kan ändra den standard kvarhållning av säkerhetskopior med REST API eller PowerShell. Godkända värden är: 7, 14, 21, 28 eller 35 dagar. I följande exempel visas hur du ändrar PITR kvarhållning till 28 dagar.

> [!NOTE]
> Thes APIs påverkar endast PITR kvarhållningsperioden. Om du har konfigurerat LTR för din databas kommer det inte att påverkas. Se [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) för information om hur du ändrar LTR kvarhållning längd.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Ändra PITR kvarhållningsperiod för säkerhetskopior med hjälp av PowerShell

```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

> [!IMPORTANT]
> Den här API: et ingår i i AzureRM.Sql PowerShell-modul från och med version [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview).

### <a name="change-pitr-retention-period-using-rest-api"></a>Ändra PITR kvarhållningsperiod med REST API

#### <a name="sample-request"></a>Exempelförfrågan

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Begärandetext

```json
{
  "properties":{  
      "retentionDays":28
   }
}
```

#### <a name="sample-response"></a>Exempelsvar

Statuskod: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Se [säkerhetskopiering kvarhållning REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) för mer information.

## <a name="next-steps"></a>Nästa steg

- Säkerhetskopior av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Läs mer om de andra Azure SQL Database lösningarna för affärskontinuitet, i [översikt över affärskontinuitet](sql-database-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure-portalen, se [återställa databasen till en tidpunkt med hjälp av Azure-portalen](sql-database-recovery-using-backups.md).
- Om du vill återställa till en tidpunkt med hjälp av PowerShell, se [återställa databasen till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Att konfigurera, hantera och återställa från långsiktig kvarhållning av automatiska säkerhetskopior i Azure blob storage med Azure portal, se [hantera långsiktig kvarhållning av säkerhetskopior med Azure portal](sql-database-long-term-backup-retention-configure.md).
- Att konfigurera, hantera och återställa från långsiktig kvarhållning av automatiska säkerhetskopior i Azure BLOB-lagring med hjälp av PowerShell, se [hantera långsiktig kvarhållning av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).
