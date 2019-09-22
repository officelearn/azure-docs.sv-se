---
title: Återställa en Azure SQL-databas från en säkerhets kopia | Microsoft Docs
description: Lär dig mer om återställning vid tidpunkter, som gör att du kan återställa en Azure SQL Database till en tidigare tidpunkt (upp till 35 dagar).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: ab0a622dcb72072621e6696d423a1d4d2917bedc
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178376"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Återställa en Azure SQL-databas med hjälp av automatisk säkerhets kopiering av databasen

Som standard lagras SQL Database säkerhets kopior i Geo-replikerad Blob Storage (RA-GRS). Följande alternativ är tillgängliga för databas återställning med [Automatisk säkerhets kopiering av databaser](sql-database-automated-backups.md):

- Skapa en ny databas på samma SQL Database servern som återställts till en angiven tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma SQL Database Server som återställts till borttagnings tiden för en borttagen databas.
- Skapa en ny databas på alla SQL Database-servrar i samma region som återställs till de senaste säkerhets kopiorna.
- Skapa en ny databas på alla SQL Database-servrar i andra regioner som återställs till platsen för de senaste replikerade säkerhets kopiorna.

Om du har konfigurerat [långsiktig kvarhållning av säkerhets kopia](sql-database-long-term-retention.md)kan du också skapa en ny databas från valfri säkerhets kopia på valfri SQL Database Server.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställningen.

När du använder standard-eller premium-tjänst nivån innebär en återställd databas en extra lagrings kostnad under följande omständigheter:

- Återställning av p11 – p15 till S4-S12 eller P1 – P6 om max storleken för databasen är större än 500 GB.
- Återställning av P1 – P6 till S4-S12 om den maximala databas storleken är större än 250 GB.

Den extra kostnaden uppstår när den återställda databasens Max storlek är större än den mängd lagrings utrymme som ingår i mål databasens tjänste nivå och prestanda nivå. Extra lagrings utrymme som tillhandahålls utöver den mängd som ingår debiteras extra. Pris information för extra lagrings utrymme finns på [sidan SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden använt utrymme är mindre än den mängd lagring som ingår, kan du undvika den här extra kostnaden genom att ange den maximala databas storleken till den inkluderade mängden.

> [!NOTE]
> [Automatiserade databas säkerhets kopieringar](sql-database-automated-backups.md) används när du skapar en [databas kopia](sql-database-copy.md).

## <a name="recovery-time"></a>Återställnings tid

Återställnings tiden för att återställa en databas med hjälp av automatisk säkerhets kopiering av databasen påverkas av flera faktorer:

- Databasens storlek
- Databasens beräknings storlek
- Antalet transaktions loggar som ingår
- Den mängd aktivitet som måste spelas upp för att återställas till återställnings punkten
- Nätverks bandbredden om återställningen är till en annan region
- Antalet samtidiga återställnings begär Anden som bearbetas i mål regionen

För en stor och/eller mycket aktiv databas kan återställningen ta flera timmar. Om det finns långvariga avbrott i en region, är det möjligt att det finns ett stort antal geo-återställnings begär Anden som bearbetas av andra regioner. När det finns många begär Anden kan återställnings tiden öka för databaser i den regionen. De flesta databas återställningar har slutförts på mindre än 12 timmar.

För en enskild prenumeration finns det begränsningar för antalet samtidiga återställnings begär Anden.  De här begränsningarna gäller för alla kombinationer av tidpunkter som återställs, geo återställer och återställer från säkerhets kopia av långsiktig kvarhållning):

| | **Max antal samtidiga begär Anden som bearbetas** | **Max antal samtidiga förfrågningar som skickas** |
| :--- | --: | --: |
|Enkel databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns för närvarande ingen inbyggd metod för att återställa hela servern. [Azure SQL Database: Fullständig Server återställnings](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript är ett exempel på hur du kan utföra den här uppgiften.

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhets kopieringar måste du vara medlem i rollen SQL Server deltagare i prenumerationen eller vara prenumerations [ägare – se RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST API. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en fristående, poolad eller instans databas till en tidigare tidpunkt med hjälp av Azure Portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases). Begäran kan ange valfri tjänst nivå eller beräknings storlek för den återställda databasen. Se till att du har tillräckligt med resurser på den server som du återställer databasen till. När du är klar skapas en ny databas på samma server som den ursprungliga databasen. Den återställda databasen kommer att debiteras enligt normala priser baserat på tjänst nivå och beräknings storlek. Du debiteras inte förrän databas återställningen är klar.

Du återställer vanligt vis en databas till en tidigare tidpunkt för återställnings syfte. Du kan behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den som data källa för att uppdatera den ursprungliga databasen.

- **Databas utbyte**

  Om den återställda databasen är avsedd att ersätta den ursprungliga databasen, bör du ange den ursprungliga databasens beräknings storlek och tjänst nivå. Du kan sedan byta namn på den ursprungliga databasen och ge den återställda databasen det ursprungliga namnet med kommandot [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Data återställning**

  Om du planerar att hämta data från den återställda databasen för att återställa från ett användar-eller program fel måste du skriva och köra ett data återställnings skript som extraherar data från den återställda databasen och gäller för den ursprungliga databasen. Även om återställnings åtgärden kan ta lång tid att slutföra, visas återställnings databasen i databas listan under hela återställnings processen. Om du tar bort databasen under återställningen avbryts återställnings åtgärden och du debiteras inte för den databas som inte slutförde återställningen.

Om du vill återställa en enskild, fristående eller instans databas till en tidpunkt med hjälp av Azure Portal öppnar du sidan för din databas och klickar på **Återställ** i verktygsfältet.

![återställning av tidpunkt](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> För att program mässigt återställa en databas från en säkerhets kopia, se [program mässigt utföra återställning med automatiska säkerhets kopieringar](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Återställning av borttagen databas

Du kan återställa en borttagen databas till borttagnings tiden eller en tidigare tidpunkt på samma SQL Database Server med Azure Portal, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)eller [rest (CreateMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Du kan [återställa den borttagna databasen på den hanterade instansen med PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> Ett exempel på PowerShell-skript som visar hur du återställer en borttagen databas finns i [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Om du tar bort en Azure SQL Database-serverinstans, tas alla dess databaser också bort och kan inte återställas. Det finns för närvarande inget stöd för att återställa en borttagen server.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Återställningen av databasen har tagits bort med hjälp av Azure Portal

Om du vill återställa en borttagen databas med hjälp av Azure Portal öppnar du sidan för servern och klickar sedan på **borttagna databaser**i avsnittet åtgärder.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> För att program mässigt återställa en borttagen databas, se [program mässigt utföra återställning med automatiska säkerhets kopieringar](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Geo-återställning

Du kan återställa en SQL-databas på valfri server i valfri Azure-region från de senaste geo-replikerade säkerhets kopiorna. Geo-återställning använder en geo-replikerad säkerhets kopiering som källa. Det kan begäras även om databasen eller data centret inte kan nås på grund av ett avbrott.

Geo-återställning är standard alternativet för återställning när databasen inte är tillgänglig på grund av en incident i värd regionen. Du kan återställa databasen till en server i en annan region. Det uppstår en fördröjning mellan när en säkerhets kopia tas och när den är geo-replikerad till en Azure-blob i en annan region. Det innebär att den återställda databasen kan vara upp till en timme bakom den ursprungliga databasen. Följande bild visar återställningen av databasen från den senaste tillgängliga säkerhets kopian i en annan region.

![Geo-återställning](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-using-azure-portal"></a>Geo-återställning med Azure Portal

Allmänt begrepp för geo-återställning av en databas från Azure Portal utförs genom att skapa en ny databas för enkel eller hanterad instans och välja en tillgänglig geo-Restore-säkerhetskopiering på databasens skapa-skärm. Den nyligen skapade databasen kommer att innehålla de geo-återställda säkerhets kopierings data.

#### <a name="single-azure-sql-database"></a>Enstaka Azure SQL Database

Följ dessa steg om du vill använda geo-återställning för enskilda Azure SQL Database från Azure Portal i den region och server du väljer:

1. Klicka på Lägg till **+ Lägg** till i Marketplace och välj **skapa SQL Database**, Fyll i informationen som krävs under **fliken grunder**
2. Välj fliken **ytterligare inställningar**
3. Under Använd befintliga data klickar du på **säkerhets kopiering**
4. Välj en säkerhets kopia i list rutan med tillgängliga säkerhets kopior för geo-återställning

![Geo-Återställ enstaka Azure SQL Database](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Slutför processen med att skapa en ny databas. När den enskilda Azure SQL Database skapas kommer den att innehålla en återställd säkerhets kopia för geo-återställning.

#### <a name="managed-instance-database"></a>Hanterad instans databas

Om du vill använda geo-återställning av hanterade instans databaser från Azure Portal till en befintlig hanterad instans i valfri region väljer du en hanterad instans som du vill att en databas ska återställas på och följer de här stegen:

1. Klicka på **+ ny databas**
2. Ange önskat databas namn
3. Under Använd befintliga data Välj alternativ **säkerhets kopiering**
4. Välj en säkerhets kopia i list rutan med tillgängliga säkerhets kopior för geo-återställning

![Geo-Återställ hanterad instans databas](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Slutför processen med att skapa en ny databas. När instans databasen har skapats kommer den att innehålla en återställd säkerhets kopia för geo-återställning.

### <a name="geo-restore-using-powershell"></a>Geo-återställning med PowerShell

#### <a name="single-azure-sql-database"></a>Enstaka Azure SQL Database

För PowerShell-skript som visar hur du utför geo-återställning för en enda Azure SQL Database, se [Använd PowerShell för att återställa en enkel Azure SQL-databas till en tidigare tidpunkt](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Hanterad instans databas

Ett PowerShell-skript som visar hur du utför geo-återställning för en hanterad instans databas finns i [använda PowerShell för att återställa en hanterad instans databas till en annan geo-region](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Överväganden för geo-återställning

Det finns för närvarande inte stöd för återställning av tidpunkt på en geo-Secondary. Återställning på plats-till-tid kan bara göras på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns i [återställa från ett avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Geo-återställning är den mest grundläggande katastrof återställnings lösningen som finns i SQL Database. Den förlitar sig på automatiskt skapade geo-replikerade säkerhets kopieringar med återställa = 1 timme och den uppskattade återställnings tiden på upp till 12 timmar. Det garanterar inte att mål regionen har kapaciteten att återställa databaserna efter en regional ourage, eftersom en kraftig ökning av behovet kommer att vara sannolik. För icke affärs kritiskt program som använder relativt små databaser är geo-återställning en lämplig lösning för haveri beredskap. För affärs kritiska program som använder stora databaser och måste garantera affärs kontinuitet bör du använda [grupper för automatisk redundans](sql-database-auto-failover-group.md). Det ger en mycket lägre återställnings-och RTO, och kapaciteten är alltid garanterad. Mer information om val av affärs kontinuitet finns i [Översikt över affärs kontinuitet](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Utföra återställning via programmering med automatiska säkerhets kopieringar

Som tidigare diskuterats kan databas återställningen, förutom Azure Portal, utföras program mässigt med hjälp av Azure PowerShell eller REST API. I följande tabeller beskrivs en uppsättning kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

- Information om hur du återställer en fristående databas eller en databas finns i [restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet: | Beskrivning |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Återställer en SQL-databas. |

  > [!TIP]
  > Ett exempel på PowerShell-skript som visar hur du utför en tidpunkts återställning av en databas finns i [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).

- Information om hur du återställer en hanterad instans databas finns i [restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet: | Beskrivning |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hämtar en eller flera hanterade instanser. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hämtar instans databaser. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Återställer en instans databas. |

### <a name="rest-api"></a>REST-API

Så här återställer du en databas med en eller flera databaser med hjälp av REST API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode = återställning)](https://docs.microsoft.com/rest/api/sql/databases) |Återställer en databas |
| [Hämta databas status för att skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/operations) |Returnerar status under en återställnings åtgärd |

### <a name="azure-cli"></a>Azure CLI

- Information om hur du återställer en enskild databas eller en pool med Azure CLI finns i [AZ SQL DB Restore](/cli/azure/sql/db#az-sql-db-restore).
- Information om hur du återställer en hanterad instans med hjälp av Azure CLI finns i [AZ SQL EXTEXTB Restore](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Sammanfattning

Automatiska säkerhets kopieringar skyddar dina databaser från användar-och program fel, oavsiktlig databas borttagning och långvariga avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänst nivåer och beräknings storlekar.

## <a name="next-steps"></a>Nästa steg

- En översikt över kontinuitet och scenarier för affärs kontinuitet finns i [Översikt över verksamhets kontinuitet](sql-database-business-continuity.md).
- Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](sql-database-automated-backups.md).
- Mer information om långsiktig kvarhållning finns i [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Mer information om snabbare återställnings alternativ finns i [Active geo-replikering](sql-database-active-geo-replication.md) eller [grupper för automatisk redundans](sql-database-auto-failover-group.md).
