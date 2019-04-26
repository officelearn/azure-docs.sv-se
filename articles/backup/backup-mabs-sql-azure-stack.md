---
title: Säkerhetskopiera SQL Server-arbetsbelastningar på Azure Stack
description: Använda Azure Backup Server för att skydda SQL Server-arbetsbelastningar på Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: adigan
ms.openlocfilehash: fb064c39fa014515fb2a3f4ccc96ce216f2f7b2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254471"
---
# <a name="back-up-sql-server-on-stack"></a>Säkerhetskopiera SQL Server på Stack
Använd den här artikeln för att konfigurera Microsoft Azure Backup Server (MABS) för att skydda SQL Server-databaser på Azure Stack.

Hantering av säkerhetskopiering av SQL Server-databas till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser
2. Skapa säkerhetskopior på begäran
3. Återställa databasen från diskar och från Azure

## <a name="before-you-start"></a>Innan du börjar

[Installera och förbereda Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure
1. I Användargränssnittet för Azure Backup Server klickar du på den **Protection** arbetsyta.

2. Klicka på verktygsfliken **New** att skapa en ny skyddsgrupp.

    ![Skapa Skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server startar guiden Skyddsgrupp som leder dig genom att skapa en **Skyddsgrupp**. Klicka på **Nästa**.

3. I den **Välj typ av Skyddsgrupp** , väljer **servrar**.

    ![Välj typ av Skyddsgrupp - 'Servers ”](./media/backup-azure-backup-sql/pg-servers.png)

4. I den **Välj gruppmedlemmar** olika datakällor visas en skärm, listan Tillgängliga medlemmar. Klicka på **+** att expandera en mapp och undermapparna. Klicka på kryssrutan för att välja ett objekt.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)

    Alla valda objekt visas i listan valda medlemmar. När du har valt servrar eller databaser som du vill skydda, klickar du på **nästa**.

5. I den **Välj Dataskyddsmetod** skärmen, ange ett namn för skyddsgruppen och välj den **jag vill ha ett Onlineskydd** kryssrutan.

    ![Dataskyddsmetod - kortvarigt skydd på disk och Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. I den **ange kortsiktiga mål** skärmen, innehåller de nödvändiga indata för att skapa säkerhetskopieringspunkter till hårddisken och klicka på **nästa**.

    I det här exemplet **Kvarhållningsintervall** är **5 dagar**, **Synkroniseringsfrekvens** en gång varje **15 minuter**, vilket är säkerhetskopieringen frekvensen. **Snabb och fullständig säkerhetskopiering** är inställd på **8:00 P.M**.

    ![Kortvariga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I det här exemplet visas kl 8:00 varje dag skapas en säkerhetskopieringspunkt genom att överföra de ändrade data från föregående dag 8:00 PM säkerhetskopieringspunkt. Den här processen kallas **Express fullständig säkerhetskopiering**. Transaktionsloggar synkroniseras var 15: e minut. Om du vill återställa databasen klockan 9:00 punkten har skapats från loggar från senaste express fullständig punkt (20: 00 i det här fallet).
   >
   >

7. På den **granska diskallokering** skärmen, kontrollera det övergripande lagringsutrymmet och diskutrymme som potentiellt. Klicka på **Nästa**.

8. I den **väljer Replikskapandemetod**, Välj hur du skapar din första återställningspunkt. Du kan överföra den första säkerhetskopieringen manuellt (av network) för att undvika överbelastning av bandbredd eller via nätverket. Om du väljer att vänta med att överföra den första säkerhetskopian måste ange du tiden för inledande överföring. Klicka på **Nästa**.

    ![Inledande replikeringsmetod](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian måste överföra hela datakällan (SQL Server-databas) från produktionsservern (SQL Server-dator) till Azure Backup Server. Dessa data kan vara stora och överföra data via nätverket kan överstiga bandbredd. Därför kan du välja att överföra den första säkerhetskopieringen: **Manuellt** (med flyttbara medier) att undvika överbelastning av bandbredd, eller **automatiskt över nätverket** (vid en viss tidpunkt).

    När den första säkerhetskopieringen har slutförts, är resten av säkerhetskopiorna säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopieringar tenderar att vara liten och överföras enkelt via nätverket.

9. Välj när du vill att konsekvenskontrollen att köra och klicka på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server utför en konsekvenskontroll på integriteten hos säkerhetskopieringspunkten. Azure Backup Server beräknar kontrollsumma för säkerhetskopian på produktionsservern (SQL Server-dator i det här scenariot) och de säkerhetskopierade data för filen. Om det finns en konflikt, antas den säkerhetskopierade filen på Azure Backup Server är skadad. Azure Backup Server hejdar säkerhetskopierade data genom att skicka de block som motsvarar den felaktig matchning av kontrollsumma. Eftersom konsekvenskontroller är prestandakrävande, kan du schemalägga konsekvenskontrollen eller körs automatiskt.

10. Om du vill ange online-skydd av datakällor, väljer du databaserna som ska skyddas till Azure och klicka på **nästa**.

    ![Välj datakällor](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Välj säkerhetskopieringsscheman och lagringsprinciper som passar organisationens principer.

    ![Schema och kvarhållning](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet kommer säkerhetskopior en gång om dagen vid 12:00 och 20: 00 (längst ned på skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några kortsiktiga återställningspunkter på disk för snabb återställning. Dessa återställningspunkter används för återställning. Azure fungerar som en bra extern plats med högre serviceavtal och garanterad tillgänglighet.
    >
    >

    **Bästa praxis**: Om du schemalägger säkerhetskopieringar till Azure för att starta när säkerhetskopiorna som lokala diskar har slutfört kopieras alltid de senaste disksäkerhetskopieringar till Azure.

12. Välj bevarandeschema för principen. Information om hur bevarandeprincipen fungerar finns på [Använd Azure Backup för att ersätta band infrastruktur artikeln](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopior tas en gång om dagen vid 12:00 och 20: 00 (längst ned på skärmen) och bevaras i 180 dagar.
    * Säkerhetskopieringen av lördag klockan 12:00. behålls i 104 veckor
    * Säkerhetskopieringen av senaste lördag klockan 12:00. behålls i 60 månader
    * Säkerhetskopieringen av sista lördagen i mars klockan 12:00. sparas i tio år
13. Klicka på **nästa** och väljer lämpligt alternativ för att överföra den första säkerhetskopian till Azure. Du kan välja **automatiskt över nätverket**

14. När du granska information om principen i den **sammanfattning** klickar du på **Skapa grupp** att slutföra arbetsflödet. Du kan klicka på **Stäng** och övervaka jobbförloppet i arbetsytan övervakning.

    ![Skapandet av Skyddsgruppen pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhetskopiering på begäran för en SQL Server-databas
Även om föregående steg skapade du en princip för säkerhetskopiering, skapas en ”återställningspunkt” endast när den första säkerhetskopieringen har körts. I stället väntar scheduler för att sätta peka stegen nedan utlösaren skapandet av en återställning manuellt.

1. Vänta tills skyddsgruppens status visas **OK** för databasen innan du skapar återställningspunkten.

    ![Skyddsgruppmedlemmar](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen Välj **Skapa återställningspunkt**.

    ![Skapa Onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **Onlineskydd** i den nedrullningsbara menyn och klicka på **OK** att starta skapandet av en återställningspunkt i Azure.

    ![Skapa återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visa jobbförloppet i den **övervakning** arbetsyta.

    ![Övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure
Följande steg krävs för att återställa en skyddad entitet (SQL Server-databas) från Azure.

1. Öppna hanteringskonsolen för Azure Backup Server. Gå till **Recovery** arbetsyta där du kan se de skyddade servrarna. Bläddra i databasen (i det här fallet ReportServer$ MSDPM2012). Välj en **återställning från** tid som anges som en **Online** peka.

    ![Välj återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på namnet på databasen och på **återställa**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS visar information om återställningspunkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du typ av återställning **Återställ till ursprunglig instans av SQL Server**. Klicka på **Nästa**.

    ![Återställa till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet återställer MABS databasen till en annan SQL Server-instans eller till en nätverksmapp för fristående.

4. I den **Ange återställningsalternativ** skärmen, kan du välja återställningsalternativ som begränsning av nätverksbandbredd för att begränsa bandbredden som används av recovery. Klicka på **Nästa**.

5. I den **sammanfattning** ser du alla recovery-konfigurationer som tillhandahålls hittills. Klicka på **återställa**.

    Status på återställningen visar databas som återställs. Du kan klicka på **Stäng** att Stäng guiden och visa förloppet i den **övervakning** arbetsyta.

    ![Påbörja återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen programkonsekvent.

## <a name="next-steps"></a>Nästa steg

Se den [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) artikeln.
Se den [säkerhetskopiering SharePoint på Azure Stack](backup-mabs-sharepoint-azure-stack.md) artikeln.
