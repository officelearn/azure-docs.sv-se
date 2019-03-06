---
title: Hantera och övervaka SQL Server-databaser på en Azure virtuell dator som backas upp av Azure Backup | Microsoft Docs
description: Den här artikeln beskriver hur du återställer SQL Server-databaser som körs på en virtuell Azure-dator och som backas upp av Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: da4264047830b21b3ac4dae723dd1fd2f9d7a8f4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432863"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser 


Den här artikeln beskrivs vanliga uppgifter för hantering och övervakning av SQL Server-databaser som körs på en Azure-dator (VM) och som ska säkerhetskopieras till en Azure Backup Recovery Services-valvet genom den [Azure Backup](backup-overview.md) service. Du lär dig att övervaka jobb och aviseringar, stoppa och återuppta databasskyddet, köra säkerhetskopieringsjobb och avregistrera en virtuell dator från säkerhetskopior.


> [!NOTE]
> Säkerhetskopiering av SQL Server-databaser som körs på en virtuell Azure-dator med Azure Backup är för närvarande i offentlig förhandsversion.


Om du inte har konfigurerat säkerhetskopiering för SQL Server-databaser, se [säkerhetskopiera SQL Server-databaser på Azure Virtual Machines](backup-azure-sql-database.md)

##  <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuell säkerhetskopieringsjobb i portalen

Azure Backup visar alla manuellt utlösta jobb i den **säkerhetskopieringsjobb** portal. De jobb som du ser i den här portalen inkludera databasidentifiering och registrera och säkerhetskopiering och återställning.

![Backup jobb-portalen](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Den **säkerhetskopieringsjobb** portalen inte visar schemalagda säkerhetskopieringsjobb. Använd SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

## <a name="monitor-scheduled-backup-jobs-in-sql-server-management-studio"></a>Övervaka schemalagda säkerhetskopieringsjobb i SQL Server Management Studio 

Azure Backup använder SQL-specifika API:er för alla säkerhetskopieringsåtgärder. Använd specifika API:er för att hämta all jobbinformation från [SQL backupset-tabellen](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) i msdb-databasen.

I följande exempel är en fråga som hämtar alla säkerhetskopieringsjobb för en databas som heter **DB1**. Anpassa frågan för avancerad övervakning.

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

Eftersom loggsäkerhetskopior sker var 15: e minut, kan det vara tråkigt att övervaka säkerhetskopieringsjobb. Azure Backup förenklar övervakning genom att skicka e-postaviseringar. E-postaviseringar finns:

- Utlöses för alla Säkerhetskopieringsfel.
- Konsoliderade på databasnivå av felkoder.
- Skickas endast för en databas första säkerhetskopieringen har misslyckats. 

Övervaka aviseringar om säkerhetskopiering av databasen:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. På instrumentpanelen för valvet väljer **aviseringar och händelser**.

   ![Välja aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

1. I **aviseringar och händelser**väljer **säkerhetskopiering aviseringar**.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan stoppa säkerhetskopiering av SQL Server-databasen i ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb och lämna kvar återställningspunkterna.

Om du väljer att lämna återställningspunkterna, Tänk på följande information:

* Alla återställningspunkter som du lämnar rensas enligt principen för säkerhetskopiering. 
* Tills alla återställningspunkter rensas, debiteras du för den skyddade instansen och förbrukad lagring. Mer information finns i [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Azure Backup behåller alltid en senaste återställningspunkt tills du tar bort säkerhetskopierade data. 
* Om du tar bort en datakälla utan att stoppa säkerhetskopiering misslyckas nya säkerhetskopior. 
* Om din databas har aktiverats för autoprotection, kan du stoppa säkerhetskopiering såvida du inte inaktiverar autoprotection.

Så här stoppar du skydd för en databas:

1. På instrumentpanelen för valvet under **användning**väljer **Säkerhetskopieringsobjekt**.

1. Under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)


1. Välj den databas som du vill sluta skydda.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


1. Databas-menyn, Välj **stoppa säkerhetskopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)


1. På den **stoppa säkerhetskopiering** menyn, Välj om du vill behålla eller ta bort data. Om du vill ange en orsak och en kommentar.

    ![Behålla eller ta bort data från menyn stoppa säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

1. Välj **stoppa säkerhetskopiering**.

  

## <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

När du stoppar skydd för SQL-databasen, om du väljer den **Behåll säkerhetskopieringsdata** alternativet kan du senare kan återuppta skyddet. Om du inte behåller säkerhetskopierade data, kan inte du återuppta skyddet.

Att återuppta skyddet av en SQL-databas:

1. Öppna säkerhetskopieringsobjektet och välj **återuppta säkerhetskopiering**.

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhetskopieringar på begäran:

* Fullständig säkerhetskopia
* Fullständig säkerhetskopia med endast kopiering
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

Mer information finns i [SQL Server-säkerhetskopieringstyper](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server-instansen när du inaktiverar skyddet men innan du tar bort valvet:

1. På instrumentpanelen för valvet under **hantera**väljer **infrastruktur för säkerhetskopiering**.  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)


3. I **skyddade servrar**, väljer du servern du avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern och välj **ta bort**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Nästa steg

Mer information finns i [felsöka säkerhetskopieringar på en SQL Server-databas](backup-sql-server-azure-troubleshoot.md).
