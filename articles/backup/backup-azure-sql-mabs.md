---
title: Säkerhetskopiera SQL Server med hjälp av Azure Backup Server
description: I den här artikeln lär du dig konfigurationen för att säkerhetskopiera SQL Server-databaser med hjälp av Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 4a4d4b7e70e2df0e014ea4b4d23027aa7c48f2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505942"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Säkerhetskopiera SQL Server till Azure med hjälp av Azure Backup Server

Den här artikeln hjälper dig att konfigurera säkerhetskopior av SQL Server-databaser med hjälp av Microsoft Azure Backup Server (MABS).

Så här säkerhetskopierar du en SQL Server-databas och återställer den från Azure:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser i Azure.
1. Skapa säkerhetskopior på begäran i Azure.
1. Återställa databasen i Azure.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du se till att du har [installerat och förberett Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy 

Om du vill skydda SQL Server-databaser i Azure skapar du först en princip för säkerhetskopiering:

1. Välj arbetsytan **Skydd** i Azure Backup Server.
1. Välj **Ny** om du vill skapa en skyddsgrupp.

    ![Skapa en skyddsgrupp i Azure Backup Server](./media/backup-azure-backup-sql/protection-group.png)
1. På startsidan går du igenom vägledningen om hur du skapar en skyddsgrupp. Välj sedan **Nästa**.
1. För skyddsgrupptypen väljer du **Servrar**.

    ![Välj grupptyp för servrarskydd](./media/backup-azure-backup-sql/pg-servers.png)
1. Expandera SQL Server-datorn där de databaser som du vill säkerhetskopiera finns. Du ser de datakällor som kan säkerhetskopieras från den servern. Expandera **alla SQL-resurser** och välj sedan de databaser som du vill säkerhetskopiera. I det här exemplet väljer vi ReportServer$MSDPM2012 och ReportServer$MSDPM2012TempDB. Välj **Nästa**.

    ![Välja en SQL Server-databas](./media/backup-azure-backup-sql/pg-databases.png)
1. Namnge skyddsgruppen och välj **sedan Jag vill ha onlineskydd**.

    ![Välj en dataskyddsmetod – kortvarig diskskydd eller Azure-skydd online](./media/backup-azure-backup-sql/pg-name.png)
1. På sidan **Ange kortsiktiga mål** ska du inkludera de indata som krävs för att skapa säkerhetskopieringspunkter på disken.

    I det här exemplet är **kvarhållningsintervallet** inställt på *5 dagar*. Frekvensen för **säkerhetskopiering synkronisering** är inställd på en gång var *15:e minut*. **Express Full Backup** är satt till *20:00*.

    ![Ställ in kortsiktiga mål för säkerhetskopieringsskydd](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I det här exemplet skapas en säkerhetskopieringspunkt klockan 20:00 varje dag. De data som har ändrats sedan föregående dags säkerhetskopieringspunkt för 8:00 överförs. Den här processen kallas **Express Full Backup**. Även om transaktionsloggarna synkroniseras var 15:e minut, om vi behöver återställa databasen klockan 21:00, skapas punkten genom att spela upp loggarna från den sista express fullständiga säkerhetskopieringspunkten, vilket är 20:00 i det här exemplet.
   >
   >

1. Välj **Nästa**. MABS visar det totala tillgängliga lagringsutrymmet. Den visar också det potentiella diskutrymmesutnyttjandet.

    ![Ställ in diskallokering i MABS](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar MABS en volym per datakälla (SQL Server-databas). Volymen används för den första säkerhetskopian. I den här konfigurationen begränsar LDM (Logical Disk Manager) MABS-skydd till 300 datakällor (SQL Server-databaser). Du kan komma runt den här begränsningen genom att välja **Samuppvakningsdata i DPM Storage Pool**. Om du använder det här alternativet använder MABS en enskild volym för flera datakällor. Med den här inställningen kan MABS skydda upp till 2 000 SQL Server-databaser.

    Om du väljer **Öka volymerna automatiskt**kan MABS ta hänsyn till den ökade säkerhetskopieringsvolymen när produktionsdata växer. Om du inte väljer **Öka volymerna automatiskt**begränsar MABS säkerhetskopieringslagringen till datakällorna i skyddsgruppen.
1. Om du är administratör kan du välja att överföra den första säkerhetskopian **automatiskt via nätverket** och välja tid för överföringen. Eller välj att **manuellt** överföra säkerhetskopian. Välj sedan **Nästa**.

    ![Välj en metod för att skapa repliker i MABS](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databasen). Säkerhetskopieringsdata flyttas från produktionsservern (SQL Server-datorn) till MABS. Om säkerhetskopian är stor kan överföring av data över nätverket orsaka överbelastning av bandbredden. Därför kan administratörer välja att använda flyttbara media för att överföra den första säkerhetskopian **manuellt**. Eller så kan de överföra data **automatiskt över nätverket** vid en viss tidpunkt.

    När den första säkerhetskopian är klar fortsätter säkerhetskopiorna stegvis på den första säkerhetskopian. Inkrementella säkerhetskopior tenderar att vara små och överförs enkelt över nätverket.
1. Välj när du vill köra en konsekvenskontroll. Välj sedan **Nästa**.

    ![Välj när en konsekvenskontroll ska köras](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS kan köra en konsekvenskontroll av säkerhetskopiornas integritet. Den beräknar kontrollsumman för säkerhetskopian på produktionsservern (SQL Server-datorn i det här exemplet) och säkerhetskopierade data för filen i MABS. Om kontrollen hittar en konflikt antas den säkerhetskopierade filen i MABS vara skadad. MABS åtgärdar säkerhetskopierade data genom att skicka block som motsvarar kontrollsumman matchar inte. Eftersom konsekvenskontrollen är en prestandaintensiv åtgärd kan administratörer välja att schemalägga konsekvenskontrollen eller köra den automatiskt.
1. Välj de datakällor som ska skyddas i Azure. Välj sedan **Nästa**.

    ![Välj datakällor som ska skyddas i Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Om du är administratör kan du välja säkerhetskopieringsscheman och bevarandeprinciper som passar organisationens principer.

    ![Välj scheman och bevarandeprinciper](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopior dagligen klockan 12:00 och 20:00.

    > [!TIP]
    > För snabb återställning, hålla några kortsiktiga återställningspunkter på din disk. Dessa återställningspunkter används för operativ återställning. Azure fungerar som en bra plats på annan plats, vilket ger högre serviceavtal och garanterad tillgänglighet.
    >
    > Använd DPM (Data Protection Manager) för att schemalägga Azure-säkerhetskopior när de lokala disksäkerhetskopiorna är klara. När du följer den här övningen kopieras den senaste disksäkerhetskopieras till Azure.
    >


1. Välj schemat för bevarandeprincipen. Mer information om hur bevarandeprincipen fungerar finns i [Använda Azure Backup för att ersätta din bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Välj en bevarandeprincip i MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopior tas dagligen kl 12:00 och 20:00. De har hållits i 180 dagar.
    * Förstärkningen på lördag klockan 12:00 hålls i 104 veckor.
    * Förstärkningen från den sista lördagen i månaden klockan 12:00 hålls i 60 månader.
    * Förstärkningen från den sista lördagen i mars kl 12:00 hålls i 10 år.

    När du har valt en bevarandeprincip väljer du **Nästa**.
1. Välj hur du överför den första säkerhetskopian till Azure.

    * Alternativet **Automatiskt över nätverket** följer ditt säkerhetskopieringsschema för att överföra data till Azure.
    * Mer information om **offlinesäkerhetskopiering**finns i [Översikt över offlinesäkerhetskopiering](offline-backup-overview.md).

    När du har valt en överföringsmekanism väljer du **Nästa**.
1. På sidan **Sammanfattning** går du igenom policyinformationen. Välj sedan **Skapa grupp**. Du kan välja **Stäng** och titta på jobbförloppet på arbetsytan **Övervakning.**

    ![Utvecklingen av skapandet av skyddsgrupper](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Skapa säkerhetskopior på begäran av en SQL Server-databas

En återställningspunkt skapas när den första säkerhetskopieringen sker. I stället för att vänta på att schemat ska köras kan du manuellt utlösa skapandet av en återställningspunkt:

1. Kontrollera att databasstatusen är **OK**i skyddsgruppen .

    ![En skyddsgrupp som visar databasstatus](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Högerklicka på databasen och välj sedan **Skapa återställningspunkt**.

    ![Välj att skapa en återställningspunkt online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. Välj **Onlineskydd**i den nedrullningsbara menyn . Välj sedan **OK** för att starta skapandet av en återställningspunkt i Azure.

    ![Börja skapa en återställningspunkt i Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Du kan visa jobbstatus på arbetsytan **Övervakning.** 

    ![Visa jobbstatus i övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure

Så här återställer du en skyddad entitet, till exempel en SQL Server-databas, från Azure:

1. Öppna DPM-serverhanteringskonsolen. Gå till arbetsytan **Återställning** för att se de servrar som DPM säkerhetskopierar. Välj databasen (i det här exemplet ReportServer$MSDPM2012). Välj en **återställningstid** som slutar med **Online**.

    ![Välja en återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Högerklicka på databasnamnet och välj **Återställ**.

    ![Återställa en databas från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM visar information om återställningspunkten. Välj **Nästa**. Om du vill skriva över databasen väljer du **återställningstypen Återställ till den ursprungliga instansen av SQL Server**. Välj sedan **Nästa**.

    ![Återställa en databas till den ursprungliga platsen](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet tillåter DPM återställning av databasen till en annan SQL Server-instans eller till en fristående nätverksmapp.
1. På sidan **Ange återställningsalternativ** kan du välja återställningsalternativ. Du kan till exempel välja **Begränsning av nätverksbandbreddsanvändning** för att begränsa den bandbredd som återställningen använder. Välj sedan **Nästa**.
1. På sidan **Sammanfattning** visas den aktuella återställningskonfigurationen. Välj **Återställ**.

    Återställningsstatusen visar databasen som återställs. Du kan välja **Stäng** för att stänga guiden och visa förloppet på arbetsytan **Övervakning.**

    ![Starta återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar överensstämmer den återställda databasen med programmet.

### <a name="next-steps"></a>Nästa steg

Mer information finns i [Vanliga frågor och svar om Azure Backup](backup-azure-backup-faq.md).
