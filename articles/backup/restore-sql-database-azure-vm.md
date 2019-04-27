---
title: Använda Azure Backup för att återställa säkerhetskopierade SQL Server-databaser på en virtuell Azure-dator | Microsoft Docs
description: Den här artikeln beskriver hur du återställer SQL Server-databaser som körs på en virtuell Azure-dator och som ska säkerhetskopieras med Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: raynew
ms.openlocfilehash: 1712e46494796e563c26316b4f45d968872c304f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781825"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Återställa SQL Server-databaser på Azure Virtual Machines

Den här artikeln beskrivs hur du återställer en SQL Server-databas som körs på en Azure-dator (VM) som den [Azure Backup](backup-overview.md) service har säkerhetskopierats till ett Azure Backup Recovery Services-valv.

Den här artikeln beskriver hur du återställer SQL Server-databaser. Mer information finns i [säkerhetskopiera SQL Server-databaser på Azure Virtual Machines](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Återställ till en tid eller en återställningspunkt

Azure Backup kan återställa SQL Server-databaser som körs på virtuella Azure-datorer på följande sätt:

- Återställa till ett visst datum eller tid (till andra) med hjälp av säkerhetskopieringar av transaktionsloggen. Azure Backup anger automatiskt den lämpliga fullständig differentiella säkerhetskopieringen och kedja av säkerhetskopior som krävs för att återställa baserat på den valda tid.
- Återställa en fullständig eller Differentiell säkerhetskopia att återställa till en specifik återställningspunkt.


## <a name="prerequisites"></a>Nödvändiga komponenter

Tänk på följande innan du återställer en databas:

- Du kan återställa databasen till en instans av en SQL Server i samma Azure-region.
- Målservern måste vara registrerad på samma valv som källa.
- Om du vill återställa en TDE-krypterad databas till en annan SQL Server, måste du först [återställa certifikatet till målservern](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Innan du återställer databasen ”huvud” starta SQL Server-instansen i enanvändarläge med hjälp av startalternativet **-m AzureWorkloadBackup**.
    - Värdet för **-m** är namnet på klienten.
    - Det angivna klientnamnet kan öppna anslutningen.
- Stoppa SQL Server Agent-tjänsten för alla systemdatabaser (modell, master, msdb,) innan du utlöser återställningen.
- Stäng alla program som kan försöka ta en anslutning till någon av dessa databaser.

## <a name="restore-a-database"></a>Återställ en databas

Om du vill återställa, behöver du följande behörigheter:

* **Säkerhetskopiera operatorn** behörigheter i valvet där du gör återställningen.
* **Deltagare (skriva)** åtkomst till källan VM som säkerhetskopieras.
* **Deltagare (skriva)** åtkomst till den Virtuella måldatorn:
    - Om du återställa till samma virtuella dator, är det här den Virtuella källdatorn.
    - Om du återställa till en annan plats, är det här den nya Virtuella måldatorn.

Återställa på följande sätt:
1. Öppna det valv som SQL Server-dator är registrerad.
2. På instrumentpanelen för valvet under **användning**väljer **Säkerhetskopieringsobjekt**.
3. I **Säkerhetskopieringsobjekt**under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Välj databas för att återställa.

    ![Välja den databas som ska återställas](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Granska databas-menyn. Hittar du information om säkerhetskopian av databasen, inklusive:

    * De äldsta och nyaste återställningspunkterna.
    * Log Säkerhetskopieringsstatus för de senaste 24 timmarna för databaser som finns i fullständiga och massloggade återställningsläge och som har konfigurerats för transaktionell loggsäkerhetskopior.

6. Välj **Återställ databasen**.

    ![Välj Återställ databas](./media/backup-azure-sql-database/restore-db-button.png)

7. I **återställa konfigurationen**, ange var du vill återställa data:
   - **Alternativ plats**: Återställa databasen till en annan plats och behålla den ursprungliga källdatabasen.
   - **Overwrite DB** (Skriv över databas): Återställ data till samma SQL Server-instans som den ursprungliga källan. Det här alternativet skriver över den ursprungliga databasen.

     > [!Important]
     > Om den valda databasen tillhör en Always On-tillgänglighetsgrupp, tillåter inte SQL Server att databasen kan skrivas över. Endast **alternativ plats** är tillgänglig.
     >

     ![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. I den **återställa konfigurationen** menyn under **var du vill återställa**väljer **alternativ plats**.
2. Välj den SQL-servernamn och instans som du vill återställa databasen.
3. I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.
4. Om så är tillämpligt, Välj **Åsidosätt om databasen med samma namn finns redan på den valda SQL-instansen**.
5. Välj **OK**.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. I **: Välj återställningspunkt**väljer om du vill [återställa till en specifik tidpunkt](#restore-to-a-specific-point-in-time) eller [återställa till en specifik återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Point-in-time-återställning finns tillgängligt endast för säkerhetskopieringar för databaser som finns i fullständiga och massloggade återställningsläge.

### <a name="restore-and-overwrite"></a>Återställa och skriva över

1. I den **återställa konfigurationen** menyn under **var du vill återställa**väljer **skriva över DB** > **OK**.

    ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. I **: Välj återställningspunkt**väljer **loggar (tidpunkt)** till [återställa till en specifik tidpunkt](#restore-to-a-specific-point-in-time). Eller välj **fullständig och differentiell** att återställa till en [specifik återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Point-in-time-återställning finns tillgängligt endast för säkerhetskopieringar för databaser som finns i fullständiga och massloggade återställningsläge.

### <a name="restore-to-a-specific-point-in-time"></a>Återställa till en specifik tidpunkt

Om du har valt **loggar (tidpunkt)** som typ av återställning gör du följande:

1.  Under **återställa datum/tid**, öppna kalendern. Datum som har återställningspunkter visas i fetstil i kalendern och det aktuella datumet är markerat.
1. Välj ett datum som har återställningspunkter. Du kan inte välja datum som har inga återställningspunkter.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.
1. Ange en tid för återställningen på tidslinjen diagrammet eller välj en tid. Välj sedan **OK**.

    ![Välj en återställningstid](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. På den **Advanced Configuration** -menyn om du vill behålla databasen fungerar inte efter återställningen, aktivera **Återställ med NORECOVERY**.
1. Om du vill ändra återställningsplatsen på målservern, anger du en ny målsökväg som.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra förloppet för återställning i den **meddelanden** området eller spåra genom att välja **återställningsjobb** på databas-menyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Återställa till en specifik återställningspunkt

Om du har valt **fullständig och differentiell** som typ av återställning gör du följande:

1. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

    ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. På den **Advanced Configuration** -menyn om du vill behålla databasen fungerar inte efter återställningen, aktivera **Återställ med NORECOVERY**.
1. Om du vill ändra återställningsplatsen på målservern, anger du en ny målsökväg som.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra förloppet för återställning i den **meddelanden** området eller spåra genom att välja **återställningsjobb** på databas-menyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Nästa steg

[Hantera och övervaka](manage-monitor-sql-database-backup.md) SQL Server-databaser som säkerhetskopieras av Azure Backup.
