---
title: Automatisk och Geo-redundant säkerhets kopiering
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database och Azure SQL-hanterad instans skapar automatiskt en lokal databas säkerhets kopia med några minuters mellanrum och använder Azure Read-Access Geo-redundant lagring för GEO-redundans.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: a99045359d9d6c6a327ef646ba15457c3e4fbbda
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299816"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>Automatiserade säkerhets kopieringar – Azure SQL Database & SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Både Azure SQL Database och Azure SQL-hanterad instans skapar säkerhets kopior av databasen som behålls under den konfigurerade kvarhållningsperioden. De använder Azure [Read-Access Geo-redundant lagring (RA-GRS)](../../storage/common/storage-redundancy.md) för att säkerställa att säkerhets kopior bevaras även om data centret inte är tillgängligt.

Databas säkerhets kopieringar är en viktig del av en strategi för affärs kontinuitet och haveri beredskap eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Om säkerhets reglerna kräver att dina säkerhets kopior är tillgängliga under en längre tid (upp till 10 år) kan du konfigurera [långsiktig kvarhållning](long-term-retention-overview.md) för både enkla databaser och databaser i pooler.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Vad är en SQL Database säkerhets kopiering?

Både SQL Database-och SQL-hanterad instans använder SQL Server teknik för att skapa [fullständiga säkerhets kopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) varje vecka, [differentiella säkerhets kopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) var 12: e timme och [säkerhets kopieringar av transaktions loggar](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) var 5 till 10 minuter Säkerhets kopiorna lagras i [RA-GRS Storage-blobbar](../../storage/common/storage-redundancy.md) som replikeras till ett [parat Data Center](../../best-practices-availability-paired-regions.md) för skydd mot ett Data Center avbrott. När du återställer en databas fastställer tjänsten vilka säkerhets kopior av fullständig, differentiell och transaktions logg som måste återställas.

Du kan använda de här säkerhetskopiorna för att:

- **Återställ en befintlig databas till en tidpunkt under den senaste** perioden under kvarhållningsperioden med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API. Den här åtgärden skapar en ny databas på samma server i samma prenumeration som den ursprungliga databasen för databaser med enkel och pool. I en hanterad instans kan den här åtgärden Skapa en kopia av databasen eller samma eller en annan hanterad instans under samma prenumeration.
- **Återställ en borttagen databas till tiden för borttagning** eller när som helst inom kvarhållningsperioden. Den borttagna databasen kan bara återställas på samma server eller hanterade instans och i samma prenumeration som den ursprungliga databasen skapades i.
- **Återställ en databas till en annan geografisk region**. Med geo-återställning kan du återställa från en geografisk katastrof när du inte har åtkomst till servern och databasen. Den skapar en ny databas på alla befintliga servrar, var som helst i världen.
- **Återställ en databas från en viss långsiktig säkerhets kopia** på en databas eller databas i pooler, om databasen har kon figurer ATS med en långsiktig bevarande princip (brv). Med LTR kan du återställa en gammal version av databasen med hjälp av [Azure Portal](long-term-backup-retention-configure.md#using-azure-portal) eller [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) för att uppfylla en begäran om efterlevnad eller köra en gammal version av programmet. Mer information finns i avsnittet om [långsiktig kvarhållning](long-term-retention-overview.md).

Information om hur du utför en återställning finns i [återställa databasen från säkerhets kopior](recovery-using-backups.md).

> [!NOTE]
> I Azure Storage syftar termen *replikering* för att kopiera filer från en plats till en annan. I Azure SQL Database-och SQL-hanterad instans syftar *databasreplikering* på att behålla flera sekundära databaser som synkroniseras med en primär databas.

Du kan prova några av de här åtgärderna med hjälp av följande exempel:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Ändra kvarhållning av säkerhets kopior | [Enkel databas](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Hanterad instans](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Enkel databas](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Ändra långsiktig kvarhållning av säkerhets kopior | [Enkel databas](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Hanterad instans-saknas  | [Enkel databas](long-term-backup-retention-configure.md)<br/>Hanterad instans-saknas  |
| Återställa en databas från en tidpunkt | [Enkel databas](recovery-using-backups.md#point-in-time-restore) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Återställa en borttagen databas | [Enkel databas](recovery-using-backups.md) | [Enkel databas](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Återställa en databas från Azure Blob Storage | Enkel databas-ej tillämpligt <br/>Hanterad instans-saknas  | Enkel databas-ej tillämpligt <br/>[Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

SQL Database-och SQL-hanterad instans stöder självbetjäning för återställning av PITR (Point-in-Time-återställning) genom att automatiskt skapa fullständiga säkerhets kopior, differentiella säkerhets kopior och säkerhets kopior av transaktions loggar. Fullständiga säkerhets kopieringar skapas varje vecka och säkerhets kopior av differentiella databaser skapas vanligt vis var 12: e timme. Säkerhets kopior av transaktions loggar skapas vanligt vis var 5 till 10 minuter. Frekvensen för säkerhets kopiering av transaktions loggar baseras på beräknings storlek och mängden databas aktivitet.

Den första fullständiga säkerhets kopieringen schemaläggs direkt efter att en databas har skapats. Den här säkerhets kopian slutförs vanligt vis inom 30 minuter, men det kan ta längre tid när databasen är stor. Den första säkerhets kopieringen kan till exempel ta längre tid på en återställd databas eller en databas kopia. Efter den första fullständiga säkerhetskopian schemaläggs alla ytterligare säkerhetskopieringar obevakat i bakgrunden. Den exakta tiden för alla säkerhets kopieringar av databasen bestäms av SQL Database-eller SQL-hanterad instans eftersom den balanserar den övergripande system belastningen. Du kan inte ändra eller inaktivera säkerhetskopieringsjobben.

### <a name="default-backup-retention-period"></a>Standard bevarande period för säkerhets kopiering

PITR-säkerhetskopieringar skyddas med Geo-redundant lagring. Läs mer i [Redundansalternativ för Azure Storage](../../storage/common/storage-redundancy.md).

Mer information om PITR finns i [punkt-in-Time-återställning](recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Långsiktig kvarhållning

För enskilda databaser och grupperade databaser kan du konfigurera långsiktig kvarhållning (brv) för fullständiga säkerhets kopieringar i upp till 10 år i Azure Blob Storage. Om du aktiverar vä-hö-princip kopieras varje veckovis fullständig säkerhets kopiering automatiskt till en annan RA-GRS lagrings behållare. För att uppfylla olika krav på efterlevnad kan du välja olika kvarhållningsperioden för säkerhets kopiering varje vecka, månatlig och per år. Lagrings användningen beror på den valda frekvensen av säkerhets kopior och kvarhållningsperioden eller perioder. Du kan använda pris listan för [vanlig pris sättning](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för att beräkna kostnaden för LTR Storage.

T. ex. PITR säkerhets kopieringar skyddas inte säkerhets kopiering med Geo-redundant lagring. Läs mer i [Redundansalternativ för Azure Storage](../../storage/common/storage-redundancy.md).

För ytterligare information om LTR, se [långsiktig kvarhållning av säkerhets kopior](long-term-retention-overview.md).

## <a name="backup-storage-consumption"></a>Lagrings förbrukning för säkerhets kopiering

För enskilda databaser och hanterade instanser används den här ekvationen för att beräkna den totala användningen av lagring av säkerhets kopior:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

För databaser i pooler sammanställs den totala lagrings storleken för säkerhets kopior på Poolnivå och beräknas enligt följande:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Säkerhets kopieringar som inträffar innan kvarhållningsperioden rensas automatiskt baserat på deras tidsstämpel. Eftersom differentiella säkerhets kopior och logg säkerhets kopior kräver en tidigare fullständig säkerhets kopiering för att vara användbara, rensas de samman i varje vecka.

SQL Database-och SQL-hanterad instans beräknar din totala lagring av säkerhets kopior som ett ackumulerat värde. Varje timme rapporteras det här värdet till Azures fakturerings pipeline, som är ansvarig för att aggregera denna användning per timme för att beräkna förbrukningen i slutet av varje månad. När databasen har släppts minskar förbrukningen som säkerhets kopierings ålder. När säkerhets kopieringarna blev äldre än kvarhållningsperioden stoppas faktureringen.
   
   > [!IMPORTANT]
   > Säkerhets kopior av en databas bevaras för den angivna kvarhållningsperioden, även om databasen har släppts. När du släpper och återskapar en databas ofta kan spara pengar på lagrings-och beräknings kostnader, kan det öka kostnaderna för säkerhets kopiering eftersom Microsoft behåller en säkerhets kopia för den angivna kvarhållningsperioden för varje släppt databas varje gång den släpps. 

### <a name="monitor-consumption"></a>Övervaka förbrukning

Varje typ av säkerhets kopiering (fullständig, differentiell och logg) rapporteras i bladet databas övervakning som ett separat mått. Följande diagram visar hur du övervakar användningen av lagrings utrymme för säkerhets kopior för en enskild databas. Den här funktionen är för närvarande inte tillgänglig för hanterade instanser.

![Övervaka användningen av säkerhets kopiering av databasen i Azure Portal](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Finjustera lagrings förbrukning för säkerhets kopiering

Överförbrukning av lagrings utrymme för säkerhets kopiering beror på arbets belastningen och storleken på de enskilda databaserna. Överväg några av följande justerings metoder för att minska användningen av lagrings utrymme för säkerhets kopior:

- Minska [lagrings perioden för säkerhets kopior](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) till det minimum som krävs för dina behov.
- Undvik att utföra stora Skriv åtgärder, till exempel indexera återskapare, oftare än du behöver.
- För stora data inläsnings åtgärder bör du överväga att använda [grupperade columnstore-index](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), minska antalet icke-grupperade index och ta hänsyn till Mass inläsnings åtgärder med rad antal runt 1 000 000.
- I tjänst nivån generell användning är den etablerade data lagringen billigare än priset för den överskjutande lagringen. Om du har ständigt höga säkerhets kopierings kostnader kan du överväga att öka lagrings utrymmet för lagringen.
- Använd TempDB i stället för permanenta tabeller i din ETL-logik för att lagra tillfälliga resultat. (Gäller endast SQL-hanterad instans.)
- Överväg att inaktivera TDE-kryptering för databaser som inte innehåller känsliga data (till exempel utvecklings-eller test databaser). Säkerhets kopieringar för icke-krypterade databaser komprimeras vanligt vis med ett högre komprimerings förhållande.

> [!IMPORTANT]
> För analys data mart \ arbets belastningar för informations lager rekommenderar vi starkt att du använder [grupperade columnstore-index](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), minskar antalet icke-grupperade index och tar med Mass inläsnings åtgärder med rad antal runt 1 000 000 för att minska den outnyttjade lagrings användningen.

## <a name="storage-costs"></a>Lagringskostnader

Priset för lagring varierar beroende på om du använder DTU-modellen eller vCore-modellen.

### <a name="dtu-model"></a>DTU-modell

Det finns ingen ytterligare avgift för lagring av säkerhets kopior för databaser och Elastic Database-pooler om du använder DTU-modellen.

### <a name="vcore-model"></a>vCore-modellen

För enskilda databaser i SQL Database ges ett minsta lagrings utrymme för säkerhets kopior som motsvarar 100 procent av databasens storlek utan extra kostnad. För elastiska pooler i SQL Database och enskilda instanser och instans pooler i SQL-hanterad instans, tillhandahålls ett minsta lagrings utrymme för säkerhets kopiering som motsvarar 100 procent av den allokerade data lagringen för pool-eller instans storleken utan extra kostnad. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras i GB/månad. Den här ytterligare förbrukningen är beroende av arbets belastningen och storleken på de enskilda databaserna.

SQL Database och SQL-hanterad instans beräknar din totala lagring av säkerhets kopior som ett ackumulerat värde. Varje timme rapporteras det här värdet till Azures fakturerings pipeline, som är ansvarig för att aggregera denna användning per timme för att få din förbrukning i slutet av varje månad. När databasen har släppts sänker Microsoft förbrukningen som säkerhets kopierings ålder. När säkerhets kopieringarna blev äldre än kvarhållningsperioden stoppas faktureringen. Eftersom alla säkerhets kopior av loggar och differentiella säkerhets kopior bevaras för den fullständiga kvarhållningsperioden, kommer stora ändrade databaser att ha högre kostnader för säkerhets kopiering.

Anta att en databas har samlat 744 GB lagrings utrymme för säkerhets kopior och att denna mängd förblir konstant under hela månaden. Om du vill konvertera den här kumulativa lagrings användningen till per timme delar du den med 744,0 (31 dagar per månad * 24 timmar per dag). Därför rapporterar SQL Database att databasen förbrukade 1 GB av PITR-säkerhetskopiering varje timme. Azure-faktureringen sammanställer den här förbrukningen och visar en användning på 744 GB för hela månaden. Kostnaden baseras på priset för $/GB/month i din region.

Nu är ett mer avancerat exempel. Anta att databasen har kvarhållning ökat till 14 dagar i mitten av månaden. Anta den här ökningen (hypotetiskt) resulterar i den totala lagringen av säkerhets kopior som är dubblerad till 1 488 GB. SQL Database skulle rapportera 1 GB användning i timmar 1 till 372. Den rapporterar användningen som 2 GB för timmar 373 till 744. Den här användningen sammanställs till en sista faktura på 1 116 GB/månad.

### <a name="monitor-costs"></a>Övervaka kostnader

Om du vill förstå kostnader för säkerhets kopierings lagring går du till **Cost Management + fakturering** i Azure Portal, väljer **Cost Management**och väljer sedan **kostnads analys**. Välj önskad prenumeration som **omfång**och filtrera sedan efter den tids period och tjänst som du är intresse rad av.

Lägg till ett filter för **tjänst namn**och välj sedan **SQL-databas** i list rutan. Använd filtret under **kategori för mätning** för att välja fakturerings räknare för din tjänst. För en enskild databas eller en Elastic Database-pool väljer du **Single/elastisk pool pitr backup Storage**. För en hanterad instans väljer du **mi pitr backup Storage**. Under kategorierna för **lagring** och **beräkning** kan intresserar dig också, men de är inte kopplade till reserv lagrings kostnader.

![Kostnads analys för lagring av säkerhets kopior](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Kvarhållning av säkerhetskopior

Alla databaser i SQL Database-och SQL-hanterade instanser har en standard kvarhållningsperiod för säkerhets kopiering av 7 dagar. Du kan [ändra kvarhållningsperioden för säkerhets kopior](#change-the-pitr-backup-retention-period) till så länge 35 dagar.

Om du tar bort en databas behåller Azure säkerhets kopiorna på samma sätt som en online-databas. Om du till exempel tar bort en Basic-databas som har en kvarhållningsperiod på sju dagar, sparas en säkerhets kopia som är fyra dagar gammal i tre dagar.

Om du behöver behålla säkerhets kopiorna längre än den maximala kvarhållningsperioden kan du ändra egenskaperna för säkerhets kopiering för att lägga till en eller flera långsiktiga kvarhållningsperiod i databasen. Mer information finns i avsnittet om [långsiktig kvarhållning](long-term-retention-overview.md).

> [!IMPORTANT]
> Att ställa in kvarhållningsperioden för säkerhets kopior till 1 dag (eller till ett värde mellan 1-7) stöds bara via PowerShell eller REST API för tillfället. Den minsta nödvändiga versionen av AZ. SQL-modulen är v 2.6.0, eller så kan den köras via CloudShell som alltid har den senaste AZ. SQL-versionen.

> [!IMPORTANT]
> Om du tar bort servern eller den hanterade instansen raderas även alla databaser som hanteras av servern eller den hanterade instansen. De kan inte återställas. Du kan inte återställa en borttagen Server eller hanterad instans. Men om du har konfigurerat långsiktig kvarhållning för SQL Database eller hanterade-instansen tas säkerhets kopiorna för databaserna med LTR inte bort, och dessa databaser kan återställas.

## <a name="encrypted-backups"></a>Krypterade säkerhets kopior

Om databasen är krypterad med TDE krypteras säkerhets kopiorna automatiskt i vila, inklusive LTR-säkerhetskopieringar. När TDE är aktive rad för SQL Database eller SQL-hanterad instans krypteras även säkerhets kopior. Alla nya databaser i SQL Database och SQL-hanterad instans konfigureras med TDE aktiverat som standard. Mer information om TDE finns i [Transparent datakryptering med SQL Database & SQL-hanterad instans](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Säkerhets kopierings integritet

Azure SQL Engineering-teamet kommer kontinuerligt att regelbundet testa återställning av automatiserade databas säkerhets kopior av databaser i SQL Database. (Den här testningen är inte tillgänglig i SQL-hanterad instans.) Vid tidpunkts återställning erhåller-databaser också integritets kontroller för DBCC CHECKDB.

SQL-hanterad instans tar en automatisk första säkerhets kopiering med `CHECKSUM` databaser som återställs med det interna `RESTORE` kommandot eller med Azure Data Migration service när migreringen är klar.

Eventuella problem som hittas under integritets kontrollen resulterar i en avisering till teknik teamet. Mer information finns [i data integritet i SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Efterlevnad

När du migrerar databasen från en DTU-baserad tjänst nivå till en vCore tjänst nivå bevaras PITR kvarhållning för att säkerställa att programmets data återställnings princip inte komprometteras. Om Standardkvarhållning inte uppfyller dina krav kan du ändra PITR-kvarhållningsperioden med hjälp av PowerShell eller REST API. Mer information finns i [ändra lagrings perioden för PITR-säkerhetskopiering](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Ändra kvarhållningsperioden för säkerhets kopiering av PITR

Du kan ändra standard lagrings perioden för PITR-säkerhetskopiering med hjälp av Azure Portal, PowerShell eller REST API. I följande exempel visas hur du ändrar PITR-kvarhållning till 28 dagar.

> [!WARNING]
> Om du minskar den aktuella kvarhållningsperioden är alla befintliga säkerhets kopior som är äldre än den nya kvarhållningsperioden inte längre tillgängliga. Om du ökar den aktuella kvarhållningsperioden behåller Azure de befintliga säkerhets kopiorna tills slutet av den längre kvarhållningsperioden nås.

> [!NOTE]
> Dessa API: er påverkar endast PITR bevarande period. Om du har konfigurerat LTR för databasen påverkas den inte. Information om hur du ändrar kvarhållning av LTR-perioder finns i [långsiktig kvarhållning](long-term-retention-overview.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Ändra lagrings perioden för PITR-säkerhetskopiering med hjälp av Azure Portal

Om du vill ändra kvarhållningsperioden för säkerhets kopior av PITR med hjälp av Azure Portal går du till Server-objektet vars kvarhållningsperiod du vill ändra i portalen. Välj sedan lämpligt alternativ baserat på det Server objekt som du ändrar.

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

Ändringar av PITR-kvarhållning av säkerhets kopior för SQL Database görs på server nivå. Ändringar som görs på server nivå gäller för databaser på servern. Om du vill ändra PITR kvarhållning för en server från Azure Portal går du till Server översikts bladet. Välj **hantera säkerhets kopior** i den vänstra rutan och välj sedan **Konfigurera kvarhållning** överst på skärmen:

![Ändra PITR-kvarhållning, server nivå](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL-hanterad instans](#tab/managed-instance)

Ändringar av PITR för säkerhets kopiering för SQL Managed instance görs på en enskild databas nivå. Om du vill ändra PITR för säkerhets kopiering för en instans databas från Azure Portal går du till bladet individuell databas översikt. Välj sedan **Konfigurera kvarhållning av säkerhets kopior** överst på skärmen:

![Ändra PITR kvarhållning, hanterad instans](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Ändra kvarhållningsperioden för säkerhets kopiering av PITR med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM-modulen stöds fortfarande av SQL Database-och SQL-hanterad instans, men all framtida utveckling är för AZ. SQL-modulen. Mer information finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen är i stort sett identiska med de i AzureRm-modulerna.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Ändra lagrings perioden för PITR-säkerhetskopiering med hjälp av REST API

#### <a name="sample-request"></a>Exempel förfrågan

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

Mer information finns i [kvarhållning av säkerhets kopior REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Nästa steg

- Databas säkerhets kopieringar är en viktig del av en strategi för affärs kontinuitet och haveri beredskap eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Mer information om andra SQL Database affärs kontinuitets lösningar finns i [Översikt över affärs kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av Azure Portal](recovery-using-backups.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av PowerShell](scripts/restore-database-powershell.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av Azure Portal finns i [Hantera långsiktig kvarhållning av säkerhets kopior med hjälp av Azure Portal](long-term-backup-retention-configure.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiserade säkerhets kopieringar i Azure Blob Storage med hjälp av PowerShell finns i [Hantera långsiktig kvarhållning av säkerhets kopior med hjälp av PowerShell](long-term-backup-retention-configure.md).
