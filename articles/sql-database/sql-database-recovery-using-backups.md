---
title: Återställa en Azure SQL-databas från en säkerhetskopia | Microsoft Docs
description: Läs mer om Point-in-Time-återställning, där du kan återställa en Azure SQL Database till en tidigare tidpunkt (upp till 35 dagar).
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: f40bd7954bbf079c87f8312bff731b68d1acb7dc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Återställa en Azure SQL-databas med automatisk databassäkerhetskopieringar
SQL-databas finns följande alternativ för databas återställning med hjälp av [automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) och [säkerhetskopieringar i långsiktig kvarhållning](sql-database-long-term-retention.md). Du kan återställa från en säkerhetskopia av databasen till:

* En ny databas på samma logiska server återställs till en angiven tidpunkt inom kvarhållningsperioden. 
* En databas på samma logiska server återställs till borttagningstid för en borttagen databas.
* En ny databas på en logisk server i en region som återställs till platsen där de senaste dagliga säkerhetskopior i georeplikerad blob storage (RA-GRS).

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställning.
>

En återställd databas har en extra lagring kostnad under följande förhållanden: 
- Återställning av P11 – P15 S12 S4 förpackningen eller P1 – P6 om den maximala databasstorleken är större än 500 GB.
- Återställning av P1 – P6 till S12 S4 förpackningen om den maximala databasstorleken är större än 250 GB.

Extra kostnaden är eftersom maxstorleken på den återställda databasen är större än mängden lagringsutrymme som ingår för prestandanivå och eventuella extra lagring som etablerats över inkluderade mängden debiteras extra.  Prisinformation extra lagringsutrymme, finns det [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/).  Om den faktiska mängden diskutrymme som används är mindre än mängden lagringsutrymme som ingår, kan sedan detta extra kostnad undvikas genom att minska den maximala databasstorleken så ingår. Mer information om databasen lagringsstorlekar och ändra den maximala databasstorleken finns [enkel databas DTU-baserade gränserna](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels) och [enkel databas vCore-baserade gränserna för](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels).  

> [!NOTE]
> [Automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) används när du skapar en [databaskopieringen](sql-database-copy.md). 
>


## <a name="recovery-time"></a>Tiden för återställning
Tiden för återställning för att återställa en databas med hjälp av automatisk databassäkerhetskopieringar påverkas av flera faktorer: 

* Storleken på databasen
* Prestandanivå för databasen
* Antalet transaktionsloggar ingår
* Antal aktiviteter som ska återupprepas om du vill återställa till återställningspunkten
* Nätverkets bandbredd om återställningen görs till en annan region 
* Antalet samtidiga återställning begäranden som bearbetas i mål-region. 
  
  För en stor och/eller mycket aktiv databas kan återställningen ta flera timmar. Om det är långvariga avbrott i en region, är det möjligt att det finns ett stort antal geo-återställning begäranden som bearbetas av andra regioner. När det finns många begäranden, kan öka återställningstiden för databaser i den regionen. De flesta databasen återställs fullständig inom 12 timmar.

För en enda prenumeration finns vissa begränsningar för antalet samtidiga återställning förfrågningar (inklusive punkt i tidpunkt för återställning, geo återställning och återställning från en säkerhetskopia för långsiktig kvarhållning) som har skickats och fortsatte:
|  | **Högsta antal samtidiga begäranden som bearbetas** | **Högsta antal samtidiga förfrågningar som skickas** |
| :--- | --: | --: |
|Enskild databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns ingen inbyggd funktion för att Massredigera återställning. Den [Azure SQL Database: fullständig serveråterställning](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript är ett exempel på ett sätt att utföra den här uppgiften.

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatisk säkerhetskopiering, måste du vara medlem i rollen SQL Server-deltagare i prenumeration eller vara prenumerationsägaren. Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en befintlig databas till en tidigare tidpunkt som en ny databas på samma logiska server med hjälp av Azure portal [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!TIP]
> Ett exempel PowerShell-skript visar hur du utför en point-in-time-återställning av en databas finns [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
>

Databasen kan återställas till en nivå eller prestanda servicenivå, och som en enskild databas eller i en elastisk pool. Se till att du har tillräckligt med resurser på den logiska servern eller i den elastiska poolen som du återställer databasen. När du är klar, är den återställda databasen en normal, fullständigt tillgänglig online-databas. Den återställda databasen debiteras med normal priser baserat på dess servicenivå för tjänstnivå och prestandanivå. Du inte betalar avgifter förrän databasen återställningen är slutförd.

Du återställer vanligtvis en databas till en tidigare för återställning. När du gör det kan behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den för att hämta data från och uppdatera sedan den ursprungliga databasen. 

* ***Databasen ersättning:*** om den återställda databasen är avsedd som en ersättning för den ursprungliga databasen, bör du kontrollera prestandanivå och/eller tjänstnivå är lämpliga och skala databasen om det behövs. Du kan byta namn på den ursprungliga databasen och ge den återställda databasen sedan den ursprungliga namn med hjälp av den [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL. 
* ***Återställning av data:*** om du planerar att hämta data från den återställda databasen för att återställa från en användare eller ett program fel, måste du skriva och köra de nödvändiga data recovery skript om du vill extrahera data från den återställda databasen till den ursprungliga databasen. Även om återställningen kan ta lång tid att slutföra, visas databasen som ska återställas i databaslistan under återställningen. Om du tar bort databasen under återställningen återställningen avbryts och du debiteras inte för den databas som inte gick att slutföra återställningen. 

### <a name="azure-portal"></a>Azure Portal

Om du vill återställa till en tidpunkt med hjälp av Azure portal, öppna sidan för din databas och klicka på **återställa** i verktygsfältet.

![punkt i tiden återställning](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Återställning av databasen borttagen
Du kan återställa en borttagen databas till borttagningstid för en borttagen databas på samma logiska server med hjälp av Azure portal [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase), eller [REST (createMode = återställer)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Du kan återställa en borttagen databas till en tidigare under kvarhållning med [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!TIP]
> Ett exempel PowerShell-skript visar hur du kan återställa en borttagen databas finns [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
>

> [!IMPORTANT]
> Om du tar bort en Azure SQL Database server-instansen tas även bort alla databaser och kan inte återställas. Det finns inget stöd för att återställa en borttagen server.
> 

### <a name="azure-portal"></a>Azure Portal

Återställa en borttagen databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure-portalen, öppna sidan för din server och i området för åtgärder, klickar du på **Bort databaser**.

![ta bort-databasen-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)


![ta bort-databasen-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Geo-återställning
Du kan återställa en SQL-databas på en server i Azure-region från de senaste georeplikerad fullständig och differentiell säkerhetskopieringarna. GEO-återställning använder geo-redundant säkerhetskopia som källa och kan användas för att återställa en databas, även om databasen eller datacenter inte är tillgänglig på grund av ett avbrott. 

GEO-återställning är standardalternativet när databasen är inte tillgänglig på grund av en incident i den region där databasen finns. Om en storskalig incident i en region resulterar i att programmets databasen återställer du en databas från säkerhetskopior georeplikerad till en server i en annan region. Det uppstår en fördröjning mellan när en differentiell säkerhetskopiering utförs och när den är georeplikerad till ett Azure blob i en annan region. Den här fördröjningen kan vara upp till en timme, så om en olycka inträffar, det kan vara upp till en timme förlust av data. Följande bild visar återställning av databasen från den senaste tillgängliga säkerhetskopian i en annan region.

![GEO-återställning](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> En PowerShell-exempelskript visar hur du utför en geo-återställning, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
> 

Point-in-time-återställning på en geo sekundär stöds inte för närvarande. Point-in-time-återställning kan göras endast på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns [återställa från avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Återställning från säkerhetskopior är den mest grundläggande katastrofåterställning som är tillgängliga i SQL-databas med längsta Recovery punkt mål (RPO) och uppskattning Recovery tid (Infoga). Lösningar med hjälp av små databaser (t.ex. grundläggande tjänstnivån eller klient databaser i elastiska pooler är små), är geo-återställning ofta en rimlig DR-lösning med en Infoga på 12 timmar. Lösningar med stora databaser och kortare återkräva tider, bör du använda [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md). Aktiv geo-replikering har en mycket lägre Återställningspunktmål och infoga eftersom det endast kräver att du påbörja en växling till en kontinuerligt replikerade sekundär. Mer information om business continuity val finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
> 

### <a name="azure-portal"></a>Azure Portal

Att geo-återställning för en databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure-portalen, öppna sidan SQL-databaser och klicka sedan på **Lägg till** . I den **Välj källa** textruta, Välj **säkerhetskopiering**. Ange säkerhetskopian som du vill återställa i region och på servern du väljer. 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programmässigt utföra återställning med hjälp av automatisk säkerhetskopiering
Som tidigare vi nämnt, utöver Azure portal, kan återställning av databasen utföras via programmering med hjälp av Azure PowerShell eller REST API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga.

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
| [REST (createMode = återställning)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Återställer en databas |
| [Hämta skapa eller uppdatera databasens Status](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Returnerar status under en återställning |
|  | |

## <a name="summary"></a>Sammanfattning
Automatisk säkerhetskopiering skydda dina databaser från användar- och programfel, oavsiktliga databasadministratörer att ta bort och långvariga avbrott. Den här inbyggda funktionen är tillgänglig för alla servicenivåer och prestandanivåer. 

## <a name="next-steps"></a>Nästa steg
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md).
* Läs om långsiktig kvarhållning i [långsiktig kvarhållning](sql-database-long-term-retention.md).
* Mer information om alternativ för snabbare återställning, se [redundans grupper och aktiv geo-replikering](sql-database-geo-replication-overview.md).  
