---
title: Återställ en Azure SQL database från en säkerhetskopia | Microsoft Docs
description: Läs mer om Point-in-Time-återställning, som gör det möjligt för dig att återställa en Azure SQL-databas till en tidigare tidpunkt (upp till 35 dagar).
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
ms.date: 10/01/2018
ms.openlocfilehash: bdd3f5c5304cec0a562945ffaf412771e15b6031
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248269"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Återställa en Azure SQL database med hjälp av automatiska databassäkerhetskopieringar
Som standard lagras SQL Database-säkerhetskopior i geo-replikerade blob storage (RA-GRS). Följande alternativ är tillgängliga för databas återställning med hjälp av [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md):

* Skapa en ny databas på samma logiska server återställt till en viss tidpunkt inom kvarhållningsperioden. 
* Skapa en databas på samma logiska server återställas till borttagning av tid för en borttagen databas.
* Skapa en ny databas på en logisk server i valfri region återställas till det datum då de senaste säkerhetskopiorna.

Om du har konfigurerat [säkerhetskopiera långsiktig kvarhållning](sql-database-long-term-retention.md) du kan också skapa en ny databas från alla LTR backup på en logisk server i valfri region.  

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställning.
>

När du använder Standard eller Premium-tjänstnivån, tillkommer en återställd databas en extra lagringsutrymme kostnad under följande förhållanden: 
- Återställning av P11 – P15 S4 – S12 eller P1 – P6 om den maximala databasstorleken är större än 500 GB.
- Återställning av P1 – P6 till S4 – S12 om den maximala databasstorleken är större än 250 GB.

Extra kostnaden är eftersom maxstorleken på den återställda databasen är större än mängden lagringsutrymme som ingår för beräkningsstorleken och debiteras extra lagringsutrymme utöver det inkluderade extra.  Prisinformation om extra lagringsutrymme finns i den [SQL Database-sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/).  Om den faktiska mängden utrymme som används är mindre än mängden lagringsutrymme som ingår, kan sedan detta extra kostnader undvikas genom att minska den maximala databasstorleken till mängden som ingår.  

> [!NOTE]
> [Automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) används när du skapar en [databaskopieringen](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Tiden för återställning
Tiden för återställning för att återställa en databas med hjälp av automatiska databassäkerhetskopieringar påverkas av flera faktorer: 

* Storleken på databasen
* Beräkningsstorleken för databasen
* Antalet transaktionsloggar som ingår
* Antal aktiviteter som ska återupprepas om du vill återställa till återställningspunkten
* Nätverkets bandbredd om återställningen till en annan region 
* Antal samtidiga återställning begäranden som bearbetas i målregionen. 
  
  För en stor och/eller mycket aktiv databas, kan återställningen ta flera timmar. Om det finns långvarig avbrott i en region, är det möjligt att det finns stora mängder geo-återställning begäranden som bearbetas av andra regioner. När det finns många begäranden, öka återställningstiden för databaser i den regionen. De flesta databasen återställer slutförts inom 12 timmar.

För en enskild prenumeration finns vissa begränsningar för antalet samtidiga restore-begäranden (inklusive punkt i återställning till tidpunkt, geo-återställning och återställa från säkerhetskopiering med LTR) som har skickats och fortsatte:
|  | **Max antal samtidiga begäranden som bearbetas** | **Max antal samtidiga begäranden som skickas** |
| :--- | --: | --: |
|Databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns inga inbyggda funktioner för att bulk återställning. Den [Azure SQL Database: fullständig serveråterställning](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript är ett exempel på ett sätt att utföra den här uppgiften.

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhetskopieringar, måste du vara medlem i rollen SQL Server-deltagare i prenumeration eller vara prenumerationsägaren - läsa [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en befintlig databas till en tidigare tidpunkt som en ny databas på samma logiska server med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> En PowerShell-exempelskript som visar hur du utför en point-in-time-återställning av en databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Databasen kan återställas till valfri tjänstnivå eller beräkningsstorleken, och som en enkel databas eller till en elastisk pool. Se till att du har tillräckligt med resurser på den logiska servern eller i den elastiska poolen som du återställer databasen. När du är klar är den återställda databasen en normal, fullt åtkomlig, online-databas. Den återställda databasen debiteras enligt normal taxa baserat på dess tjänstnivå och beräkningsstorleken. Du inte betalar avgifter förrän återställa databasen är klar.

Du kan vanligtvis återställer en databas till en tidigare tidpunkt för återställning. När du gör detta kan du behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den för att hämta data från och uppdatera sedan den ursprungliga databasen. 

* ***Databasen ersättning:*** om den återställda databasen är avsedd som en ersättning för den ursprungliga databasen, bör du kontrollera beräkningsstorleken och/eller tjänstnivå är lämpliga och skala databasen om det behövs. Du kan byta namn på den ursprungliga databasen och ge sedan den återställda databasen med den ursprungliga namn den [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL. 
* ***Återställning av data:*** om du planerar att hämta data från den återställda databasen att återställa efter ett fel för användaren eller programmet, måste du skriva och köra de nödvändiga data recovery skript för att extrahera data från den återställda databasen till den ursprungliga databasen. Även om återställningen kan ta lång tid att slutföra, visas databasen som ska återställas i databaslistan under återställningsprocessen. Om du tar bort databasen under återställningen återställningen avbryts och du debiteras inte för den databas som inte gick att slutföra återställningen. 

### <a name="azure-portal"></a>Azure Portal

Öppna sidan för din databas om du vill återställa till en tidpunkt med hjälp av Azure-portalen och klicka på **återställa** i verktygsfältet.

![punkt i tiden återställning](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Återställning av databasen som har tagits bort
Du kan återställa en borttagen databas till borttagningstid för en borttagen databas på samma logiska server med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST (createMode = återställer)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Du kan återställa en borttagen databas till en tidigare tidpunkt under kvarhållning av säkerhetskopior med hjälp av [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!Note]
> Återställa borttagen databas finns inte i hanterade instanser.

> [!TIP]
> En PowerShell-exempelskript som visar hur du återställer en borttagen databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Om du tar bort en Azure SQL Database-server-instans, raderas också alla databaser och kan inte återställas. Det finns för närvarande inget stöd för att återställa en borttagen server.
> 

### <a name="azure-portal"></a>Azure Portal

Återställa en borttagen databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure portal, öppnar sidan för din server och i området för åtgärder, klickar på **Borttagna databaser**.

![ta bort-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![ta bort-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Geo-återställning
Du kan återställa en SQL-databas på en server i valfri Azure-region från de senaste geo-replikerade fullständiga och differentiella säkerhetskopieringarna. GEO-återställning använder en geo-redundant säkerhetskopia som källa och kan användas för att återställa en databas, även om databasen eller datacenter är otillgängligt på grund av ett avbrott. 

> [!Note]
> GEO-återställning finns inte i hanterade instanser.

GEO-återställning är standardalternativet för återställning när databasen är inte tillgänglig på grund av en incident i den region där databasen finns. Om en storskalig incident i en region blir otillgänglig programmets databas, kan du återställa en databas från geo-replikerade säkerhetskopior till en server i alla andra regioner. Det finns en fördröjning mellan när en differentiell säkerhetskopiering tas och när det är geo-replikerade till ett Azure blob i en annan region. Den här fördröjningen kan vara upp till en timme, så om en olycka inträffar, det kan ta upp till en timme dataförlust. Följande bild visar återställning av databasen från den senaste tillgängliga säkerhetskopian i en annan region.

![GEO-återställning](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> En PowerShell-exempelskript som visar hur du utför en geo-återställning, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Point-in-time-återställning på en geo-secondary stöds inte för närvarande. Point-in-time-återställning kan göras endast på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns i [återställa från ett avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Återställning från säkerhetskopior är den mest grundläggande haveriberedskapslösningar som är tillgängliga i SQL Database med längsta mål för återställningspunkt (RPO) och uppskattning Recovery tid (ERT). För lösningar med hjälp av små databaser (t.ex. grundläggande tjänstenivå eller mindre storlek klientdatabaser i elastiska pooler), är ofta geo-återställning som en rimlig DR-lösning med en ERT på 12 timmar. Med hjälp av stora databaser för lösningar och kräver kortare tid, bör du använda [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md). Aktiv geo-replikering erbjuder en mycket lägre RPO och ERT eftersom den endast kräver du påbörja en växling till en kontinuerligt replikerad sekundär. Mer information om alternativ för affärskontinuitet finns [översikt över affärskontinuitet](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Azure Portal

Att geo-återställning för en databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure portal, öppna sidan SQL-databaser och klicka sedan på **Lägg till** . I den **Välj källa** textrutan väljer **Backup**. Ange säkerhetskopieringen från som du vill utföra återställningen i regionen och på-servern valfri. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programmässigt utföra återställning med hjälp av automatiska säkerhetskopieringar
Som tidigare beskrivs, utöver Azure portal, kan databasåterställning utföras via programmering med Azure PowerShell eller REST API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell
| Cmdlet | Beskrivning |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Hämtar en eller flera databaser. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Återställer en SQL-databas. |
|  | |

### <a name="rest-api"></a>REST-API
| API | Beskrivning |
| --- | --- |
| [REST (createMode = Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Återställer en databas |
| [Hämta skapa eller uppdatera Database-Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Returnerar status under en återställning |
|  | |

## <a name="summary"></a>Sammanfattning
Automatisk säkerhetskopiering skyddar dina databaser från användare och programfel, databasen oavsiktlig borttagning och långvarig avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänstnivåer och storlekar. 

## <a name="next-steps"></a>Nästa steg
* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
* Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
* Läs om långsiktig kvarhållning i [långsiktig kvarhållning](sql-database-long-term-retention.md).
* Läs om hur du snabbare återställningsalternativ i [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md).  
