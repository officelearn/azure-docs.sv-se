---
title: Säkerhetskopiera SQL Server till Azure som en DPM-arbetsbelastning
description: En introduktion till säkerhetskopiering av SQL Server-databaser med hjälp av Azure Backup-tjänsten
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8cbb8c833bc2933afac300bcc848fd50861011d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505928"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Säkerhetskopiera SQL Server till Azure som en DPM-arbetsbelastning

Den här artikeln leder dig genom konfigurationsstegen för att säkerhetskopiera SQL Server-databaser med hjälp av Azure Backup.

Om du vill säkerhetskopiera SQL Server-databaser till Azure behöver du ett Azure-konto. Om du inte har ett kan du skapa ett kostnadsfritt konto på bara några minuter. Mer information finns i [Skapa ditt kostnadsfria Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

Så här säkerhetskopierar du en SQL Server-databas till Azure och återställer den från Azure:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser i Azure.
1. Skapa säkerhetskopior på begäran i Azure.
1. Återställa databasen från Azure.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar, se till att du har uppfyllt [förutsättningarna](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att använda Azure Backup för att skydda arbetsbelastningar. Här är några av de nödvändiga uppgifterna: 
* Skapa ett valv för säkerhetskopiering.
* Ladda ner valvreferenser. 
* Installera Azure Backup-agenten.
* Registrera servern med valvet.

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy 

Om du vill skydda SQL Server-databaser i Azure skapar du först en princip för säkerhetskopiering:

1. Välj arbetsytan Skydd på DPM-servern **(Data** Protection Manager).
1. Välj **Ny** om du vill skapa en skyddsgrupp.

    ![Skapa en skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)
1. På startsidan går du igenom vägledningen om hur du skapar en skyddsgrupp. Välj sedan **Nästa**.
1. Välj **servrar**.

    ![Välj grupptyp för servrarskydd](./media/backup-azure-backup-sql/pg-servers.png)
1. Expandera SQL Server-datorn där de databaser som du vill säkerhetskopiera finns. Du ser de datakällor som kan säkerhetskopieras från den servern. Expandera **alla SQL-resurser** och välj sedan de databaser som du vill säkerhetskopiera. I det här exemplet väljer vi ReportServer$MSDPM2012 och ReportServer$MSDPM2012TempDB. Välj sedan **Nästa**.

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

1. Välj **Nästa**. DPM visar det totala tillgängliga lagringsutrymmet. Den visar också det potentiella diskutrymmesutnyttjandet.

    ![Ställ in diskallokering](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar DPM en volym per datakälla (SQL Server-databas). Volymen används för den första säkerhetskopian. I den här konfigurationen begränsar LDM (Logical Disk Manager) DPM-skydd till 300 datakällor (SQL Server-databaser). Du kan komma runt den här begränsningen genom att välja **Samuppvakningsdata i DPM Storage Pool**. Om du använder det här alternativet använder DPM en enskild volym för flera datakällor. Med den här inställningen kan DPM skydda upp till 2 000 SQL Server-databaser.

    Om du väljer **Öka volymerna automatiskt**kan DPM ta hänsyn till den ökade säkerhetskopieringsvolymen när produktionsdata växer. Om du inte väljer **Öka volymerna automatiskt**begränsar DPM säkerhetskopieringslagringen till datakällorna i skyddsgruppen.

1. Om du är administratör kan du välja att överföra den första säkerhetskopian **automatiskt via nätverket** och välja tid för överföringen. Eller välj att **manuellt** överföra säkerhetskopian. Välj sedan **Nästa**.

    ![Välj en metod för att skapa repliker](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databasen). Säkerhetskopieringsdata flyttas från produktionsservern (SQL Server-datorn) till DPM-servern. Om säkerhetskopian är stor kan överföring av data över nätverket orsaka överbelastning av bandbredden. Därför kan administratörer välja att använda flyttbara media för att överföra den första säkerhetskopian **manuellt**. Eller så kan de överföra data **automatiskt över nätverket** vid en viss tidpunkt.

    När den första säkerhetskopian är klar fortsätter säkerhetskopiorna stegvis på den första säkerhetskopian. Inkrementella säkerhetskopior tenderar att vara små och överförs enkelt över nätverket.
    
1. Välj när du vill köra en konsekvenskontroll. Välj sedan **Nästa**.

    ![Välj när en konsekvenskontroll ska köras](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan köra en konsekvenskontroll av säkerhetskopiornas integritet. Den beräknar kontrollsumman för säkerhetskopian på produktionsservern (SQL Server-datorn i det här exemplet) och säkerhetskopierade data för filen i DPM. Om kontrollen hittar en konflikt antas den säkerhetskopierade filen i DPM vara skadad. DPM åtgärdar säkerhetskopierade data genom att skicka block som motsvarar kontrollsumman matchar inte. Eftersom konsekvenskontrollen är en prestandaintensiv åtgärd kan administratörer välja att schemalägga konsekvenskontrollen eller köra den automatiskt.

1. Välj de datakällor som ska skyddas i Azure. Välj sedan **Nästa**.

    ![Välj datakällor som ska skyddas i Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Om du är administratör kan du välja säkerhetskopieringsscheman och bevarandeprinciper som passar organisationens principer.

    ![Välj scheman och bevarandeprinciper](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopior dagligen klockan 12:00 och 20:00.

    > [!TIP]
    > För snabb återställning, hålla några kortsiktiga återställningspunkter på din disk. Dessa återställningspunkter används för operativ återställning. Azure fungerar som en bra plats på annan plats, vilket ger högre serviceavtal och garanterad tillgänglighet.
    >
    > Använd DPM för att schemalägga Azure-säkerhetskopior när de lokala disksäkerhetskopiorna är klara. När du följer den här övningen kopieras den senaste disksäkerhetskopieras till Azure.
    >

1. Välj schemat för bevarandeprincipen. Mer information om hur bevarandeprincipen fungerar finns i [Använda Azure Backup för att ersätta din bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Välj en bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

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

    I det här exemplet gör DPM att databasen kan återställas till en annan SQL Server-instans eller till en fristående nätverksmapp.
1. På sidan **Ange återställningsalternativ** kan du välja återställningsalternativ. Du kan till exempel välja **Begränsning av nätverksbandbreddsanvändning** för att begränsa den bandbredd som återställningen använder. Välj sedan **Nästa**.
1. På sidan **Sammanfattning** visas den aktuella återställningskonfigurationen. Välj **Återställ**.

    Återställningsstatusen visar databasen som återställs. Du kan välja **Stäng** för att stänga guiden och visa förloppet på arbetsytan **Övervakning.**

    ![Starta återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar överensstämmer den återställda databasen med programmet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Vanliga frågor och svar om Azure Backup](backup-azure-backup-faq.md).
