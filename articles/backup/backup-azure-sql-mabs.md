---
title: Säkerhetskopiera SQL Server med Azure Backup Server
description: I den här artikeln får du lära dig hur du säkerhetskopierar SQL Server databaser med hjälp av Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 2bb172ca36f3f932fdaaf5b71e8fa183c04d1510
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194185"
---
# <a name="back-up-sql-server-to-azure-by-using-azure-backup-server"></a>Säkerhetskopiera SQL Server till Azure med Azure Backup Server

Den här artikeln hjälper dig att konfigurera säkerhets kopieringar av SQL Server databaser med hjälp av Microsoft Azure Backup Server (MABS).

Säkerhetskopiera en SQL Server-databas och återställa den från Azure:

1. Skapa en säkerhets kopierings princip för att skydda SQL Server databaser i Azure.
1. Skapa säkerhets kopior på begäran i Azure.
1. Återställ databasen i Azure.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du kontrol lera att du har [installerat och för berett Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy"></a>Skapa en säkerhetskopieringspolicy

För att skydda SQL Server databaser i Azure måste du först skapa en princip för säkerhets kopiering:

1. I Azure Backup Server väljer du arbets ytan **skydd** .
1. Välj **ny** för att skapa en skydds grupp.

    ![Skapa en skydds grupp i Azure Backup Server](./media/backup-azure-backup-sql/protection-group.png)
1. På sidan Start granskar du vägledningen om hur du skapar en skydds grupp. Välj **Nästa**.
1. För typ av skydds grupp väljer du **servrar**.

    ![Välj typ av skydds grupp för servrar](./media/backup-azure-backup-sql/pg-servers.png)
1. Expandera SQL Server-instansen där de databaser som du vill säkerhetskopiera finns. Du ser de data källor som kan säkerhets kopie ras från den servern. Expandera **alla SQL-resurser** och välj sedan de databaser som du vill säkerhetskopiera. I det här exemplet väljer du ReportServer $ MSDPM2012 och ReportServer $ MSDPM2012TempDB. Välj **Nästa**.

    ![Välj en SQL Server databas](./media/backup-azure-backup-sql/pg-databases.png)
1. Ge skydds gruppen ett namn och välj sedan **Jag vill ha onlineskydd**.

    ![Välj en data skydds metod – kortvarigt disk skydd eller online Azure-skydd](./media/backup-azure-backup-sql/pg-name.png)
1. På sidan **Ange kortsiktiga mål** inkluderar du nödvändiga indata för att skapa säkerhets kopierings punkter på disken.

    I det här exemplet är **kvarhållningsintervallet** inställt på *5 dagar*. **Frekvensen för synkronisering** av säkerhets kopiering är en gång var *15: e minut*. **Fullständig snabb säkerhets kopiering** har värdet *8:00 PM*.

    ![Konfigurera kortsiktiga mål för säkerhets kopierings skydd](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I det här exemplet skapas en säkerhets kopierings punkt på 8:00 PM varje dag. De data som har ändrats sedan den föregående dagen för 8:00 PM-säkerhetskopiering överförs. Den här processen kallas **snabb och fullständig säkerhets kopiering**. Även om transaktions loggarna synkroniseras var 15: e minut, om vi behöver återställa databasen kl. 9:00, så skapas punkten genom att logga in loggarna från den senaste fullständiga snabb säkerhets kopierings punkten, som är 8:00 PM i det här exemplet.
   >
   >

1. Välj **Nästa**. MABS visar det övergripande tillgängliga lagrings utrymmet. Det visar även den potentiella användningen av disk utrymme.

    ![Konfigurera diskallokering i MABS](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar MABS en volym per data källa (SQL Server databas). Volymen används för den första säkerhets kopian. I den här konfigurationen begränsar LDM (Logical Disk Manager) MABS-skydd till 300-data källor (SQL Server databaser). Undvik den här begränsningen genom att välja **samplacera data i DPM-lagringspool**. Om du använder det här alternativet använder MABS en enda volym för flera data källor. Med den här installationen kan MABS skydda upp till 2 000 SQL Server-databaser.

    Om du väljer **utöka volymerna automatiskt**, kan Mabs konto för den ökade säkerhets kopierings volymen eftersom produktions data växer. Om du inte väljer **utöka volymerna automatiskt**, begränsar Mabs lagringen av säkerhets kopiorna till data källorna i skydds gruppen.
1. Om du är administratör kan du välja att överföra den första säkerhets kopieringen **automatiskt över nätverket** och välja överförings tiden. Eller Välj att **manuellt** överföra säkerhets kopian. Välj **Nästa**.

    ![Välj en metod för att skapa en replik i MABS](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhets kopian kräver överföring av hela data källan (SQL Server Database). Säkerhetskopierade data flyttas från produktions servern (SQL Server dator) till MABS. Om den här säkerhets kopian är stor kan överföring av data över nätverket orsaka överbelastning av bandbredd. Av den anledningen kan administratörer välja att använda flyttbara media för att överföra den första säkerhets kopieringen **manuellt**. Du kan också överföra data **automatiskt via nätverket** vid en viss tidpunkt.

    När den första säkerhets kopieringen är klar fortsätter säkerhets kopieringarna stegvis på den första säkerhets kopian. Stegvisa säkerhets kopieringar tenderar att vara små och överförs enkelt över nätverket.
1. Välj när du vill köra en konsekvens kontroll. Välj **Nästa**.

    ![Välj när du vill köra en konsekvens kontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS kan köra en konsekvens kontroll på säkerhets kopierings punktens integritet. Den beräknar säkerhets kopian för säkerhets kopian på produktions servern (SQL Server datorn i det här exemplet) och de säkerhetskopierade data för filen i MABS. Om kontrollen hittar en konflikt antas den säkerhetskopierade filen i MABS vara skadad. MABS åtgärdar säkerhetskopierade data genom att skicka block som motsvarar matchnings fel för kontroll summan. Eftersom konsekvens kontrollen är en prestanda intensiv åtgärd kan administratörer välja att schemalägga konsekvens kontrollen eller köra den automatiskt.
1. Välj de data källor som ska skyddas i Azure. Välj **Nästa**.

    ![Välj data källor som ska skyddas i Azure](./media/backup-azure-backup-sql/pg-sqldatabases.png)
1. Om du är administratör kan du välja säkerhets kopierings scheman och lagrings principer som passar organisationens principer.

    ![Välj scheman och bevarande principer](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhets kopiorna dagligen med 12:00 PM och 8:00 PM.

    > [!TIP]
    > Spara några kortsiktiga återställnings punkter på disken för snabb återställning. De här återställnings punkterna används för drift återställning. Azure fungerar som en annan plats, vilket ger högre service avtal och garanterad tillgänglighet.
    >
    > Använd Data Protection Manager (DPM) för att schemalägga Azure-säkerhetskopieringar när säkerhets kopieringen av lokala diskar slutförts. När du följer den här övningen kopieras den senaste säkerhets kopian av disken till Azure.
    >

1. Välj schema för bevarande princip. Mer information om hur bevarande principen fungerar finns i [använda Azure Backup för att ersätta din band infrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Välj en bevarande princip i MABS](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhets kopieringar tas dagligen med 12:00 PM och 8:00 PM. De behålls i 180 dagar.
    * Säkerhets kopian på lördag på 12:00 är kvar i 104 veckor.
    * Säkerhets kopieringen från den sista lördagen i månaden vid 12:00 är kvar i 60 månader.
    * Säkerhets kopieringen från den sista lördagen i mars kl. 12:00 sparas i 10 år.

    När du har valt en bevarande princip väljer du **Nästa**.
1. Välj hur du vill överföra den första säkerhets kopian till Azure.

    * Alternativet **automatiskt över nätverket** följer ditt schema för säkerhets kopiering för att överföra data till Azure.
    * Mer information om **säkerhets kopiering offline**finns i [Översikt över säkerhets kopiering offline](offline-backup-overview.md).

    När du har valt en överförings funktion väljer du **Nästa**.
1. Granska princip informationen på sidan **Sammanfattning** . Välj sedan **Skapa grupp**. Du kan välja **Stäng** och titta på jobb förloppet i arbets ytan **övervakning** .

    ![Förloppet för skapandet av skydds gruppen](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="create-on-demand-backup-copies-of-a-sql-server-database"></a>Skapa säkerhets kopior på begäran av en SQL Server databas

En återställnings punkt skapas när den första säkerhets kopieringen sker. I stället för att vänta på att schemat ska köras kan du manuellt utlösa skapandet av en återställnings punkt:

1. Kontrol lera att databasens status är **OK**i skydds gruppen.

    ![En skydds grupp som visar databasens status](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
1. Högerklicka på databasen och välj sedan **skapa återställnings punkt**.

    ![Välj att skapa en onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
1. I den nedrullningsbara menyn väljer du **onlineskydd**. Välj **OK** för att starta skapandet av en återställnings punkt i Azure.

    ![Börja skapa en återställnings punkt i Azure](./media/backup-azure-backup-sql/sqlbackup-azure.png)
1. Du kan visa jobb förloppet i arbets ytan **övervakning** .

    ![Visa jobb förlopp i övervaknings konsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server databas från Azure

Återställa en skyddad entitet, till exempel en SQL Server databas, från Azure:

1. Öppna DPM-serverns hanterings konsol. Gå till arbets ytan **återställning** för att se de servrar som DPM säkerhetskopierar. Välj databasen (i det här exemplet ReportServer $ MSDPM2012). Välj en **återställnings tid** som slutar med **online**.

    ![Välja en återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
1. Högerklicka på databas namnet och välj **Återställ**.

    ![Återställa en databas från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
1. DPM visar information om återställnings punkten. Välj **Nästa**. Om du vill skriva över databasen väljer du återställnings typen **Återställ till den ursprungliga instansen av SQL Server**. Välj **Nästa**.

    ![Återställa en databas till dess ursprungliga plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet tillåter DPM återställning av databasen till en annan SQL Server instans eller till en fristående nätverksmapp.
1. På sidan **Ange återställnings alternativ** kan du välja återställnings alternativ. Du kan till exempel välja **nätverks bandbredds begränsning** för att begränsa bandbredden som används i återställningen. Välj **Nästa**.
1. På sidan **Sammanfattning** visas den aktuella återställnings konfigurationen. Välj **Återställ**.

    Återställnings status visar den databas som återställs. Du kan välja **Stäng** om du vill stänga guiden och se förloppet i arbets ytan **övervakning** .

    ![Starta återställnings processen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen konsekvent med programmet.

### <a name="next-steps"></a>Nästa steg

Mer information finns i [vanliga frågor och svar om Azure Backup](backup-azure-backup-faq.md).
