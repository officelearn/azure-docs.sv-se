---
title: Återställa SQL Server-databaser på en virtuell Azure-dator
description: I den här artikeln beskrivs hur du återställer SQL Server-databaser som körs på en Virtuell Azure-dator och som säkerhetskopieras med Azure Backup.
ms.topic: conceptual
ms.date: 05/22/2019
ms.openlocfilehash: 642476c98ca223da01bda5c6eb79ee9b53732468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252459"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Återställa SQL Server-databaser på virtuella Azure-datorer

I den här artikeln beskrivs hur du återställer en SQL Server-databas som körs på en virtuell Azure-dator (VM) som [Azure Backup-tjänsten](backup-overview.md) har säkerhetskopierat till ett Azure Backup Recovery Services-valv.

I den här artikeln beskrivs hur du återställer SQL Server-databaser. Mer information finns i [Säkerhetskopiera SQL Server-databaser på virtuella Azure-datorer](backup-azure-sql-database.md).

## <a name="restore-to-a-time-or-a-recovery-point"></a>Återställa till en tid eller en återställningspunkt

Azure Backup kan återställa SQL Server-databaser som körs på virtuella Azure-datorer enligt följande:

- Återställ till ett visst datum eller en viss tid (till det andra) med hjälp av säkerhetskopior av transaktionsloggen. Azure Backup bestämmer automatiskt lämplig fullständig differentiell säkerhetskopiering och kedjan av loggsäkerhetskopior som krävs för att återställa baserat på den valda tiden.
- Återställ en specifik fullständig eller differentiell säkerhetskopiering för att återställa till en viss återställningspunkt.

## <a name="prerequisites"></a>Krav

Innan du återställer en databas bör du tänka på följande:

- Du kan återställa databasen till en instans av en SQL Server i samma Azure-region.
- Målservern måste vara registrerad i samma valv som källan.
- Om du vill återställa en TDE-krypterad databas till en annan SQL Server måste du först [återställa certifikatet till målservern](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Innan du återställer "huvuddatabasen" startar du SQL Server-instansen i enanvändarläge med startalternativet **-m AzureWorkloadBackup**.
  - Värdet för **-m** är namnet på klienten.
  - Endast det angivna klientnamnet kan öppna anslutningen.
- För alla systemdatabaser (modell, master, msdb) stoppa SQL Server Agent-tjänsten innan du utlöser återställningen.
- Stäng alla program som kan försöka ansluta till någon av dessa databaser.
- Om du har flera instanser som körs på en server, alla instanser bör vara igång annars servern inte skulle visas i listan över målservrar för dig att återställa databasen till.

## <a name="restore-a-database"></a>Återställ en databas

För att återställa behöver du följande behörigheter:

- **Behörigheter för säkerhetskopieringsoperator** i valvet där du gör återställningen.
- **Deltagaråtkomst (skriva)** till källdatorn som har säkerhetskopierats.
- **Deltagaråtkomst (skriva)** till måldatorn:
  - Om du återställer till samma virtuella dator är det här källdatorn.
  - Om du återställer till en alternativ plats är det här den nya måldatorn.

Återställ enligt följande:

1. Öppna valvet där SQL Server-virtuella datorer är registrerade.
2. Välj **Säkerhetskopieringsobjekt**under Användning på instrumentpanelen **för**valvet .
3. I **Säkerhetskopieringsobjekt**under **Hantering av säkerhetskopieringstyp**väljer du SQL i Azure **VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Markera den databas som ska återställas.

    ![Välja den databas som ska återställas](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Granska databasmenyn. Den innehåller information om säkerhetskopian av databasen, inklusive:

    - De äldsta och nyaste återställningspunkterna.
    - Loggsäkerhetsstatusen för de senaste 24 timmarna för databaser som är i fullständigt och massloggat återställningsläge och som har konfigurerats för transaktionsloggsäkerheter.

6. Välj **Återställ**.

    ![Välj Återställ](./media/backup-azure-sql-database/restore-db.png)

7. I **Återställ konfiguration**anger du var (eller hur) data ska återställas:
   - **Alternativ plats**: Återställ databasen till en annan plats och behåll den ursprungliga källdatabasen.
   - **Skriv över DB:** Återställ data till samma SQL Server-instans som den ursprungliga källan. Det här alternativet skriver över den ursprungliga databasen.

    > [!IMPORTANT]
    > Om den valda databasen tillhör en alltid på tillgänglighetsgrupp tillåter SQL Server inte att databasen skrivs över. Endast **alternativ plats** är tillgänglig.
    >
   - **Återställ som filer:** I stället för att återställa som en databas återställer du de säkerhetskopieringsfiler som kan återställas som en databas senare på alla datorer där filerna finns med SQL Server Management Studio.
     ![Återställ konfigurationsmeny](./media/backup-azure-sql-database/restore-configuration.png)

### <a name="restore-to-an-alternate-location"></a>Återställa till en alternativ plats

1. Välj **Alternativ plats**under **Var du återställer**på menyn **Återställ konfiguration** .
2. Markera det SQL Server-namn och den instans som du vill återställa databasen till.
3. I rutan **Restored DB Name** (Namn på återställd databas) anger du namnet på måldatabasen.
4. Om tillämpligt väljer du **Skriv över om db med samma namn redan finns på vald SQL-instans**.
5. Välj **OK**.

    ![Ange värden för menyn Återställ konfiguration](./media/backup-azure-sql-database/restore-configuration.png)

6. I **Välj återställningspunkt**väljer du om du vill [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time) eller återställa till en viss [återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Point-in-time-återställningen är endast tillgänglig för loggsäkerhetskopior för databaser som är i fullständigt och massloggat återställningsläge.

### <a name="restore-and-overwrite"></a>Återställa och skriva över

1. Välj **Skriv över DB** > **OK**under Var **du återställer**på menyn **Återställ konfiguration** .

    ![Välja Overwrite DB (Skriv över databas)](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. I **Välj återställningspunkt**väljer du **Loggar (Tidpunkt)** för att [återställa till en viss tidpunkt](#restore-to-a-specific-point-in-time). Eller välj **Fullständig & differential** för att återställa till en viss [återställningspunkt](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Point-in-time-återställningen är endast tillgänglig för loggsäkerhetskopior för databaser som är i fullständigt och massloggat återställningsläge.

### <a name="restore-as-files"></a>Återställa som filer

Om du vill återställa säkerhetskopierade data som .bak-filer i stället för en databas väljer du **Återställ som filer**. När filerna har dumpats till en angiven sökväg kan du ta dessa filer till vilken dator som helst där du vill återställa dem som en databas. På grund av att du kan flytta dessa filer till vilken dator som helst kan du nu återställa data mellan prenumerationer och regioner.

1. Välj **Återställ som filer**under Var du **återställer**konfigurationsmenyn på menyn **Återställ konfiguration** .
2. Välj det SQL Server-namn som du vill återställa säkerhetskopiorna till.
3. I **sökvägen Till mål på servern** matas in mappsökvägen på den server som valts i steg 2. Det här är den plats där tjänsten dumpar alla nödvändiga säkerhetskopior. Vanligtvis möjliggör en sökväg för nätverksresurs eller sökväg till en monterad Azure-filresurs när den anges som målsökvägen enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs monterad på dem.<BR>

    >Om du vill återställa databassäkerhetskopieringsfilerna på en Azure File Share som är monterad på den registrerade virtuella målfilen kontrollerar du att NT AUTHORITY\SYSTEM har åtkomst till filresursen. Du kan utföra stegen nedan för att bevilja läs-/skrivbehörighet till AFS som är monterad på den virtuella datorn:
    >
    >- Kör `PsExec -s cmd` för att komma in i NT AUTHORITY\SYSTEM-skalet
    >   - Kör `cmdkey /add:<storageacct>.file.core.windows.net /user:AZURE\<storageacct> /pass:<storagekey>`
    >   - Verifiera åtkomst med`dir \\<storageacct>.file.core.windows.net\<filesharename>`
    >- Starta en återställning som filer från `\\<storageacct>.file.core.windows.net\<filesharename>` säkerhetskopieringsvalvet till sökvägen<BR>
    Du kan ladda ner Psexec via<https://docs.microsoft.com/sysinternals/downloads/psexec>

4. Välj **OK**.

    ![Välj Återställ som filer](./media/backup-azure-sql-database/restore-as-files.png)

5. Välj den **återställningspunkt** som motsvarar vilken alla tillgängliga .bak-filer ska återställas.

    ![Välj en återställningspunkt](./media/backup-azure-sql-database/restore-point.png)

6. Alla säkerhetskopieringsfiler som är associerade med den valda återställningspunkten dumpas i målsökvägen. Du kan återställa filerna som en databas på vilken dator som helst som de finns på med HJÄLP AV SQL Server Management Studio.

    ![Återställda säkerhetskopieringsfiler i målsökvägen](./media/backup-azure-sql-database/sql-backup-files.png)

### <a name="restore-to-a-specific-point-in-time"></a>Återställa till en viss tidpunkt

Om du har valt **Loggar (Point in Time)** som återställningstyp gör du följande:

1. Öppna kalendern under **Återställ datum/tid.** I kalendern visas de datum som har återställningspunkter i fetstil och det aktuella datumet markerat.
1. Välj ett datum med återställningspunkter. Du kan inte välja datum som inte har några återställningspunkter.

    ![Öppna kalendern](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. När du har valt ett datum visar tidslinjegrafen tillgängliga återställningspunkter i ett kontinuerligt intervall.
1. Ange en tid för återställningen i tidslinjediagrammet eller välj en tid. Välj sedan **OK**.

    ![Välj en återställningstid](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

1. Om du vill behålla databasen som inte fungerar efter återställningen på menyn **Avancerad konfiguration** aktiverar du Återställ **med NORECOVERY**.
1. Om du vill ändra återställningsplatsen på målservern anger du en ny målsökväg.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra återställningsstatusen i området **Meddelanden** eller spåra den genom att välja **Återställ jobb** på databasmenyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>Återställa till en viss återställningspunkt

Om du har valt **Fullständig & Differential** som återställningstyp gör du följande:

1. Välj en återställningspunkt i listan och välj **OK** för att slutföra återställningspunktsproceduren.

    ![Välja en fullständig återställningspunkt](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

    >[!NOTE]
    > Som standard visas återställningspunkter från de senaste 30 dagarna. Du kan visa återställningspunkter som är äldre än 30 dagar genom att klicka på **Filter** och välja ett anpassat intervall.

1. Om du vill behålla databasen som inte fungerar efter återställningen på menyn **Avancerad konfiguration** aktiverar du Återställ **med NORECOVERY**.
1. Om du vill ändra återställningsplatsen på målservern anger du en ny målsökväg.
1. Välj **OK**.

    ![Menyn Avancerad konfiguration](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. På menyn **Återställ** väljer du **Återställ** för att starta återställningsjobbet.
1. Spåra återställningsstatusen i området **Meddelanden** eller spåra den genom att välja **Återställ jobb** på databasmenyn.

    ![Förlopp för återställningsjobb](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>Återställa databaser med ett stort antal filer

Om den totala strängstorleken för filer i en databas är större än en [viss gräns](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)lagrar Azure Backup listan över databasfiler i en annan gropkomponent så att du inte kan ange sökvägen till målåterställning under återställningen. Filerna återställs till SQL-standardsökvägen i stället.

  ![Återställa databas med stor fil](./media/backup-azure-sql-database/restore-large-files.jpg)

## <a name="next-steps"></a>Nästa steg

[Hantera och övervaka](manage-monitor-sql-database-backup.md) SQL Server-databaser som säkerhetskopieras av Azure Backup.
