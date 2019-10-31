---
title: Använd Azure Backup för att återställa SQL Server-databaser på en virtuell Azure-dator
description: Den här artikeln beskriver hur du återställer SQL Server databaser som körs på en virtuell Azure-dator och som säkerhets kopie ras med Azure Backup.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: dacurwin
ms.openlocfilehash: 648c5ca1eb1cb1c0f1832654fc66d436b9318af3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161871"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Återställa SQL Server databaser på virtuella Azure-datorer

Den här artikeln beskriver hur du återställer en SQL Server databas som körs på en virtuell Azure-dator (VM) som tjänsten [Azure Backup](backup-overview.md) har säkerhetskopierat till ett Azure Backup Recovery Services-valv.

Den här artikeln beskriver hur du återställer SQL Server-databaser. Mer information finns i [säkerhetskopiera SQL Server databaser på virtuella Azure-datorer](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Återställa till en tid eller en återställnings punkt

Azure Backup kan återställa SQL Server databaser som körs på virtuella Azure-datorer på följande sätt:

- Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av säkerhets kopior av transaktions loggen. Azure Backup identifierar automatiskt rätt fullständig differentiell säkerhets kopiering och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.
- Återställ en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

## <a name="prerequisites"></a>Krav

Observera följande innan du återställer en databas:

- Du kan återställa databasen till en instans av en SQL Server i samma Azure-region.
- Mål servern måste vara registrerad på samma valv som källan.
- Om du vill återställa en TDE-krypterad databas till en annan SQL Server måste du först [återställa certifikatet till mål servern](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Innan du återställer Master-databasen startar du SQL Server-instansen i enanvändarläge genom att använda Start alternativet **-m AzureWorkloadBackup**.
  - Värdet för **-m** är namnet på klienten.
  - Det är bara det angivna klient namnet som kan öppna anslutningen.
- Stoppa tjänsten SQL Server Agent innan du utlöser återställningen för alla system databaser (modell, Master, msdb).
- Stäng alla program som kan försöka ansluta till någon av dessa databaser.
- Om du har flera instanser som körs på en server, bör alla instanser vara igång och köra på annat sätt, så visas inte servern i listan över mål servrar som du kan återställa databasen till.

## <a name="restore-a-database"></a>Återställ en databas

För att återställa måste du ha följande behörigheter:

- Behörighet för **säkerhets kopierings ansvarig** i valvet där du utför återställningen.
- **Deltagar** åtkomst till den virtuella käll datorn som säkerhets kopie ras.
- **Deltagar** åtkomst till den virtuella mål datorn:
  - Om du återställer till samma virtuella dator är det här den virtuella käll datorn.
  - Om du återställer till en annan plats är detta den nya virtuella mål datorn.

Återställ enligt följande:

1. Öppna valvet där SQL Server VM är registrerat.
2. På instrument panelen för valvet under **användning**väljer du **säkerhets kopierings objekt**.
3. I **säkerhets kopierings objekt**, under **säkerhets kopierings hanterings typ**, väljer du **SQL i virtuell Azure-dator**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Välj den databas som ska återställas.

    ![Välja den databas som ska återställas](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Granska menyn databas. Den innehåller information om säkerhets kopian av databasen, inklusive:

    - De äldsta och nyaste återställningspunkterna.
    - Status för logg säkerhets kopiering för de senaste 24 timmarna för databaser som är i fullständigt och Mass återställnings läge och som har kon figurer ATS för säkerhets kopiering av transaktions loggar.

6. Välj **Återställ**.

    ![Välj Återställ](./media/backup-azure-sql-database/restore-db.png)

7. I **Återställ konfiguration**anger du var (eller hur) du vill återställa data:
   - **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga käll databasen.
   - **Skriv över DB**: Återställ data till samma SQL Server instans som den ursprungliga källan. Med det här alternativet skrivs den ursprungliga databasen över.

           > [!IMPORTANT]
           > If the selected database belongs to an Always On availability group, SQL Server doesn't allow the database to be overwritten. Only **Alternate Location** is available.
           >
   - **Återställ som filer**: i stället för att återställa som en databas återställer du de säkerhetskopierade filer som kan återställas som en databas senare på alla datorer där filerna finns med hjälp av SQL Server Management Studio.
     ![återställa konfigurations menyn](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. I menyn **Återställ konfiguration** , under **återställnings**läge, väljer du **alternativ plats**.
2. Välj det SQL Server namn och den instans som du vill återställa databasen till.
3. I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.
4. Om det är tillämpligt väljer **du Skriv över om databasen med samma namn redan finns på den valda SQL-instansen**.
5. Välj **OK**.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration.png)

6. I **Välj återställnings punkt**väljer du om du vill [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time) eller [återställa till en viss återställnings punkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Återställning vid tidpunkt är bara tillgängligt för säkerhets kopiering av loggar för databaser som är fullständiga och Mass loggade återställnings läge.

### <a name="restore-and-overwrite"></a>Återställ och skriv över

1. I menyn **Återställ konfiguration** , under **återställnings**-menyn, väljer du **Skriv över DB** > **OK**.

    ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att återställa till en [viss återställnings punkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Återställning vid tidpunkt är bara tillgängligt för säkerhets kopiering av loggar för databaser som är fullständiga och Mass loggade återställnings läge.

### <a name="restore-as-files"></a>Återställ som filer

Om du vill återställa säkerhets kopierings data som. bak-filer i stället för en databas väljer du **Återställ som filer**. När filerna har dumpas till en angiven sökväg kan du ta dessa filer till alla datorer där du vill återställa dem som en databas. Genom att kunna flytta filerna runt till en dator kan du nu återställa data mellan prenumerationer och regioner.

1. I menyn **Återställ konfiguration** , under **var du vill återställa**, väljer du **Återställ som filer**.
2. Välj det SQL Server namn som du vill återställa säkerhetskopieringsfilerna till.
3. I **mål Sök vägen på servern** ange mappsökvägen på den server som valdes i steg 2. Detta är den plats där tjänsten kommer att dumpa alla nödvändiga säkerhetskopieringsfiler. Normalt möjliggör en nätverks resurs Sök väg eller sökväg till en monterad Azure-filresurs när den anges som mål Sök väg, vilket ger enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs som är monterad på dem.
4. Välj **OK**.

![Välj Återställ som filer](./media/backup-azure-sql-database/restore-as-files.png)

5. Välj den **återställnings punkt** som motsvarar alla tillgängliga. bak-filer som ska återställas.

![Välj en återställnings punkt](./media/backup-azure-sql-database/restore-point.png)

6. Alla säkerhetskopierade filer som är associerade med den valda återställnings punkten dumpas till mål Sök vägen. Du kan återställa filerna som en databas på alla datorer som de finns i med hjälp av SQL Server Management Studio.

![Återställda säkerhetskopieringsfiler i mål Sök vägen](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Återställ till en viss tidpunkt

Om du har valt **loggar (tidpunkt)** som återställnings typ gör du följande:

1. Öppna kalendern under **Återställ datum/tid**. I kalendern visas de datum som har återställnings punkter i fet stil och aktuellt datum är markerat.
1. Välj ett datum som har återställnings punkter. Det går inte att välja datum som inte har några återställnings punkter.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.
1. Ange en tid för återställningen i tids linje diagrammet eller Välj en tid. Välj sedan **OK**.

    ![Välj en återställnings tid](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. I menyn **Avancerad konfiguration** , om du vill behålla databasen utan åtgärd efter återställningen, aktiverar du **återställning med NORECOVERY**.
1. Ange en ny mål Sök väg om du vill ändra återställnings platsen på mål servern.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra återställnings förloppet i **aviserings** området eller spåra det genom att välja **återställnings jobb** på menyn databas.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Återställa till en viss återställnings punkt

Om du har valt **fullständig & differentiell** som återställnings typ gör du följande:

1. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

    ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. I menyn **Avancerad konfiguration** , om du vill behålla databasen utan åtgärd efter återställningen, aktiverar du **återställning med NORECOVERY**.
1. Ange en ny mål Sök väg om du vill ändra återställnings platsen på mål servern.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra återställnings förloppet i **aviserings** området eller spåra det genom att välja **återställnings jobb** på menyn databas.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Återställa databaser med ett stort antal filer

Om den totala sträng storleken på filer i en databas är större än en [viss gräns](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), lagrar Azure Backup listan över databasfiler i en annan Pit-komponent, så att du inte kan ange mål återställnings Sök vägen under återställnings åtgärden. Filerna kommer att återställas till standard Sök vägen för SQL i stället.

  ![Återställ databasen med stor fil](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Nästa steg

[Hantera och övervaka](manage-monitor-sql-database-backup.md) SQL Server databaser som säkerhets kopie ras av Azure Backup.
