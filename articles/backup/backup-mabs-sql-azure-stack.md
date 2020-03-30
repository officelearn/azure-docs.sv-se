---
title: Säkerhetskopiera SQL Server-arbetsbelastningar på Azure Stack
description: I den här artikeln kan du lära dig hur du konfigurerar Microsoft Azure Backup Server (MABS) för att skydda SQL Server-databaser på Azure Stack.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 03211e1147f96429a8406c4c95654161ed2bf308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172315"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Säkerhetskopiera SQL Server på Azure Stack

Använd den här artikeln om du vill konfigurera MABS (Microsoft Azure Backup Server) för att skydda SQL Server-databaser på Azure Stack.

Hanteringen av säkerhetskopiering av SQL Server-databasen till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser
2. Skapa säkerhetskopior på begäran
3. Återställa databasen från diskar och från Azure

## <a name="before-you-start"></a>Innan du börjar

[Installera och förbereda Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure

1. Klicka på arbetsytan **Skydd** på Azure Backup Server.

2. Klicka på **Nytt** i verktygsfliksområdet om du vill skapa en ny skyddsgrupp.

    ![Skapa skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server startar guiden Skyddsgrupp, som leder dig genom att skapa en **skyddsgrupp**. Klicka på **Nästa**.

3. Välj **Servrar**på skärmen **Välj skyddsgrupptyp** .

    ![Välj grupptyp för skydd - "Servrar"](./media/backup-azure-backup-sql/pg-servers.png)

4. På skärmen **Välj gruppmedlemmar** visas de olika datakällorna i listan Tillgängliga medlemmar. Klicka **+** här om du vill expandera en mapp och visa undermapparna. Markera ett objekt genom att klicka i kryssrutan.

    ![Välj SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    Alla markerade objekt visas i listan Markerade medlemmar. När du har valt de servrar eller databaser som du vill skydda klickar du på **Nästa**.

5. På skärmen **Välj dataskyddsmetod** anger du ett namn för skyddsgruppen och markerar kryssrutan **Jag vill ha onlineskydd.**

    ![Data Protection Method - kortsiktig disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. På skärmen **Ange kortsiktiga mål** ska du inkludera de indata som krävs för att skapa säkerhetskopieringspunkter till disk och klicka på **Nästa**.

    I exemplet är **kvarhållningsintervallet** **5 dagar**, **Synkroniseringsfrekvensen** är en gång var **15:e minut**, vilket är säkerhetskopieringsfrekvensen. **Express Full Backup** är satt till **8:00.**

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I exemplet som visas, vid 20:00 varje dag skapas en säkerhetskopieringspunkt genom att överföra ändrade data från föregående dags säkerhetskopieringspunkt för 8:00. Den här processen kallas **Express Full Backup**. Transaktionsloggar synkroniseras var 15:e minut. Om du behöver återställa databasen klockan 21:00 skapas punkten från loggarna från den senaste fullständiga säkerhetskopiorna för express (20:00 i det här fallet).
   >
   >

7. Kontrollera det totala tillgängliga lagringsutrymmet och det potentiella diskutrymmet på skärmen **Granska diskallokering.** Klicka på **Nästa**.

8. I **metoden Välj replikskapande**väljer du hur du skapar den första återställningspunkten. Du kan överföra den första säkerhetskopian manuellt (utanför nätverket) för att undvika överbelastning av bandbredden eller över nätverket. Om du väljer att vänta med att överföra den första säkerhetskopian kan du ange tiden för den första överföringen. Klicka på **Nästa**.

    ![Inledande replikeringsmetod](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databasen) från produktionsservern (SQL Server-datorn) till Azure Backup Server. Dessa data kan vara stora och överföringen av data över nätverket kan överstiga bandbredden. Därför kan du välja att överföra den första säkerhetskopian: **Manuellt** (med flyttbara media) för att undvika överbelastning **av bandbredden** eller automatiskt över nätverket (vid en angiven tidpunkt).

    När den första säkerhetskopian är klar är resten av säkerhetskopiorna inkrementella säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopior tenderar att vara små och överförs enkelt över nätverket.

9. Välj när du vill att konsekvenskontrollen ska köras och klicka på **Nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server utför en konsekvenskontroll av säkerhetskopiornas integritet. Azure Backup Server beräknar kontrollsumman för säkerhetskopian på produktionsservern (SQL Server-datorn i det här scenariot) och säkerhetskopierade data för den filen. Om det finns en konflikt antas den säkerhetskopierade filen på Azure Backup Server är skadad. Azure Backup Server korrigerar säkerhetskopierade data genom att skicka blocken som motsvarar kontrollsumman matchar inte. Eftersom konsekvenskontroller är prestandaintensiva kan du schemalägga konsekvenskontrollen eller köra den automatiskt.

10. Om du vill ange onlineskydd för datakällorna väljer du de databaser som ska skyddas till Azure och klickar på **Nästa**.

    ![Välj datakällor](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Välj säkerhetskopieringsscheman och bevarandeprinciper som passar organisationsprinciperna.

    ![Schema och kvarhållning](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopior en gång om dagen klockan 12:00 och 20:00 (nedersta delen av skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några kortsiktiga återställningspunkter på disken, för snabb återställning. Dessa återställningspunkter används för operativ återställning. Azure fungerar som en bra plats på annan plats med högre SLA och garanterad tillgänglighet.
    >
    >

    **Bästa praxis:** Om du schemalägger säkerhetskopior till Azure för att starta när de lokala disksäkerhetskopiorna har slutförts kopieras alltid de senaste disksäkerhetskopiorna till Azure.

12. Välj schemat för bevarandeprincipen. Information om hur bevarandeprincipen fungerar finns på [Använd Azure Backup för att ersätta artikeln med bandinfrastruktur](backup-azure-backup-cloud-as-tape.md).

    ![Loggperiod](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopior tas en gång om dagen klockan 12:00 och 20:00 (nedre delen av skärmen) och behålls i 180 dagar.
    * Förstärkningen på lördag klockan 12:00. behålls i 104 veckor
    * Förstärkningen i lördags klockan 12.00. behålls i 60 månader
    * Förstärkningen i lördags i mars klockan 12.00. behålls i 10 år
13. Klicka på **Nästa** och välj lämpligt alternativ för att överföra den första säkerhetskopian till Azure. Du kan välja **Automatiskt framför nätverket**

14. När du har granskat principinformationen på **skärmen Sammanfattning** klickar du på **Skapa grupp** för att slutföra arbetsflödet. Du kan klicka på **Stäng** och övervaka jobbstatusen på övervakningsarbetsytan.

    ![Ingående skyddsgrupp](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhetskopiering på begäran av en SQL Server-databas

Medan de tidigare stegen skapade en princip för säkerhetskopiering skapas en "återställningspunkt" endast när den första säkerhetskopieringen sker. I stället för att vänta på att schemaläggaren ska sparka in utlöser stegen nedan skapandet av en återställningspunkt manuellt.

1. Vänta tills skyddsgruppstatusen visar **OK** för databasen innan återställningspunkten skapas.

    ![Medlemmar i skyddsgruppen](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen och välj **Skapa återställningspunkt**.

    ![Skapa återställningspunkt online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **Onlineskydd** i den nedrullningsvänliga menyn och klicka på **OK** för att börja skapa en återställningspunkt i Azure.

    ![Skapa återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visa jobbstatusen på arbetsytan **Övervakning.**

    ![Övervakningskonsol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure

Följande steg krävs för att återställa en skyddad entitet (SQL Server-databas) från Azure.

1. Öppna Azure Backup Server Management Console. Navigera till **återställningsarbetsytan** där du kan se de skyddade servrarna. Bläddra i den nödvändiga databasen (i det här fallet ReportServer$MSDPM2012). Välj en **återställning från** tid som anges som en **onlinepunkt.**

    ![Välj återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på databasnamnet och klicka på **Återställ**.

    ![Återställ från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS visar detaljerna i återställningspunkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du **återställningstypen Återställ till den ursprungliga instansen av SQL Server**. Klicka på **Nästa**.

    ![Återställa till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet återställer MABS databasen till en annan SQL Server-instans eller till en fristående nätverksmapp.

4. På skärmen **Ange återställningsalternativ** kan du välja återställningsalternativ som begränsning av nätverksbandbredd för att begränsa den bandbredd som används av återställningen. Klicka på **Nästa**.

5. På **skärmen Sammanfattning** visas alla återställningskonfigurationer som hittills. Klicka på **Återställ**.

    Återställningsstatusen visar databasen som återställs. Du kan klicka på **Stäng** för att stänga guiden och visa förloppet på arbetsytan **Övervakning.**

    ![Initiera återställningsprocess](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen programkonsekvent.

## <a name="next-steps"></a>Nästa steg

Se [artikel om säkerhetskopieringsfiler och program.](backup-mabs-files-applications-azure-stack.md)
Se artikeln [Backup SharePoint på Azure Stack.](backup-mabs-sharepoint-azure-stack.md)
