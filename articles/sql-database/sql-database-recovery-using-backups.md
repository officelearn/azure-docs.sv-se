---
title: Återställ en Azure SQL database från en säkerhetskopia | Microsoft Docs
description: Läs mer om Point-in-Time-återställning, som gör det möjligt för dig att återställa en Azure SQL-databas till en tidigare tidpunkt (upp till 35 dagar).
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
ms.date: 10/23/2018
ms.openlocfilehash: 301b0179c8222bfdff3b07f7962a74a4cc83b8f6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432293"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Återställa en Azure SQL database med hjälp av automatiska databassäkerhetskopieringar

Som standard lagras SQL Database-säkerhetskopior i geo-replikerade blob storage (RA-GRS). Följande alternativ är tillgängliga för databas återställning med hjälp av [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md):

- Skapa en ny databas på samma logiska server återställt till en viss tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma logiska server återställas till borttagning av tid för en borttagen databas.
- Skapa en ny databas på en logisk server i samma region återställas till det datum då de senaste säkerhetskopiorna.
- Skapa en ny databas på en logisk server i någon annan region återställas till det datum då de senaste replikerade säkerhetskopiorna.

Om du har konfigurerat [säkerhetskopiera långsiktig kvarhållning](sql-database-long-term-retention.md) du kan också skapa en ny databas från alla LTR backup på en logisk server i valfri region.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställning.

När du använder Standard eller Premium-tjänstnivån, tillkommer en återställd databas en extra lagringsutrymme kostnad under följande förhållanden:

- Återställning av P11 – P15 S4 – S12 eller P1 – P6 om den maximala databasstorleken är större än 500 GB.
- Återställning av P1 – P6 till S4 – S12 om den maximala databasstorleken är större än 250 GB.

Extra kostnaden är eftersom maxstorleken på den återställda databasen är större än mängden lagringsutrymme som ingår för beräkningsstorleken och debiteras extra lagringsutrymme utöver det inkluderade extra. Prisinformation om extra lagringsutrymme finns i den [SQL Database-sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än mängden lagringsutrymme som ingår, kan sedan detta extra kostnader undvikas genom att minska den maximala databasstorleken till mängden som ingår.

> [!NOTE]
> [Automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) används när du skapar en [databaskopieringen](sql-database-copy.md).

## <a name="recovery-time"></a>Tiden för återställning

Tiden för återställning för att återställa en databas med hjälp av automatiska databassäkerhetskopieringar påverkas av flera faktorer:

- Storleken på databasen
- Beräkningsstorleken för databasen
- Antalet transaktionsloggar som ingår
- Antal aktiviteter som ska återupprepas om du vill återställa till återställningspunkten
- Nätverkets bandbredd om återställningen till en annan region
- Antalet samtidiga återställning begäranden som bearbetas i målregionen

För en stor och/eller mycket aktiv databas, kan återställningen ta flera timmar. Om det finns långvarig avbrott i en region, är det möjligt att det finns stora mängder geo-återställning begäranden som bearbetas av andra regioner. När det finns många begäranden, öka återställningstiden för databaser i den regionen. De flesta databasen återställer klara på mindre än 12 timmar.

För en enskild prenumeration finns vissa begränsningar för antalet samtidiga restore-begäranden (inklusive punkt i återställning till tidpunkt, geo-återställning och återställa från säkerhetskopiering med LTR) som har skickats och fortsatte:

| | **Max antal samtidiga begäranden som bearbetas** | **Max antal samtidiga begäranden som skickas** |
| :--- | --: | --: |
|Databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns inga inbyggda funktioner för att bulk återställning. Den [Azure SQL Database: Fullständig återställning av](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript är ett exempel på ett sätt att utföra den här uppgiften.

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhetskopieringar, måste du vara medlem i rollen SQL Server-deltagare i prenumeration eller vara prenumerationsägaren - läsa [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en enskild, delade eller hanterad instans-databas till en tidigare tidpunkt som en ny databas på samma server med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST API](https://docs.microsoft.com/rest/api/sql/databases). En databas kan återställas till valfri tjänstnivå eller compute storlek. Se till att du har tillräckligt med resurser på den server som du återställer databasen. När du är klar är den återställda databasen en normal, fullt åtkomlig, online-databas. Den återställda databasen debiteras enligt normal taxa baserat på dess tjänstnivå och beräkningsstorleken. Du inte betalar avgifter förrän återställa databasen är klar.

Du kan vanligtvis återställer en databas till en tidigare tidpunkt för återställning. När du gör detta kan du behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den för att hämta data från och uppdatera sedan den ursprungliga databasen.

- **Databasersättning av**

  Om den återställda databasen är avsedd som en ersättning för den ursprungliga databasen, bör du kontrollera beräkningsstorleken och/eller tjänstnivå är lämpliga och skala databasen om det behövs. Du kan byta namn på den ursprungliga databasen och ge sedan den återställda databasen med den ursprungliga namn den [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Återställning av data**

  Om du planerar att hämta data från den återställda databasen att återställa efter ett fel för användaren eller programmet, måste du skriva och köra de nödvändiga data recovery skript för att extrahera data från den återställda databasen till den ursprungliga databasen. Även om återställningen kan ta lång tid att slutföra, visas databasen som ska återställas i databaslistan under återställningsprocessen. Om du tar bort databasen under återställningen återställningen avbryts och du debiteras inte för den databas som inte gick att slutföra återställningen.

Öppna sidan för din databas om du vill återställa en enskild, delade eller hanterad instans-databas till en tidpunkt med hjälp av Azure-portalen och klicka på **återställa** i verktygsfältet.

![punkt i tiden återställning](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Om du vill återställa en databas från en säkerhetskopia programmässigt Se [programmässigt utföra återställning med hjälp av automatisk säkerhetskopiering](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Återställning av databasen som har tagits bort

Du kan återställa en borttagen databas till borttagningstid för en borttagen databas på samma logiska server med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST (createMode = återställer)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Du kan [återställa borttagen databas på hanterad instans med hjälp av PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2019/01/21/recreate-dropped-database-on-azure-sql-managed-instance). Du kan återställa en borttagen databas till en tidigare tidpunkt under kvarhållning av säkerhetskopior med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> En PowerShell-exempelskript som visar hur du återställer en borttagen databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Om du tar bort en Azure SQL Database-server-instans, raderas också alla databaser och kan inte återställas. Det finns för närvarande inget stöd för att återställa en borttagen server.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Återställning av borttagna databasen med hjälp av Azure portal

Återställa en borttagen databas via Azure-portalen under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure portal, öppna sidan för din server och i den Operations-området klickar du på **borttagna databaser**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Om du vill återställa en borttagen databas via programmering, se [programmässigt utföra återställning med hjälp av automatisk säkerhetskopiering](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Geo-återställning

Du kan återställa en SQL-databas på en server i valfri Azure-region från de senaste geo-replikerade säkerhetskopiorna. GEO-återställning använder en geo-redundant säkerhetskopia som källa och kan användas för att återställa en databas, även om databasen eller datacenter är otillgängligt på grund av ett avbrott.

> [!Note]
> GEO-återställning finns inte i hanterade instanser.

GEO-återställning är standardalternativet för återställning när databasen är inte tillgänglig på grund av en incident i den region där databasen finns. Om en storskalig incident i en region blir otillgänglig programmets databas, kan du återställa en databas från geo-replikerade säkerhetskopior till en server i alla andra regioner. Det finns en fördröjning mellan när en säkerhetskopia görs och när det är geo-replikerade till ett Azure blob i en annan region. Den här fördröjningen kan vara upp till en timme, så om en olycka inträffar, det kan ta upp till en timme dataförlust. Följande bild visar återställning av databasen från den senaste tillgängliga säkerhetskopian i en annan region.

![GEO-återställning](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> En PowerShell-exempelskript som visar hur du utför en geo-återställning, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).

Point-in-time-återställning på en geo-secondary stöds inte för närvarande. Point-in-time-återställning kan göras endast på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns i [återställa från ett avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Återställning från säkerhetskopior är den mest grundläggande haveriberedskapslösningar som är tillgängliga i SQL Database med längsta mål för återställningspunkt (RPO) och uppskattning Recovery tid (ERT). För lösningar med hjälp av små databaser (t.ex. grundläggande tjänstenivå eller mindre storlek klientdatabaser i elastiska pooler), är ofta geo-återställning som en rimlig DR-lösning med en ERT på upp till 12 timmar (vanligtvis mycket mindre). Med hjälp av stora databaser för lösningar och kräver kortare tid, bör du använda [aktiv geo-replikering](sql-database-active-geo-replication.md) eller [automatisk redundans grupper](sql-database-auto-failover-group.md). Aktiv geo-replikering erbjuder en mycket lägre RPO och ERT eftersom den endast kräver du påbörja en växling till en kontinuerligt replikerad sekundär. Automatisk redundans grupper aktivera automatisk redundans för en grupp med databaser. Mer information om alternativ för affärskontinuitet finns [översikt över affärskontinuitet](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>GEO-återställning med hjälp av Azure portal

Att geo-återställning för en databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure portal, öppna sidan SQL-databaser och klicka sedan på **Lägg till** . I den **Välj källa** textrutan väljer **Backup**. Ange säkerhetskopieringen från som du vill utföra återställningen i regionen och på-servern valfri.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programmässigt utföra återställning med hjälp av automatiska säkerhetskopieringar

Som tidigare beskrivs, utöver Azure portal, kan databasåterställning utföras via programmering med Azure PowerShell eller REST API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

- Om du vill återställa en enskild eller grupperade database, se [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase)

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Hämtar en eller flera databaser. |
  | [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Återställer en SQL-databas. |

  > [!TIP]
  > En PowerShell-exempelskript som visar hur du utför en point-in-time-återställning av en databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).

- Om du vill återställa en databas för hanterad instans, se [Point-in-time-återställning av en databas på Azure SQL Managed Instance med hjälp av PowerShell i AzureRm.Sql bibliotek](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/28/point-in-time-restore-of-a-database-on-azure-sql-managed-instance-using-azurerm-sql-powershell-library/)

### <a name="rest-api"></a>REST-API

Du återställer en enda eller grupperade databas med hjälp av REST-API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Återställer en databas |
| [Hämta skapa eller uppdatera Database-Status](https://docs.microsoft.com/rest/api/sql/operations) |Returnerar status under en återställning |

### <a name="azure-cli"></a>Azure CLI

Om du vill återställa en enskild eller grupperade databas med Azure CLI, se [az sql db restore](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-restore).

## <a name="summary"></a>Sammanfattning

Automatisk säkerhetskopiering skyddar dina databaser från användare och programfel, databasen oavsiktlig borttagning och långvarig avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänstnivåer och storlekar.

## <a name="next-steps"></a>Nästa steg

- En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
- Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
- Läs om långsiktig kvarhållning i [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Läs om hur du snabbare återställningsalternativ i [aktiv geo-replikering](sql-database-active-geo-replication.md) eller [automatisk redundans grupper](sql-database-auto-failover-group.md).
