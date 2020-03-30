---
title: Återställa en databas från en säkerhetskopia
description: Lär dig mer om point-in-time-återställning, som gör att du kan återställa en Azure SQL-databas på upp till 35 dagar.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268748"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>Återställa en Azure SQL-databas med hjälp av automatiska säkerhetskopieringar av databaser

Som standard lagras Azure SQL Database-säkerhetskopior i geo-replikerad blob storage (RA-GRS-lagringstyp). Följande alternativ är tillgängliga för återställning av databaser med hjälp av [automatiska databassäkerhetskopior](sql-database-automated-backups.md). Du kan:

- Skapa en ny databas på samma SQL Database-server, återställd till en angiven tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma SQL Database-server, återställd till borttagningstiden för en borttagen databas.
- Skapa en ny databas på en SQL Database-server i samma region, återställd till den grad att de senaste säkerhetskopiorna har säkerhetskopierats.
- Skapa en ny databas på alla SQL Database-servrar i någon annan region, återställd till den grad att de senaste replikerade säkerhetskopiorna har replikerats.

Om du har konfigurerat [långsiktig kvarhållning för säkerhetskopiering](sql-database-long-term-retention.md)kan du också skapa en ny databas från en långsiktig återställningssäkerhetskopiering på valfri SQL Database-server.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställningen.

När du använder tjänstnivåerna Standard eller Premium kan databasåterställning medföra en extra lagringskostnad. Den extra kostnaden uppstår när den maximala storleken på den återställda databasen är större än mängden lagringsutrymme som ingår i måldatabasens tjänstnivå och prestandanivå. Information om priser för extra lagringsutrymme finns på [prissidan för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden använt utrymme är mindre än mängden lagringsutrymme som ingår, kan du undvika den här extra kostnaden genom att ange den maximala databasstorleken till det medföljande beloppet.

## <a name="recovery-time"></a>Återhämtningstid

Återställningstiden för att återställa en databas med hjälp av automatiska säkerhetskopieringar av databaser påverkas av flera faktorer:

- Storleken på databasen.
- Databasens beräkningsstorlek.
- Antalet transaktionsloggar inblandade.
- Mängden aktivitet som måste spelas upp igen för att återställa till återställningspunkten.
- Nätverksbandbredden om återställningen är till en annan region.
- Antalet samtidiga återställningsbegäranden som bearbetas i målregionen.

För en stor eller mycket aktiv databas kan återställningen ta flera timmar. Om det finns ett långvarigt avbrott i en region är det möjligt att ett stort antal begäranden om geoåterställning initieras för haveriberedskap. När det finns många begäranden kan återställningstiden för enskilda databaser öka. De flesta databasåterställningar slutförs på mindre än 12 timmar.

För en enskild prenumeration finns det begränsningar för antalet samtidiga återställningsbegäranden. Dessa begränsningar gäller för alla kombinationer av point-in-time-återställningar, geo-återställningar och återställningar från långsiktig lagringssäkerhetskopiering.

| | **Max # av samtidiga begäranden som bearbetas** | **Max # av samtidiga begäranden som skickas in** |
| :--- | --: | --: |
|En databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns ingen inbyggd metod för att återställa hela servern. Ett exempel på hur du utför den här uppgiften finns i [Azure SQL Database: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666).

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhetskopior måste du vara medlem i SQL Server-deltagarrollen i prenumerationen eller vara prenumerationsägare. Mer information finns [i RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure-portalen, PowerShell eller REST API. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en fristående, poolad eller instansdatabas till en tidigare tidpunkt med hjälp av Azure-portalen, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)eller [REST API](https://docs.microsoft.com/rest/api/sql/databases). Begäran kan ange valfri tjänstnivå eller beräkningsstorlek för den återställda databasen. Kontrollera att du har tillräckligt med resurser på den server som du återställer databasen till. När den är klar skapar återställningen en ny databas på samma server som den ursprungliga databasen. Den återställda databasen debiteras med normala hastigheter, baserat på dess tjänstnivå och beräkningsstorlek. Du debiteras inte förrän databasåterställningen är klar.

Du återställer vanligtvis en databas till en tidigare punkt för återställningsändamål. Du kan behandla den återställda databasen som en ersättning för den ursprungliga databasen eller använda den som en datakälla för att uppdatera den ursprungliga databasen.

- **Byte av databas**

  Om du tänker att den återställda databasen ska ersätta den ursprungliga databasen bör du ange den ursprungliga databasens beräkningsstorlek och tjänstnivå. Du kan sedan byta namn på den ursprungliga databasen och ge den återställda databasen det ursprungliga namnet med kommandot [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Dataräddning**

  Om du planerar att hämta data från den återställda databasen för att återställa från ett användar- eller programfel måste du skriva och köra ett dataåterställningsskript som extraherar data från den återställda databasen och gäller för den ursprungliga databasen. Även om återställningen kan ta lång tid att slutföra, visas återställningsdatabasen i databaslistan under hela återställningsprocessen. Om du tar bort databasen under återställningen avbryts återställningen och du debiteras inte för databasen som inte slutförde återställningen.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Återställning i tid med hjälp av Azure-portal

Du kan återställa en enda SQL-databas eller instansdatabas till en tidpunkt från översiktsbladet för den databas som du vill återställa i Azure-portalen.

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Om du vill återställa en enskild eller poolad databas till en tidpunkt med hjälp av Azure-portalen öppnar du databasöversiktssidan och väljer **Återställ** i verktygsfältet. Välj säkerhetskopieringskälla och välj den tidpunkt då en ny databas ska skapas. 

  ![Skärmbild av alternativ för återställning av databaser](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Om du vill återställa en hanterad instansdatabas till en tidpunkt med hjälp av Azure-portalen öppnar du databasöversiktssidan och väljer **Återställ** i verktygsfältet. Välj den tidpunkt då en ny databas ska skapas. 

  ![Skärmbild av alternativ för återställning av databaser](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> Information om hur du återställer en databas från en säkerhetskopia [programmatiskt med automatiska säkerhetskopior](sql-database-recovery-using-backups.md).

## <a name="deleted-database-restore"></a>Borttagen databasåterställning

Du kan återställa en borttagen databas till borttagningstiden, eller en tidigare tidpunkt, på samma SQL Database-server eller samma hanterade instans. Du kan åstadkomma detta via Azure-portalen, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)eller [REST (createMode=Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Du återställer en borttagen databas genom att skapa en ny databas från säkerhetskopian.

> [!IMPORTANT]
> Om du tar bort en Azure SQL Database-server eller hanterad instans tas även alla dess databaser bort och kan inte återställas. Du kan inte återställa en borttagen server eller hanterad instans.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Borttagen databasåterställning med hjälp av Azure-portalen

Du återställer borttagna databaser från Azure-portalen från server- och instansresursen.

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Om du vill återställa en en eller flera borttagna databaser till borttagningstiden med hjälp av Azure-portalen öppnar du serveröversiktssidan och väljer **Borttagna databaser**. Markera en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data återställda från säkerhetskopian.

  ![Skärmbild av återställning av borttagen Azure SQL-databas](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Om du vill återställa en hanterad databas med hjälp av Azure-portalen öppnar du översiktssidan för hanterade instanser och väljer **Borttagna databaser**. Markera en borttagen databas som du vill återställa och skriv namnet på den nya databasen som ska skapas med data återställda från säkerhetskopian.

  ![Skärmbild av den borttagna Azure SQL-instansdatabasen](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>Borttagen databasåterställning med PowerShell

Använd följande exempelskript för att återställa en borttagen databas för Azure SQL Database och en hanterad instans med hjälp av PowerShell.

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Ett exempel på PowerShell-skript som visar hur du återställer en borttagen Azure SQL-databas finns i [Återställa en SQL-databas med PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Ett exempel på PowerShell-skript som visar hur du återställer en borttagen instansdatabas finns i [Återställa borttagen databas på hanterad instans med PowerShell](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database)

> [!TIP]
> Information om hur du återställer en borttagen databas [programmatiskt med automatiska säkerhetskopior](sql-database-recovery-using-backups.md).

## <a name="geo-restore"></a>Geo-återställning

Du kan återställa en SQL-databas på valfri server i alla Azure-regioner från de senaste geo-replikerade säkerhetskopiorna. Geoåterställning använder en geore replikerad säkerhetskopia som källa. Du kan begära geoåterställning även om databasen eller datacentret inte är tillgängligt på grund av ett avbrott.

Geoåterställning är standardalternativet för återställning när databasen inte är tillgänglig på grund av en incident i värdregionen. Du kan återställa databasen till en server i valfri annan region. Det finns en fördröjning mellan när en säkerhetskopia tas och när den geo-replikeras till en Azure-blob i en annan region. Därför kan den återställda databasen vara upp till en timme efter den ursprungliga databasen. Följande bild visar en databasåterställning från den senast tillgängliga säkerhetskopian i en annan region.

![Grafik av geo-återställande](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Geo-återställning med hjälp av Azure-portalen

Från Azure-portalen skapar du en ny databas med en eller hanterad instans och väljer en tillgänglig säkerhetskopia för geoåterställning. Den nyskapade databasen innehåller geoåterdanerade säkerhetskopierade data.

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Så här georenoverar du en enda SQL-databas från Azure-portalen i den region och den server du väljer:

1. Välj Lägg **till** > **Skapa SQL-databas på** **instrumentpanelen**. Ange den information som krävs på fliken **Grunderna.**
2. Välj **Ytterligare inställningar**.
3. För **Använd befintliga data**väljer du **Säkerhetskopiering**.
4. För **säkerhetskopiering**väljer du en säkerhetskopia i listan över tillgängliga geoåterställningssäkerheter.

    ![Skärmbild av alternativ för Skapa SQL-databas](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

Slutför processen med att skapa en ny databas från säkerhetskopian. När du skapar den enda Azure SQL-databasen innehåller den den återställda säkerhetskopian av geoåterställning.

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Om du vill geo-återställa en hanterad instansdatabas från Azure-portalen till en befintlig hanterad instans i ett område som du väljer väljer väljer du en hanterad instans där du vill att en databas ska återställas. Följ de här stegen:

1. Välj **Ny databas**.
2. Skriv ett önskat databasnamn.
3. Under **Använd befintliga data**väljer du **Säkerhetskopiering**.
4. Välj en säkerhetskopia i listan över tillgängliga säkerhetskopieringar för geoåterställning.

    ![Skärmbild av Nya databasalternativ](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

Slutför processen med att skapa en ny databas. När du skapar instansdatabasen innehåller den den återställda säkerhetskopian för geoåterställning.

### <a name="geo-restore-by-using-powershell"></a>Geoåterställning med PowerShell

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Ett PowerShell-skript som visar hur du utför geoåterställning för en enda SQL-databas finns i [Använda PowerShell för att återställa en enda Azure SQL-databas till en tidigare tidpunkt](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Ett PowerShell-skript som visar hur du utför geoåterställning för en hanterad instansdatabas finns i [Använda PowerShell för att återställa en hanterad instansdatabas till en annan georegion](scripts/sql-managed-instance-restore-geo-backup.md).

### <a name="geo-restore-considerations"></a>Överväganden om geoåterställning

Du kan inte utföra en point-in-time-återställning i en geo-sekundär databas. Du kan bara göra det i en primär databas. Detaljerad information om hur du använder geoåterställning för att återställa från ett avbrott finns i [Återställ från ett avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> Geoåterställning är den mest grundläggande katastrofåterställningslösningen som finns i SQL Database. Den är beroende av automatiskt skapade geo-replikerade säkerhetskopior med återställningspunktmål (RPO) lika med 1 timme och den beräknade återställningstiden på upp till 12 timmar. Det garanterar inte att målregionen kommer att ha kapacitet att återställa dina databaser efter ett regionalt avbrott, eftersom en kraftig ökning av efterfrågan är sannolikt. Om ditt program använder relativt små databaser och inte är avgörande för företaget är geoåterställning en lämplig lösning för haveriberedskap. För affärskritiska program som kräver stora databaser och måste säkerställa kontinuitet i verksamheten använder du [Grupper för automatisk redundans](sql-database-auto-failover-group.md). Det erbjuder en mycket lägre RPO och återhämtningstid mål, och kapaciteten är alltid garanteras. Mer information om val av affärskontinuitet finns i [Översikt över kontinuitet i verksamheten](sql-database-business-continuity.md).

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>Programmässigt utför återställning med hjälp av automatiska säkerhetskopior

Du kan också använda Azure PowerShell eller REST API för återställning. I följande tabeller beskrivs den uppsättning kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-moduler är i stor utsträckning identiska.

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Information om hur du återställer en fristående eller poolad databas finns i [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
  | [Hämta-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Hämta AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Återställ-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Återställer en SQL-databas. |

  > [!TIP]
  > Ett exempel på PowerShell-skript som visar hur du utför en point-in-time-återställning av en databas finns i [Återställa en SQL-databas med PowerShell](scripts/sql-database-restore-database-powershell.md).

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Information om hur du återställer en databas med hanterade instanser finns i [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Få-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hämtar en eller flera hanterade instanser. |
  | [Hämta AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hämtar en instansdatabas. |
  | [Återställ-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Återställer en instansdatabas. |

### <a name="rest-api"></a>REST API

Så här återställer du en enskild eller poolad databas med REST API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Återställer en databas. |
| [Hämta status för Skapa eller uppdatera databas](https://docs.microsoft.com/rest/api/sql/operations) |Returnerar statusen under en återställning. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>Enkel Azure SQL-databas

Information om hur du återställer en enskild eller poolad databas med hjälp av Azure CLI finns i [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).

#### <a name="managed-instance-database"></a>Databas med hanterad instans

Information om hur du återställer en hanterad instansdatabas med hjälp av Azure CLI finns i [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore).

## <a name="summary"></a>Sammanfattning

Automatiska säkerhetskopior skyddar dina databaser från användar- och programfel, oavsiktlig databasborttagning och långvariga avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänstnivåer och beräkningsstorlekar.

## <a name="next-steps"></a>Nästa steg

- [Översikt över kontinuitet i verksamheten](sql-database-business-continuity.md)
- [Automatiska säkerhetskopior av SQL Database](sql-database-automated-backups.md)
- [Långsiktig kvarhållning](sql-database-long-term-retention.md)
- Mer information om snabbare återställningsalternativ finns i [Aktiva geo-replikerings-](sql-database-active-geo-replication.md) eller [automatisk redundansgrupper](sql-database-auto-failover-group.md).
