---
title: Azure SQL Database automatisk, Geo-redundant säkerhets kopiering | Microsoft Docs
description: SQL Database skapar automatiskt en lokal databas säkerhets kopia med några minuters mellanrum och använder Azure Read-Access Geo-redundant lagring för GEO-redundans.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/22/2019
ms.openlocfilehash: 551c2c02af7b996a34a138586fd91a77a0455d92
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904325"
---
# <a name="automated-backups"></a>Automatiserade säkerhetskopieringar

SQL Database skapar automatiskt de databas säkerhets kopior som hålls mellan 7 och 35 dagar och använder Azure [Read-Access Geo-redundant lagring (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) för att säkerställa att de bevaras även om data centret inte är tillgängligt. Dessa säkerhets kopior skapas automatiskt. Databas säkerhets kopieringar är en viktig del av en strategi för affärs kontinuitet och haveri beredskap eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Om säkerhets reglerna kräver att dina säkerhets kopior är tillgängliga under en längre tid (upp till 10 år) kan du konfigurera en [långsiktig kvarhållning](sql-database-long-term-retention.md) på singleton-databaser och elastiska pooler.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Vad är en SQL Database säkerhets kopia

SQL Database använder SQL Server teknik för att skapa [fullständiga säkerhets kopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) varje vecka, [differentiella säkerhets kopieringar](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) var 12: e timme och [säkerhets kopior av transaktions loggar](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) var 5-10 minut. Säkerhets kopiorna lagras i [RA-GRS Storage-blobbar](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) som replikeras till ett [parat Data Center](../best-practices-availability-paired-regions.md) för skydd mot ett avbrott i data centret. När du återställer en databas, tar tjänsten reda på vilka säkerhets kopior av fullständig, differentiell och transaktions logg måste återställas.

Du kan använda dessa säkerhets kopior för att:

- **Återställ en befintlig databas till en** tidpunkt som redan har passerat under kvarhållningsperioden med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API. I en enkel databas och elastiska pooler skapar den här åtgärden en ny databas i samma server som den ursprungliga databasen. I en hanterad instans kan den här åtgärden Skapa en kopia av databasen eller samma eller en annan hanterad instans under samma prenumeration.
  - **[Ändra kvarhållning av säkerhets kopior](#how-to-change-the-pitr-backup-retention-period)** mellan 7 och 35 dagar för att konfigurera säkerhets kopierings principen.
  - **Ändra principen för långsiktig kvarhållning upp till 10 år** på enkel databas och elastiska pooler med hjälp [av Azure Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) eller [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Återställ en borttagen databas till den tidpunkt då den togs bort eller var** som helst under kvarhållningsperioden. Den borttagna databasen kan bara återställas på samma logiska Server eller hanterade instans där den ursprungliga databasen skapades.
- **Återställ en databas till en annan geografisk region**. Med geo-återställning kan du återställa från en geografisk katastrof när du inte har åtkomst till servern och databasen. Den skapar en ny databas på en befintlig server var som helst i världen.
- **Återställ en databas från en speciell långsiktig säkerhets kopia** på Enkel databas eller elastisk pool om databasen har kon figurer ATS med en långsiktig bevarande princip (brv). Med LTR kan du återställa en gammal version av databasen med hjälp [av Azure Portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) eller [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) för att uppfylla en begäran om efterlevnad eller köra en gammal version av programmet. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Information om hur du utför en återställning finns i [återställa databasen från säkerhets kopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure Storage refererar termen *replikering* till att kopiera filer från en plats till en annan. SQL- *databasreplikering* syftar till att hålla flera sekundära databaser synkroniserade med en primär databas.

Du kan prova några av de här åtgärderna med hjälp av följande exempel:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Ändra kvarhållning av säkerhets kopior | [Enkel databas](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-the-azure-portal) <br/> [Hanterad instans](sql-database-automated-backups.md#change-pitr-for-a-managed-instance) | [Enkel databas](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Ändra långsiktig kvarhållning av säkerhets kopior | [Enkel databas](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Hanterad instans-saknas  | [Enkel databas](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Hanterad instans-saknas  |
| Återställ databas från tidpunkt | [Enkel databas](sql-database-recovery-using-backups.md#point-in-time-restore) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Återställa borttagen databas | [Enkel databas](sql-database-recovery-using-backups.md#deleted-database-restore-using-the-azure-portal) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Återställa databasen från Azure Blob Storage | Enkel databas-ej tillämpligt <br/>Hanterad instans-saknas  | Enkel databas-ej tillämpligt <br/>[Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Hur länge säkerhets kopiorna sparas

Alla Azure SQL-databaser (enstaka, pooler och hanterade instans databaser) har en Standardkvarhållning av säkerhets kopior på **sju** dagar. Du kan [ändra kvarhållningsperioden för säkerhets kopior upp till 35 dagar](#how-to-change-the-pitr-backup-retention-period).

Om du tar bort en databas behåller SQL Database säkerhets kopiorna på samma sätt som en online-databas. Om du till exempel tar bort en Basic-databas som har en kvarhållningsperiod på sju dagar, sparas en säkerhets kopia som är fyra dagar gammal i tre dagar.

Om du behöver behålla säkerhets kopiorna längre än den maximala kvarhållningsperioden kan du ändra egenskaperna för säkerhets kopiering för att lägga till en eller flera långsiktiga kvarhållningsperiod i databasen. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Om du tar bort Azure SQL-servern som är värd för SQL-databaser, tas även alla elastiska pooler och databaser som tillhör servern bort och kan inte återställas. Det går inte att återställa en borttagen Server. Men om du har konfigurerat långsiktig kvarhållning tas säkerhets kopiorna för databaserna med LTR inte bort och dessa databaser kan återställas.

## <a name="how-often-do-backups-happen"></a>Hur ofta sker säkerhets kopieringar

### <a name="backups-for-point-in-time-restore"></a>Säkerhets kopieringar för återställning av tidpunkter

SQL Database stöder självbetjäning för återställning av PITR (Point-in-Time-återställning) genom att automatiskt skapa fullständig säkerhets kopiering, differentiella säkerhets kopieringar och säkerhets kopior av transaktions loggar. Fullständiga säkerhets kopior av databaser skapas varje vecka, och säkerhets kopior av differentiella databaser skapas vanligt vis var 12: e timme och säkerhets kopior av transaktions loggar skapas vanligt vis var 5-10 minut, med frekvensen baserat på beräknings storlek och mängden databas aktivitet. Den första fullständiga säkerhets kopieringen schemaläggs direkt efter att en databas har skapats. Den slutförs vanligt vis inom 30 minuter, men det kan ta längre tid när databasen har en betydande storlek. Den första säkerhets kopieringen kan till exempel ta längre tid på en återställd databas eller en databas kopia. Efter den första fullständiga säkerhets kopieringen schemaläggs alla ytterligare säkerhets kopieringar automatiskt och hanteras tyst i bakgrunden. Den exakta tiden för alla säkerhets kopieringar av databasen bestäms av SQL Database tjänsten när den balanserar den övergripande system belastningen. Du kan inte ändra eller inaktivera säkerhets kopierings jobben. 

PITR-säkerhetskopieringar är geo-redundanta och skyddade genom [Azure Storage över-regional replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Mer information finns i [punkt-in-Time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Säkerhets kopior för långsiktig kvarhållning

Med enkla och grupperade databaser kan du konfigurera långsiktig kvarhållning (brv) för fullständiga säkerhets kopieringar i upp till 10 år i Azure Blob Storage. Om LTR-principen är aktive rad kopieras de fullständiga vecko säkerhets kopieringarna automatiskt till en annan RA-GRS lagrings behållare. För att uppfylla olika krav för efterlevnad kan du välja olika kvarhållningsperiod för säkerhets kopiering varje vecka, månatlig och/eller år. Lagrings förbrukningen beror på den valda frekvensen av säkerhets kopior och kvarhållningsperioden. Du kan använda pris listan för [vanlig pris sättning](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för att beräkna kostnaden för LTR Storage.

Precis som PITR är säkerhets kopiorna för säkerhets kopian geo-redundanta och skyddade genom [Azure Storage över-regional replikering](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Mer information finns i [långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Lagringskostnader
För enskilda databaser tillhandahålls ett minsta antal säkerhets kopierings lagrings utrymme som är lika med 100% av databasens storlek utan extra kostnad. För elastiska pooler tillhandahålls minst 100% av det allokerade data lagrings utrymmet för poolen utan extra kostnad. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras för GB/månad. Den här ytterligare förbrukningen är beroende av arbets belastningen och storleken på de enskilda databaserna.

Mer information om lagrings priser finns på sidan med [priser](https://azure.microsoft.com/pricing/details/sql-database/single/) . 

## <a name="are-backups-encrypted"></a>Är säkerhets kopior krypterade

Om databasen är krypterad med TDE krypteras säkerhets kopiorna automatiskt i vila, inklusive LTR-säkerhetskopieringar. När TDE har Aktiver ATS för en Azure SQL-databas krypteras även säkerhets kopior. Alla nya Azure SQL-databaser konfigureras med TDE aktiverat som standard. Mer information om TDE finns i [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Hur garanterar Microsoft säkerhets kopierings integritet

Azure SQL Database teknik teamet kommer fort löp ande att testa återställning av automatiserade databas säkerhets kopior av databaser som placerats i logiska servrar och elastiska pooler (detta är inte tillgängligt i en hanterad instans). Vid tidpunkts återställning får-databaser också integritets kontroller med hjälp av DBCC CHECKDB.

Den hanterade instansen tar `CHECKSUM` automatisk första säkerhets kopiering med databaserna som återställs med hjälp av det interna `RESTORE` kommandot eller Datamigreringshanteraren när migreringen är klar.

Eventuella problem som hittas under integritets kontrollen resulterar i en avisering till teknik teamet. Mer information om data integritet i Azure SQL Database finns [i data integritet i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Hur kan automatiska säkerhets kopieringar påverka efterlevnad

När du migrerar databasen från en DTU-baserad tjänst nivå med standard PITR-kvarhållning på 35 dagar till en vCore tjänst nivå bevaras PITR kvarhållning för att säkerställa att programmets data återställnings princip inte komprometteras. Om Standardkvarhållning inte uppfyller dina krav kan du ändra PITR-kvarhållningsperioden med PowerShell eller REST API. Mer information finns i [ändra kvarhållningsperiod för säkerhets kopior](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Så här ändrar du bevarande perioden för PITR-säkerhetskopiering

Du kan ändra standardinställningen för kvarhållning av PITR med hjälp av Azure Portal, PowerShell eller REST API. De värden som stöds är: 7, 14, 21, 28 eller 35 dagar. I följande exempel visas hur du ändrar PITR kvarhållning till 28 dagar.

> [!WARNING]
> Om du minskar den aktuella kvarhållningsperioden är alla befintliga säkerhets kopior som är äldre än den nya kvarhållningsperioden inte längre tillgängliga. Om du ökar den aktuella kvarhållningsperioden, behåller SQL Database befintliga säkerhets kopior tills den längre kvarhållningsperioden har nåtts.

> [!NOTE]
> Dessa API: er påverkar endast PITR bevarande period. Om du har konfigurerat LTR för databasen påverkas den inte. Mer information om hur du ändrar kvarhållning av LTR-perioder finns i [långsiktig kvarhållning](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Ändra kvarhållning av PITR för säkerhets kopior med hjälp av Azure Portal

Om du vill ändra kvarhållningsperioden för PITR med hjälp av Azure Portal navigerar du till det Server objekt vars kvarhållningsperiod du vill ändra i portalen och väljer sedan lämpligt alternativ baserat på vilket Server objekt du ändrar.

#### <a name="change-pitr-for-a-sql-database-server"></a>Ändra PITR för en SQL Database Server

![Ändra PITR Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Ändra PITR för en hanterad instans

![Ändra PITR Azure Portal](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Ändra kvarhållning av PITR för säkerhets kopior med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Ändra PITR-kvarhållningsperiod med REST API

#### <a name="sample-request"></a>Exempelförfrågan

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Brödtext i förfrågan

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exempelsvar

Status kod: 200

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

Mer information finns i kvarhållning av [säkerhets kopior REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Nästa steg

- Databas säkerhets kopieringar är en viktig del av en strategi för affärs kontinuitet och haveri beredskap eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Mer information om andra Azure SQL Database affärs kontinuitets lösningar finns i [Översikt över affärs kontinuitet](sql-database-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure Portal, se Restore [Database till en tidpunkt med hjälp av Azure Portal](sql-database-recovery-using-backups.md).
- Om du vill återställa till en tidpunkt med hjälp av PowerShell, se Restore [Database till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Om du vill konfigurera, hantera och återställa från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av Azure Portal, se [Hantera långsiktig kvarhållning av säkerhets kopior med hjälp av Azure Portal](sql-database-long-term-backup-retention-configure.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av PowerShell finns i [Hantera långsiktig kvarhållning av säkerhets kopior med PowerShell](sql-database-long-term-backup-retention-configure.md).
