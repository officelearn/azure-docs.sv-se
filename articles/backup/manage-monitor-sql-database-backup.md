---
title: Hantera och övervaka SQL Server-databaser på en Azure virtuell dator som backas upp av Azure Backup | Microsoft Docs
description: Den här artikeln beskrivs hur du återställer SQL Server-databaser som körs på en virtuell Azure-dator som säkerhetskopieras med Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430803"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser 


Den här artikeln beskrivs vanliga uppgifter för hantering och övervakning av SQL Server-databaser som körs på en virtuell Azure-dator som säkerhetskopieras till en Azure Backup Recovery Services-valvet genom den [Azure Backup](backup-overview.md) service. Uppgifter som övervakning av jobb och aviseringar, stoppar och återupptar databasskyddet, kör säkerhetskopieringsjobb och avregistrera en virtuell dator från en säkerhetskopia.


> [!NOTE]
> Säkerhetskopiering av SQl Server-databaser som körs på en virtuell Azure-dator med Azure Backup är för närvarande i offentlig förhandsversion.


TIf som du inte har gjort men konfigurerad säkerhetskopiering för SQL Server-databaser, följer du anvisningarna i [i den här artikeln](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Övervaka säkerhetskopieringsjobb

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Övervaka ad hoc-jobb i portalen

Azure Backup visar alla manuellt utlösta jobb i den **säkerhetskopieringsjobb** portalen, inklusive identifiera och registrera databaser och säkerhetskopiering och återställning.

![Portalen för säkerhetskopieringsjobb](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Schemalagda säkerhetskopieringsjobb visas inte i den **säkerhetskopieringsjobb** portal. Använd SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Övervaka säkerhetskopieringsjobb med SQL Server Management Studio 

Azure Backup använder SQL-specifika API:er för alla säkerhetskopieringsåtgärder.

Använd specifika API:er för att hämta all jobbinformation från [SQL backupset-tabellen](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen.

Följande exempel är en fråga som hämtar alla säkerhetskopieringsjobb för en databas med namnet **DB1**. Anpassa frågan för avancerad övervakning.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom loggsäkerhetskopior sker var 15: e minut, kan det vara tråkigt att övervaka säkerhetskopieringsjobb. Azure Backup förenklar övervakning med e-postaviseringar.

- Aviseringar har utlösts för alla Säkerhetskopieringsfel.
- Aviseringar konsolideras på databasnivå efter felkod.
- En e-postavisering skickas endast för det första säkerhetskopieringsfelet för en databas. 

Så här övervakar du säkerhetskopieringsaviseringar:

1. Logga in på Azure-prenumerationen i den [Azure-portalen](https://portal.azure.com) att övervaka databasvarningar.

2. På instrumentpanelen för valvet väljer **aviseringar och händelser**.

   ![Välja aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. I **aviseringar och händelser**väljer **säkerhetskopiering aviseringar**.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan stoppa säkerhetskopiering av SQL Server-databasen i ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb, men lämna kvar återställningspunkterna.

Tänk på följande:

Om du lämnar återställningspunkterna tas poäng bort i enlighet med principen för säkerhetskopiering. Du debiteras för den skyddade instansen och förbrukad lagring tills alla återställningspunkter rensas. [Läs mer](https://azure.microsoft.com/pricing/details/backup/) om prissättning.
- När du lämnar återställningspunkter intakta, även om de går ut enligt policy för datalagring, håller Azure Backup alltid en senaste återställningspunkt tills du uttryckligen tar bort säkerhetskopierade data.
- Om du tar bort en datakälla utan att stoppa säkerhetskopiering kommer nya säkerhetskopior börjar misslyckas. Igen, gamla återställningspunkterna upphör att gälla enligt principen, men en senaste återställningspunkt sparas alltid tills du stoppar säkerhetskopiering och ta bort data.
- Du kan inte stoppa säkerhetskopiering för en databas som har aktiverats för automatiskt skydd, tills automatiskt skydd har inaktiverats.

Så här stoppar du skydd för en databas:

1. I instrumentpanelen för valvet under **användning**väljer **Säkerhetskopieringsobjekt**.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. I **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Välj den databas som du vill sluta skydda.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. Databas-menyn, Välj **stoppa säkerhetskopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)


6. I **stoppa säkerhetskopiering** menyn, Välj om du vill behålla eller ta bort data. Du kan också ange en orsak och en kommentar.

    ![Menyn Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

7. Klicka på **stoppa säkerhetskopiering** .

  

### <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

Om du valde alternativet **Behåll säkerhetskopieringsdata** när skyddet för SQL-databasen stoppades kan du återuppta skyddet. Om säkerhetskopieringsdata inte behölls går det inte att återuppta skyddet.

1. Om du vill återuppta skyddet för SQL-databasen öppnar du säkerhetskopieringsobjektet och väljer **Resume backup** (Återuppta säkerhetskopiering).

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhetskopieringar på begäran:

* Fullständig säkerhetskopia
* Fullständig säkerhetskopia med endast kopiering
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

[Läs mer](backup-architecture.md#sql-server-backup-types) om SQL Server att säkerhetskopiera typer.

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server-instansen när du har inaktiverat skydd, men innan du tar bort valvet:

1. På instrumentpanelen för valvet under **hantera**väljer **infrastruktur för säkerhetskopiering**.  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)


3. I **skyddade servrar**, väljer du servern du avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern > **ta bort**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Nästa steg

[Granska](backup-sql-server-azure-troubleshoot.md) felsökningsinformation för säkerhetskopiering av SQL Server-databas.
