---
title: Automatiska, geouppsagade säkerhetskopior
description: SQL Database skapar automatiskt en lokal databassäkerhetskopiering med några minuters mellanrum och använder Azure-geo-redundant lagring med läsåtkomst för georedundans.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061767"
---
# <a name="automated-backups"></a>Automatiserade säkerhetskopieringar

Azure SQL Database skapar automatiskt de säkerhetskopieringar av databasen som sparas under den konfigurerade kvarhållningsperioden. Den använder Azure [read-access geo-redundant lagring (RA-GRS)](../storage/common/storage-redundancy.md) för att säkerställa att säkerhetskopiorna bevaras även om datacentret inte är tillgängligt.

Säkerhetskopiering av databaser är en viktig del av alla affärskontinuiteter och katastrofåterställningsstrategier eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Om dina säkerhetsregler kräver att dina säkerhetskopior är tillgängliga under en längre tid (upp till 10 år) kan du konfigurera långsiktig kvarhållning på [singleton-databaser](sql-database-long-term-retention.md) och elastiska databaspooler.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>Vad är en säkerhetskopia av SQL Database?

SQL Database använder SQL Server-teknik för att skapa [fullständiga säkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) varje vecka, [differentiella säkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) var 12:e timme och [säkerhetskopiering av transaktionsloggar](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) var 5:e till 10:e minut. Säkerhetskopiorna lagras i [RA-GRS-lagringsblobar](../storage/common/storage-redundancy.md) som replikeras till ett [parat datacenter](../best-practices-availability-paired-regions.md) för skydd mot ett datacenterutbrott. När du återställer en databas avgör tjänsten vilka fullständiga säkerhetskopieringar, differentiella och transaktionsloggar som behöver återställas.

Du kan använda de här säkerhetskopiorna för att:

- **Återställa en befintlig databas till en tidpunkt tidigare** under kvarhållningsperioden med hjälp av Azure-portalen, Azure PowerShell, Azure CLI eller REST API. För enskilda databaser och elastiska databaspooler skapar den här åtgärden en ny databas på samma server som den ursprungliga databasen. I hanterad instans kan den här åtgärden skapa en kopia av databasen eller samma eller en annan hanterad instans under samma prenumeration.
- **Återställa en borttagen databas till tidpunkten för borttagningen** eller när som helst inom kvarhållningsperioden. Den borttagna databasen kan bara återställas på samma logiska server eller hanterade instans där den ursprungliga databasen skapades.
- **Återställa en databas till ett annat geografiskt område**. Med geoåterställning kan du återställa från en geografisk katastrof när du inte kan komma åt servern och databasen. Det skapar en ny databas på en befintlig server, var som helst i världen.
- **Återställa en databas från en specifik långsiktig säkerhetskopiering** på en enda databas eller elastisk databaspool, om databasen är konfigurerad med en långsiktig bevarandeprincip (LTR). Med LTR kan du återställa en gammal version av databasen med hjälp av [Azure-portalen](sql-database-long-term-backup-retention-configure.md#using-azure-portal) eller [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) för att uppfylla en efterlevnadsbegäran eller för att köra en gammal version av programmet. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

Information om hur du återställer finns i [Återställa databas från säkerhetskopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure Storage avser termen *replikering* att kopiera filer från en plats till en annan. SQL *Server-databasreplikering* avser att hålla flera sekundära databaser synkroniserade med en primär databas.

Du kan prova några av dessa åtgärder med hjälp av följande exempel:

| | Azure Portal | Azure PowerShell |
|---|---|---|
| Ändra kvarhållning av säkerhetskopiering | [Enskild databas](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Hanterad instans](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Enskild databas](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Ändra långsiktig lagring av säkerhetskopiering | [Enskild databas](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Hanterad instans - Saknas  | [Enskild databas](sql-database-long-term-backup-retention-configure.md)<br/>Hanterad instans - Saknas  |
| Återställa en databas från en tidpunkt | [Enskild databas](sql-database-recovery-using-backups.md#point-in-time-restore) | [Enskild databas](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Återställa en borttagen databas | [Enskild databas](sql-database-recovery-using-backups.md) | [Enskild databas](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Hanterad instans](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Återställa en databas från Azure Blob-lagring | Enkel databas - Ej vilken databas <br/>Hanterad instans - Saknas  | Enkel databas - Ej vilken databas <br/>[Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Säkerhetskopieringsfrekvens

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

SQL Database stöder självbetjäning för point-in-time restore (PITR) genom att automatiskt skapa fullständiga säkerhetskopior, differentiella säkerhetskopior och säkerhetskopiering av transaktionsloggar. Fullständiga säkerhetskopieringar av databaser skapas varje vecka och differentiella databassäkerhetskopior skapas i allmänhet var 12:e timme. Säkerhetskopior av transaktionsloggen skapas i allmänhet var 5:e till 10:e minut. Frekvensen för säkerhetskopior av transaktionsloggen baseras på beräkningsstorleken och mängden databasaktivitet. 

Den första fullständiga säkerhetskopian schemaläggs omedelbart efter att en databas har skapats. Den här säkerhetskopian slutförs vanligtvis inom 30 minuter, men det kan ta längre tid när databasen är stor. Den första säkerhetskopian kan till exempel ta längre tid på en återställd databas eller en databaskopia. Efter den första fullständiga säkerhetskopian schemaläggs alla ytterligare säkerhetskopieringar obevakat i bakgrunden. Den exakta tiden för alla databassäkerhetskopieringar fastställs av SQL Database-tjänsten eftersom den balanserar den övergripande systemarbetsbelastningen. Du kan inte ändra eller inaktivera säkerhetskopieringsjobben.

PITR-säkerhetskopior skyddas med geouppsagbar lagring. Läs mer i [Redundansalternativ för Azure Storage](../storage/common/storage-redundancy.md).

Mer information om PITR finns [i Point-in-time restore](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Långsiktig kvarhållning

För enstaka och poolade databaser kan du konfigurera långsiktig kvarhållning (LTR) för fullständiga säkerhetskopior i upp till 10 år i Azure Blob-lagring. Om du aktiverar LTR-principen kopieras de veckovisa fullständiga säkerhetskopieringarna automatiskt till en annan RA-GRS-lagringsbehållare. För att uppfylla olika efterlevnadskrav kan du välja olika kvarhållningsperioder för veckovisa, månatliga och/eller årliga säkerhetskopior. Lagringsförbrukningen beror på den valda frekvensen av säkerhetskopior och kvarhållningsperioden eller kvarhållningsperioderna. Du kan använda [LTR-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/?service=sql-database) för att uppskatta kostnaden för LTR-lagring.

Precis som PITR-säkerhetskopior skyddas LTR-säkerhetskopior med geouppsagd lagring. Läs mer i [Redundansalternativ för Azure Storage](../storage/common/storage-redundancy.md).

Mer information om LTR finns i [Lagring av säkerhetskopiering på lång sikt](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Förbrukning för lagring för säkerhetskopiering

För enskilda databaser används den här ekvationen för att beräkna den totala användningen av säkerhetskopieringslagring:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

För elastiska databaspooler aggregeras den totala lagringsstorleken för säkerhetskopiering på poolnivå och beräknas på följande sätt:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Säkerhetskopior som inträffar före kvarhållningsperioden rensas automatiskt baserat på deras tidsstämpel. Eftersom differentiella säkerhetskopior och loggsäkerhetskopior kräver en tidigare fullständig säkerhetskopiering för att vara användbar, rensas de tillsammans i veckosegment.

Azure SQL Database beräknar din totala lagring av säkerhetskopiering i kvarhållning som ett kumulativt värde. Varje timme rapporteras det här värdet till Azure-faktureringspipelinen, som ansvarar för att samla den här timanvändningen för att beräkna din förbrukning i slutet av varje månad. När databasen har tagits bort minskar förbrukningen när säkerhetskopiorna åldras. När säkerhetskopiorna har blivit äldre än kvarhållningsperioden stoppas faktureringen.

   > [!IMPORTANT]
   > Säkerhetskopior av en databas behålls under den angivna kvarhållningsperioden, även om databasen har tagits bort. Även om släppa och återskapa en databas kan ofta spara på lagring och beräkningskostnader, kan det öka kostnaderna för säkerhetskopiering lagring eftersom Microsoft behåller en säkerhetskopia för den angivna lagringsperioden (som är minst 7 dagar) för varje bortsläppt databas, varje när den släpps.

### <a name="monitor-consumption"></a>Övervaka förbrukning

Varje typ av säkerhetskopiering (fullständig, differentiell och log) rapporteras på databasövervakningsbladet som ett separat mått. Följande diagram visar hur du övervakar förbrukningen för säkerhetskopieringslagring för en enskild databas. Den här funktionen är för närvarande inte tillgänglig för hanterade instanser.

![Övervaka förbrukning av säkerhetskopiering av databaser i Azure-portalen](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Finjustera lagringsförbrukning för säkerhetskopiering

Överskjutande lagringsförbrukning för säkerhetskopiering beror på arbetsbelastningen och storleken på de enskilda databaserna. Tänk på några av följande justeringstekniker för att minska förbrukningen för säkerhetskopieringslagring:

* Minska [lagringsperioden för säkerhetskopiering](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) till ett minimum för dina behov.
* Undvik att göra stora skrivåtgärder, som ombyggnader av index, oftare än du behöver.
* För stora datainläsningsåtgärder bör du överväga att använda [klustrade columnstore-index,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)minska antalet icke-klustrade index och överväga massbelastningsåtgärder med radantal runt 1 miljon.
* På den allmänna tjänstnivån är den etablerade datalagringen billigare än priset för den extra säkerhetskopieringslagringen. Om du har kontinuerligt höga kostnader för extra lagring av säkerhetskopiering kan du överväga att öka datalagringen för att spara på lagringen för säkerhetskopiering.
* Använd TempDB i stället för permanenta tabeller i ETL-logiken för att lagra tillfälliga resultat. (Gäller endast hanterad instans.)
* Överväg att stänga av TDE-kryptering för databaser som inte innehåller känsliga data (till exempel utvecklings- eller testdatabaser). Säkerhetskopior för icke-krypterade databaser komprimeras vanligtvis med ett högre komprimeringsförhållande.

> [!IMPORTANT]
> För analysdata mart \ datalagerarbetsbelastningar rekommenderar vi starkt att du använder [klustrade columnstore-index,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)minskar antalet icke-klustrade index och överväger massinläsningsåtgärder med radantal runt 1 miljon för att minska förbrukningen för extra säkerhetskopieringslagring.

## <a name="storage-costs"></a>Lagringskostnader

Priset för lagring varierar beroende på om du använder DTU-modellen eller vCore-modellen.

### <a name="dtu-model"></a>DTU-modell

Det finns ingen extra kostnad för säkerhetskopieringslagring för databaser och elastiska databaspooler om du använder DTU-modellen.

### <a name="vcore-model"></a>vCore-modellen

För enskilda databaser anges ett minsta lagringsbelopp för säkerhetskopiering som motsvarar 100 procent av databasstorleken utan extra kostnad. För elastiska databaspooler och hanterade instanser tillhandahålls ett minsta belopp för säkerhetskopieringslagring som motsvarar 100 procent av den allokerade datalagringen för poolen respektive instansstorleken utan extra kostnad. Ytterligare förbrukning av lagringsenhet för säkerhetskopior debiteras i GB/månad. Den här ytterligare förbrukningen beror på arbetsbelastningen och storleken på de enskilda databaserna.

Azure SQL Database beräknar din totala lagring av säkerhetskopiering i kvarhållning som ett kumulativt värde. Varje timme rapporteras det här värdet till Azure-faktureringspipelinen, som ansvarar för att samla den här timanvändningen för att få din förbrukning i slutet av varje månad. När databasen har tagits bort minskar Microsoft förbrukningen när säkerhetskopiorna åldras. När säkerhetskopiorna har blivit äldre än kvarhållningsperioden stoppas faktureringen. Eftersom alla loggsäkerhetskopior och differentiella säkerhetskopior behålls under hela kvarhållningsperioden, har kraftigt ändrade databaser högre säkerhetskopieringsavgifter.

Anta att en databas har samlat 744 GB lagringsutrymme för säkerhetskopiering och att det här beloppet förblir konstant under en hel månad. Om du vill konvertera den här kumulativa lagringsförbrukningen till timanvändning dividerar du den med 744,0 (31 dagar per månad * 24 timmar per dag). Så SQL Database rapporterar att databasen förbrukas 1 GB PITR-säkerhetskopiering varje timme. Azure-fakturering aggregerade den här förbrukningen och visar en användning på 744 GB för hela månaden. Kostnaden baseras på $/GB/månadspriset i din region.

Nu, ett mer komplext exempel. Anta att databasen har ökat kvarhållningen till 14 dagar i mitten av månaden. Anta att den här ökningen (hypotetiskt) resulterar i att den totala säkerhetskopieringslagringen fördubblas till 1 488 GB. SQL Database skulle rapportera 1 GB användning i timmar 1 till 372. Det skulle rapportera användningen som 2 GB för timmar 373 till 744. Den här användningen skulle aggregeras till en slutfaktura på 1 116 GB/månad.

### <a name="monitor-costs"></a>Övervaka kostnader

Om du vill förstå lagringskostnader för säkerhetskopiering går du till **Kostnadshantering + Fakturering** i Azure-portalen, väljer **Kostnadshantering**och väljer sedan **Kostnadsanalys**. Välj önskad prenumeration som **Scope**och filtrera sedan för den tidsperiod och tjänst som du är intresserad av.

Lägg till ett filter för **tjänstnamn**och välj sedan **SQL-databasen** i listrutan. Använd **underkategorifiltret för mätarkategori** för att välja faktureringsräknaren för din tjänst. För en enskild databas eller en elastisk databaspool väljer du **säkerhetskopiering av en/elastisk poolde pitr.** Välj lagring av **säkerhetskopiering av mi pitr**för en hanterad instans . Underkategorierna **Lagring** och **beräkning** kan också intressera dig, men de är inte associerade med lagringskostnader för säkerhetskopiering.

![Kostnadsanalys för säkerhetskopiering av lagring](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Kvarhållning av säkerhetskopior

Alla Azure SQL-databaser (enskilda, poolade och hanterade instansdatabaser) har en standardlagringsperiod för säkerhetskopiering på 7 dagar. Du kan [ändra lagringsperioden för säkerhetskopiering](#change-the-pitr-backup-retention-period) till så länge som 35 dagar.

Om du tar bort en databas behåller SQL Database säkerhetskopiorna på samma sätt som för en onlinedatabas. Om du till exempel tar bort en Basic-databas som har en kvarhållningsperiod på sju dagar sparas en säkerhetskopia som är fyra dagar gammal i ytterligare tre dagar.

Om du behöver behålla säkerhetskopiorna längre än den maximala kvarhållningsperioden kan du ändra egenskaperna för säkerhetskopiering för att lägga till en eller flera långsiktiga kvarhållningsperioder i databasen. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Om du tar bort Azure SQL-servern som är värd för SQL-databaser tas även alla elastiska databaspooler och databaser som tillhör servern bort. De kan inte återvinnas. Du kan inte återställa en borttagen server. Men om du har konfigurerat långsiktig kvarhållning tas inte säkerhetskopior för databaserna med LTR bort och dessa databaser kan återställas.

## <a name="encrypted-backups"></a>Krypterade säkerhetskopior

Om databasen är krypterad med TDE krypteras säkerhetskopior automatiskt i vila, inklusive LTR-säkerhetskopior. När TDE är aktiverat för en Azure SQL-databas krypteras även säkerhetskopior. Alla nya Azure SQL-databaser är konfigurerade med TDE aktiverat som standard. Mer information om TDE finns i [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integritet för säkerhetskopiering

Kontinuerligt testar Azure SQL Database engineering-teamet automatiskt återställning av automatiska databassäkerhetskopior av databaser som placeras i logiska servrar och elastiska databaspooler. (Den här testningen är inte tillgänglig i hanterade instans.) Vid återställning i tid får databaser också DBCC CHECKDB-integritetskontroller.

Hanterad instans tar `CHECKSUM` automatisk första säkerhetskopiering med `RESTORE` databaser som återställts med det inbyggda kommandot eller med Azure Data Migration Service när migreringen har slutförts.

Eventuella problem som hittas under integritetskontrollen resulterar i en avisering till ingenjörsteamet. Mer information finns [i Dataintegritet i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Efterlevnad

När du migrerar databasen från en DTU-baserad tjänstnivå till en vCore-baserad tjänstnivå bevaras PITR-lagringen för att säkerställa att programmets dataåterställningsprincip inte äventyras. Om standardlagringen inte uppfyller dina efterlevnadskrav kan du ändra PITR-kvarhållningsperioden med hjälp av PowerShell eller REST API. Mer information finns i [Ändra lagringsperioden för PITR-säkerhetskopiering](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Ändra lagringsperioden för PITR-säkerhetskopiering

Du kan ändra standardlagringsperioden för PITR-säkerhetskopiering med hjälp av Azure-portalen, PowerShell eller REST API.You can change the default PITR backup retention period by using the Azure portal, PowerShell, or the REST API. Följande exempel illustrerar hur du ändrar PITR-kvarhållningen till 28 dagar.

> [!WARNING]
> Om du minskar den aktuella kvarhållningsperioden är alla befintliga säkerhetskopior som är äldre än den nya kvarhållningsperioden inte längre tillgängliga. Om du ökar den aktuella kvarhållningsperioden behåller SQL Database de befintliga säkerhetskopiorna tills slutet av den längre kvarhållningsperioden har uppnåtts.

> [!NOTE]
> Dessa API:er påverkar endast PITR-kvarhållningsperioden. Om du har konfigurerat LTR för databasen påverkas den inte. Information om hur du ändrar kvarhållningsperioder för ltr finns i [Långtidslagring](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Ändra lagringsperioden för PITR-säkerhetskopiering med hjälp av Azure-portalen

Om du vill ändra pitr-lagringsperioden för säkerhetskopiering med hjälp av Azure-portalen går du till serverobjektet vars kvarhållningsperiod du vill ändra i portalen. Välj sedan lämpligt alternativ baserat på det serverobjekt som du ändrar.

#### <a name="single-database-and-elastic-database-pools"></a>[Endatabas- och elastiska databaspooler](#tab/single-database)

Ändringar i PITR-lagringsplats för azure SQL-databaser görs på servernivå. Ändringar som görs på servernivå gäller för databaser på servern. Om du vill ändra PITR-kvarhållning för en Azure SQL Database-server från Azure-portalen går du till serveröversiktsbladet. Välj **Hantera säkerhetskopior** i den vänstra rutan och välj sedan **Konfigurera kvarhållning** högst upp på skärmen:

![Ändra PITR-kvarhållning, servernivå](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Hanterad instans](#tab/managed-instance)

Ändringar i PITR-lagringsplats för HANTERADE SQL-databasinstanser görs på en enskild databasnivå. Om du vill ändra PITR-lagring av säkerhetskopiering för en instansdatabas från Azure-portalen går du till det enskilda databasöversiktsbladet. Välj sedan **Konfigurera kvarhållning av säkerhetskopiering** högst upp på skärmen:

![Ändra PITR-lagring, hanterad instans](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Ändra lagringsperioden för PITR-säkerhetskopiering med powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. Mer information finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen är i stort sett identiska med dem i AzureRm-modulerna.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Ändra kvarhållningsperioden för PITR-säkerhetskopiering med hjälp av REST API

#### <a name="sample-request"></a>Exempel på begäran

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

Mer information finns i [REST API för rest för bevarande av säkerhetskopiering](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Nästa steg

- Säkerhetskopiering av databaser är en viktig del av alla affärskontinuiteter och katastrofåterställningsstrategier eftersom de skyddar dina data från oavsiktlig skada eller borttagning. Mer information om de andra lösningarna för affärskontinuitet i Azure SQL Database finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av Azure-portalen](sql-database-recovery-using-backups.md).
- Få mer information om hur du [återställer en databas till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiska säkerhetskopior i Azure Blob-lagring med hjälp av Azure-portalen finns i [Hantera långsiktig lagring av säkerhetskopiering med hjälp av Azure-portalen](sql-database-long-term-backup-retention-configure.md).
- Information om hur du konfigurerar, hanterar och återställer från långsiktig kvarhållning av automatiska säkerhetskopior i Azure Blob-lagring med hjälp av PowerShell finns i [Hantera långsiktig lagring av säkerhetskopiering med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).
