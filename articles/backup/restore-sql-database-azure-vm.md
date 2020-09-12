---
title: Återställa SQL Server-databaser på en virtuell Azure-dator
description: Den här artikeln beskriver hur du återställer SQL Server databaser som körs på en virtuell Azure-dator och som säkerhets kopie ras med Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: afb3ef7ac1d161c073ef715a9f7b1ec83bd8410a
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89377989"
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
- Om du vill återställa en TDE-krypterad databas till en annan SQL Server måste du först [återställa certifikatet till mål servern](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server).
- [CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-ver15) -aktiverade databaser ska återställas med alternativet [Återställ som filer](#restore-as-files) .
- Innan du återställer Master-databasen startar du SQL Server-instansen i enanvändarläge genom att använda Start alternativet **-m AzureWorkloadBackup**.
  - Värdet för **-m** är namnet på klienten.
  - Det är bara det angivna klient namnet som kan öppna anslutningen.
- Stoppa tjänsten SQL Server Agent innan du utlöser återställningen för alla system databaser (modell, Master, msdb).
- Stäng alla program som kan försöka ansluta till någon av dessa databaser.
- Om du har flera instanser som körs på en server, ska alla instanser vara igång och köra på annat sätt, så visas inte servern i listan över mål servrar som du kan återställa databasen till.

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
        > Om den valda databasen tillhör en tillgänglighets grupp som alltid är aktive rad tillåter SQL Server inte att databasen skrivs över. Endast **alternativ plats** är tillgänglig.
        >
   - **Återställ som filer**: i stället för att återställa som en databas återställer du de säkerhetskopierade filer som kan återställas som en databas senare på alla datorer där filerna finns med hjälp av SQL Server Management Studio.
     ![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. I menyn **Återställ konfiguration** , under **återställnings**läge, väljer du **alternativ plats**.
1. Välj det SQL Server namn och den instans som du vill återställa databasen till.
1. I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.
1. Om det är tillämpligt väljer **du Skriv över om databasen med samma namn redan finns på den valda SQL-instansen**.
1. Välj **återställnings punkt**och välj om du vill [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time) eller om du vill [återställa till en viss återställnings punkt](#restore-to-a-specific-restore-point).

    ![Välj återställnings punkt](./media/backup-azure-sql-database/select-restore-point.png)

    ![Återställ till tidpunkt](./media/backup-azure-sql-database/restore-to-point-in-time.png)

1. På menyn **Avancerad konfiguration**:

    - Om du inte vill att databasen ska vara i drift efter återställningen aktiverar du **återställning med NORECOVERY**.
    - Ange nya mål Sök vägar om du vill ändra återställnings platsen på mål servern.

        ![Ange mål Sök vägar](./media/backup-azure-sql-database/target-paths.png)

1. Välj **OK** för att utlösa återställningen. Spåra återställnings förloppet i **aviserings** området eller spåra det under vyn **säkerhets kopierings jobb** i valvet.

    > [!NOTE]
    > Återställning vid tidpunkt är bara tillgängligt för säkerhets kopiering av loggar för databaser som är fullständiga och Mass loggade återställnings läge.

### <a name="restore-and-overwrite"></a>Återställ och skriv över

1. I menyn **Återställ konfiguration** , under **återställningen**, väljer du **Skriv över DB**  >  **OK**.

    ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. I **Välj återställnings punkt**väljer du **loggar (tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller Välj **fullständig & differentiell** för att återställa till en [viss återställnings punkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Återställning vid tidpunkt är bara tillgängligt för säkerhets kopiering av loggar för databaser som är fullständiga och Mass loggade återställnings läge.

### <a name="restore-as-files"></a>Återställ som filer

Om du vill återställa säkerhets kopierings data som. bak-filer i stället för en databas väljer du **Återställ som filer**. När filerna har dumpas till en angiven sökväg kan du ta dessa filer till alla datorer där du vill återställa dem som en databas. Eftersom du kan flytta dessa filer runt till en dator kan du nu återställa data mellan prenumerationer och regioner.

1. Under **var och hur du ska återställa**, väljer du **Återställ som filer**.
1. Välj det SQL Server namn som du vill återställa säkerhetskopieringsfilerna till.
1. I **mål Sök vägen på servern** ange mappsökvägen på den server som valdes i steg 2. Detta är den plats där tjänsten kommer att dumpa alla nödvändiga säkerhetskopieringsfiler. Normalt möjliggör en nätverks resurs Sök väg eller sökväg till en monterad Azure-filresurs när den anges som mål Sök väg, vilket ger enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs som är monterad på dem.<BR>

    >Om du vill återställa säkerhets kopian av databasen på en Azure-filresurs som är monterad på den registrerade virtuella mål datorn, kontrollerar du att NT instans\system har åtkomst till fil resursen. Du kan utföra stegen nedan för att ge Läs-/Skriv behörighet till AFS som är monterad på den virtuella datorn:
    >
    >- Kör `PsExec -s cmd` för att ange i NT instans\system-gränssnittet
    >   - Kör `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verifiera åtkomst med `dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Starta en återställning som filer från säkerhets kopierings valvet till `\\<storageacct>.file.core.windows.net\<filesharename>` som sökväg<BR>
    Du kan ladda ned PsExec från [Sysinternals](/sysinternals/downloads/psexec) -sidan.

1. Välj **OK**.

    ![Välj Återställ som filer](./media/backup-azure-sql-database/restore-as-files.png)

1. Välj **återställnings punkt**och välj om du vill [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time) eller om du vill [återställa till en viss återställnings punkt](#restore-to-a-specific-restore-point).

1. Alla säkerhetskopierade filer som är associerade med den valda återställnings punkten dumpas till mål Sök vägen. Du kan återställa filerna som en databas på vilken dator som helst som de är tillgängliga på med hjälp av SQL Server Management Studio.

    ![Återställda säkerhetskopieringsfiler i mål Sök vägen](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Återställ till en viss tidpunkt

Om du har valt **loggar (tidpunkt)** som återställnings typ gör du följande:

1. Öppna kalendern under **Återställ datum/tid**. I kalendern visas de datum som har återställnings punkter i fet stil och aktuellt datum är markerat.
1. Välj ett datum som har återställnings punkter. Det går inte att välja datum som inte har några återställnings punkter.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.
1. Ange en tid för återställningen i tids linje diagrammet eller Välj en tid. Välj sedan **OK**.

### <a name="restore-to-a-specific-restore-point"></a>Återställa till en viss återställnings punkt

Om du har valt **fullständig & differentiell** som återställnings typ gör du följande:

1. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

    ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-full-recovery-point.png)

    >[!NOTE]
    > Återställnings punkter från de senaste 30 dagarna visas som standard. Du kan visa återställnings punkter som är äldre än 30 dagar genom att välja **filter** och välja ett anpassat intervall.

### <a name="restore-databases-with-large-number-of-files"></a>Återställa databaser med ett stort antal filer

Om den totala sträng storleken för filer i en databas är större än en [viss gräns](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure Backup lagra listan över databasfiler i en annan depå komponent så att du inte kan ange sökvägen till mål återställningen under återställningen. Filerna kommer att återställas till standard Sök vägen för SQL i stället.

  ![Återställ databasen med stor fil](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Nästa steg

[Hantera och övervaka](manage-monitor-sql-database-backup.md) SQL Server databaser som säkerhets kopie ras av Azure Backup.
