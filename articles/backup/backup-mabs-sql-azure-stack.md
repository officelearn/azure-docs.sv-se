---
title: Säkerhetskopiera SQL Server arbets belastningar på Azure Stack
description: Använd Azure Backup Server för att skydda SQL Server arbets belastning på Azure Stack.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: ab65a1bf371ff8581f347403b49fafed6697374c
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210227"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Säkerhetskopiera SQL Server på Azure Stack
Använd den här artikeln för att konfigurera Microsoft Azure Backup Server (MABS) för att skydda SQL Server databaser på Azure Stack.

Hantering av SQL Server databas säkerhets kopiering till Azure och återställning från Azure omfattar tre steg:

1. Skapa en säkerhets kopierings princip för att skydda SQL Server databaser
2. Skapa säkerhets kopior på begäran
3. Återställa databasen från diskar och från Azure

## <a name="before-you-start"></a>Innan du börjar

[Installera och förbered Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en säkerhets kopierings princip för att skydda SQL Server databaser till Azure
1. Klicka på arbets ytan **skydd** på Azure Backup Server användar gränssnitt.

2. I menyfliksområdet verktyg klickar du på **nytt** för att skapa en ny skydds grupp.

    ![Skapa skydds grupp](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server startar guiden skydds grupp som vägleder dig genom att skapa en **skydds grupp**. Klicka på **Nästa**.

3. På skärmen **Välj typ av skydds grupp** väljer du **servrar**.

    ![Välj typ av skydds grupp-servrar](./media/backup-azure-backup-sql/pg-servers.png)

4. På skärmen **Välj grupp medlemmar** visas de olika data källorna i listan Tillgängliga medlemmar. Klicka **+** om du vill expandera en mapp och Visa undermapparna. Klicka på kryss rutan för att välja ett objekt.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)

    Alla markerade objekt visas i listan med valda medlemmar. När du har valt de servrar eller databaser som du vill skydda klickar du på **Nästa**.

5. På skärmen **Välj data skydds metod** anger du ett namn på skydds gruppen och markerar kryss rutan **Jag vill ha onlineskydd** .

    ![Data skydds metod – kortsiktig disk & online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. På skärmen **Ange kortvariga mål** inkluderar du nödvändiga indata för att skapa säkerhets kopierings punkter på disk och klickar på **Nästa**.

    I exemplet är **kvarhållningsintervallet** **5 dagar**, **synkroniseringsfrekvensen** är en gång var **15: e minut**, vilket är säkerhets kopierings frekvensen. **Fullständig snabb säkerhets kopiering** har angetts till **8:00 P. M**.

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I exemplet som visas, med 8:00 PM varje dag, skapas en säkerhets kopierings punkt genom överföring av ändrade data från föregående dagars 8:00 PM-säkerhetskopia. Den här processen kallas **snabb och fullständig säkerhets kopiering**. Transaktions loggar synkroniseras var 15: e minut. Om du behöver återställa databasen på 9:00 PM skapas punkten från loggarna från den senaste fullständiga säkerhets kopierings punkten (20 i det här fallet).
   >
   >

7. På skärmen **Granska diskallokering** kontrollerar du det övergripande tillgängliga lagrings utrymmet och det potentiella disk utrymmet. Klicka på **Nästa**.

8. I **metoden Välj skapande av replik**väljer du hur du vill skapa din första återställnings punkt. Du kan överföra den första säkerhets kopieringen manuellt (av nätverket) för att undvika överbelastning av bandbredd eller över nätverket. Om du väljer att vänta med att överföra den första säkerhets kopian kan du ange tiden för den inledande överföringen. Klicka på **Nästa**.

    ![Inledande replikeringsmetod](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhets kopian kräver att hela data källan överförs (SQL Server databas) från produktions servern (SQL Server datorn) till Azure Backup Server. Dessa data kan vara stora och överföring av data över nätverket kan överskrida bandbredden. Därför kan du välja att överföra den första säkerhets kopieringen: **Manuellt** (med flyttbara medier) för att undvika överbelastning av bandbredd eller **automatiskt över nätverket** (vid en viss tidpunkt).

    När den första säkerhets kopieringen är klar är resten av säkerhets kopieringarna stegvisa säkerhets kopior på den första säkerhets kopian. Stegvisa säkerhets kopieringar tenderar att vara små och överförs enkelt över nätverket.

9. Välj när du vill att konsekvens kontrollen ska köras och klicka på **Nästa**.

    ![Konsekvens kontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server utför en konsekvens kontroll av säkerhets kopierings punktens integritet. Azure Backup Server beräknar den säkerhetskopierade filens kontroll summa på produktions servern (SQL Server datorn i det här scenariot) och de säkerhetskopierade data för den filen. Om det uppstår en konflikt antas det att den säkerhetskopierade filen på Azure Backup Server är skadad. Azure Backup Server reserverar säkerhetskopierade data genom att skicka blocken som motsvarar felaktig kontroll summa. Eftersom konsekvens kontroller är prestanda intensiva kan du schemalägga konsekvens kontrollen eller köra den automatiskt.

10. Om du vill ange onlineskydd för data källorna väljer du de databaser som ska skyddas till Azure och klickar på **Nästa**.

    ![Välj data källor](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Välj säkerhets kopierings scheman och bevarande principer som passar organisationens principer.

    ![Schema och kvarhållning](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhets kopiorna en gång om dagen på 12:00 PM och 8 PM (nedre delen av skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några kortsiktiga återställnings punkter på disk för snabb återställning. De här återställnings punkterna används för drift återställning. Azure fungerar som en annan plats med högre service avtal och garanterad tillgänglighet.
    >
    >

    **Bästa praxis**: Om du schemalägger säkerhets kopieringar till Azure för att starta när de lokala diskarna har slutförts, kopieras de senaste disk säkerhets kopiorna alltid till Azure.

12. Välj schema för bevarande princip. Information om hur bevarande principen fungerar finns i [använda Azure Backup för att ersätta din band infrastruktur artikel](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhets kopieringar tas en gång om dagen på 12:00 PM och 20:00 (nedre delen av skärmen) och bevaras i 180 dagar.
    * Säkerhets kopian på lördag kl. 12:00 sparas i 104 veckor
    * Säkerhets kopian den senaste lördagen klockan 12:00 behålls i 60 månader
    * Säkerhets kopian på sista lördagen i mars kl 12:00 behålls i 10 år
13. Klicka på **Nästa** och välj lämpligt alternativ för att överföra den första säkerhets kopian till Azure. Du kan välja **automatiskt över nätverket**

14. När du har granskat princip informationen på sidan **Sammanfattning** klickar du på **Skapa grupp** för att slutföra arbets flödet. Du kan klicka på **Stäng** och övervaka jobb förloppet i arbets ytan övervakning.

    ![Skapande av skydds grupp pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhets kopiering på begäran av en SQL Server databas
Medan föregående steg skapade en säkerhets kopierings policy skapas en "återställnings punkt" endast när den första säkerhets kopieringen sker. I stället för att vänta på att Schemaläggaren ska sätta igång, utlöser stegen nedan skapandet av en återställnings punkt manuellt.

1. Vänta tills skydds gruppens status visar **OK** för databasen innan du skapar återställnings punkten.

    ![Medlemmar i skydds grupp](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen och välj **skapa återställnings punkt**.

    ![Skapa online återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **onlineskydd** på den nedrullningsbara menyn och klicka på **OK** för att börja skapa en återställnings punkt i Azure.

    ![Skapa återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visa jobb förloppet i arbets ytan **övervakning** .

    ![Övervaknings konsol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server databas från Azure
Följande steg krävs för att återställa en skyddad entitet (SQL Server databas) från Azure.

1. Öppna konsolen för Azure Backup Server hantering. Gå till återställnings arbets ytan där du kan se de skyddade servrarna. Bläddra igenom den nödvändiga databasen (i det här fallet ReportServer $ MSDPM2012). Välj en **återställning från** tid som har angetts som en **online** -punkt.

    ![Välj återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på databas namnet och klicka på **Återställ**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS visar information om återställnings punkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du återställnings typen **Återställ till den ursprungliga instansen av SQL Server**. Klicka på **Nästa**.

    ![Återställ till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet återställer MABS databasen till en annan SQL Server instans eller till en fristående nätverksmapp.

4. På skärmen **Ange återställnings alternativ** kan du välja återställnings alternativ som begränsning av nätverks bandbredd för att begränsa bandbredden som används vid återställning. Klicka på **Nästa**.

5. På sidan **Sammanfattning** visas alla återställnings konfigurationer som du har angett hittills. Klicka på **Återställ**.

    Återställnings status visar den databas som återställs. Du kan klicka på **Stäng** för att stänga guiden och se förloppet i arbets ytan **övervakning** .

    ![Starta återställnings processen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen program konsekvent.

## <a name="next-steps"></a>Nästa steg

Se artikeln [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) .
Se [säkerhets kopian av SharePoint på Azure Stack](backup-mabs-sharepoint-azure-stack.md) -artikeln.
