---
title: Återställ säkerhetskopierade SQL Server-databaser på en virtuell Azure-dator med Azure Backup | Microsoft Docs
description: Den här artikeln beskrivs hur du återställer SQL Server-databaser som körs på en virtuell Azure-dator som säkerhetskopieras med Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445448"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Återställa SQL Server-databaser på Azure Virtual Machines 


Den här artikeln beskrivs hur du återställer en SQL Server-databas som körs på en Azure virtuell dator som har säkerhetskopierats till ett Azure Backup Recovery Services-valv med den [Azure Backup](backup-overview.md) service.


> [!NOTE]
> Säkerhetskopiering av SQl Server-databaser som körs på en virtuell Azure-dator med Azure Backup är för närvarande i offentlig förhandsversion.


Den här artikeln beskriver hur du återställer SQL Server-databaser. Om du inte har konfigurerat säkerhetskopiering för databaser, följer du anvisningarna i [i den här artikeln](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>Om hur du återställer databaser

Azure Backup kan återställa SQL Server-databaser som körs på virtuella Azure-datorer på följande sätt:

- Återställa till ett visst datum eller tid (till andra) med hjälp av säkerhetskopieringar av transaktionsloggen. Azure Backup anger automatiskt den lämpliga fullständig differentiella säkerhetskopieringen och kedja av loggsäkerhetskopior som krävs för att återställa baserat på den valda tid.
- Återställa en fullständig eller Differentiell säkerhetskopia att återställa till en specifik återställningspunkt i stället för en viss tid.


### <a name="prerequisites"></a>Förutsättningar

Tänk på följande innan du återställer en databas:

- Du kan återställa databasen till en instans av en SQL Server i samma Azure-region.
- Målservern måste vara registrerad på samma valv som källa.
- Om du vill återställa en krypterad TDE-databas till en annan SQL Server, måste du först [återställa certifikatet till målservern](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Innan du utlöser en återställning av ”master”-databasen, starta SQL Server-instansen i enanvändarläge med startalternativ **-m AzureWorkloadBackup**.
    - Värdet för **-m** är namnet på klienten.
    - Det angivna klientnamnet tillåts att öppna anslutningen.
- För alla systemdatabaser (model, master, msdb) ska du stoppa SQL Agent-tjänsten innan du utlöser återställningen.
- Stäng alla program som kan försöka stjäla en anslutning till någon av dessa databaser.

## <a name="restore-a-database"></a>Återställ en databas

Så här återställer du behöver följande behörigheter:

* **Säkerhetskopiera operatorn** behörigheter i valvet den som du gör återställningen.
* **Deltagare (skriva)** åtkomst till källan VM som säkerhetskopieras.
* **Deltagare (skriva)** åtkomst till den Virtuella måldatorn:
    - Om du återställa till samma virtuella dator kommer detta att den Virtuella källdatorn.
    - Om du återställa till en annan plats kommer detta att den nya Virtuella måldatorn. 

Återställa på följande sätt:
1. Öppna det valv som SQL Server-dator är registrerad.
2. På instrumentpanelen för valvet under **användning**väljer **Säkerhetskopieringsobjekt**.

    ![Öppna menyn Säkerhetskopieringsobjekt](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. I **Säkerhetskopieringsobjekt**under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Välj databas för att återställa.

    ![Välja den databas som ska återställas](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Granska databas-menyn. Hittar du information om säkerhetskopian av databasen, inklusive: 

    * De äldsta och nyaste återställningspunkterna.
    * Logga Säkerhetskopieringsstatus för de senaste 24 timmarna för databaser i fullständiga och massloggade återställningsläge, om konfigurerad för transaktionell loggsäkerhetskopior.

6. Klicka på **Återställ databasen**. 

    ![Välj Återställ databas](./media/backup-azure-sql-database/restore-db-button.png)

7. I **återställa konfigurationen**, ange var du vill återställa data till:
    - **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga källdatabasen.
    - **Overwrite DB** (Skriv över databas): Återställ data till samma SQL Server-instans som den ursprungliga källan. Det här alternativet gör så att den ursprungliga databasen skrivs över.

    > [!Important]
    > Om den valda databasen tillhör en AlwaysOn-tillgänglighetsgrupp tillåter inte SQL Server att databasen skrivs över. Endast **alternativ plats** är tillgänglig.
    >

    ![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. I den **återställa konfigurationen** menyn under **var du vill återställa**väljer **alternativ plats**.
2. Välj den SQL-servernamn och instans som du vill återställa databasen.
3. I **återställt databasnamn** anger du namnet på måldatabasen.
4. Om så är tillämpligt, Välj **Åsidosätt om databasen med samma namn finns redan på den valda SQL-instansen**.
5. Klicka på **OK**.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. I **: Välj återställningspunkt**väljer om du vill [återställa till en specifik tidpunkt](#restore-to-a-specific-point-in-time), eller att återställa till en [specifik återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Point-in-time-återställning finns tillgängligt endast för säkerhetskopieringar för databaser med fullständiga och massloggade återställningsläge. 


### <a name="restore-and-overwrite"></a>Återställa och skriva över

1. I den **återställa konfigurationen** menyn under **var du vill återställa**väljer **skriva över DB** > **OK**.

    ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. I **: Välj återställningspunkt**, Välj ** loggar (tidpunkt) att [återställa till en specifik tidpunkt](#restore-to-a-specific-point-in-time), eller **fullständig och differentiell** att återställa till en [specifika återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Tidpunktsåterställning är endast tillgängligt för loggsäkerhetskopior för databaser med en fullständig och bulkloggad återställningsmodell. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Återställa till en specifik tidpunkt

Om du har valt **loggar (tidpunkt)** som typ av återställning gör du följande:

1.  Under **återställa datum/tid**, Välj mini-kalender. På den **kalender**datumen i fetstil har återställningspunkter och det aktuella datumet är markerat.
2. Välj ett datum med återställningspunkter. Datum utan återställningspunkter kan inte väljas.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.
4. Ange en tid för återställning med hjälp av tidslinje graph eller välj en tid. Klicka sedan på **OK**.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. På den **Advanced Configuration** -menyn om du vill behålla databasen inte kan användas efter återställningen, aktivera **Återställ med NORECOVERY**.
5. Om du vill ändra återställningsplatsen på målservern, anger du en ny målsökväg som.
6. Klicka på **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
8. Spåra förloppet för återställning i den **meddelanden** området eller välj **återställningsjobb** på databas-menyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Återställa till en specifik återställningspunkt

Om du har valt **fullständig och differentiell** som typ av återställning gör du följande:


1. Välj en återställningspunkt i listan och klicka på **OK** Slutför återställningspunkt proceduren.

    ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. På den **Advanced Configuration** -menyn om du vill behålla databasen inte kan användas efter återställningen, aktivera **Återställ med NORECOVERY**.
3. Om du vill ändra återställningsplatsen på målservern, anger du en ny målsökväg som. 
4. Klicka på **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
8. Spåra förloppet för återställning i den **meddelanden** området eller välj **återställningsjobb** på databas-menyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Nästa steg

[Hantera och övervaka](manage-monitor-sql-database-backup.md) SQL Server-databaser som säkerhetskopieras av Azure Backup.
