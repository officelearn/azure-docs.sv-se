---
title: Säkerhetskopiera SQL Server-arbetsbelastningar på Azure-stacken
description: Använda Azure Backup Server för att skydda SQL Server-arbetsbelastning på Azure-stacken.
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: ca7da7ab048b6f7bfdba81aac9bc7702b20ff967
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751805"
---
# <a name="back-up-sql-server-on-stack"></a>Säkerhetskopiera SQL Server på Stack
Använd den här artikeln för att konfigurera Microsoft Azure Backup Server (MABS) för att skydda SQL Server-databaser på Azure-stacken.

Hantering av säkerhetskopiering av SQL Server-databas till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser
2. Skapa säkerhetskopior på begäran
3. Återställa databasen från diskar och från Azure

## <a name="before-you-start"></a>Innan du börjar

[Installera och förbereda Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure
1. Klicka på Azure Backup Server UI, den **skydd** arbetsytan.

2. Klicka på verktygsfliken **ny** att skapa en ny skyddsgrupp.

    ![Skapa en Skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup-servern startar guiden Skyddsgruppen som leder dig genom att skapa en **Skyddsgrupp**. Klicka på **Nästa**.

3. I den **Välj typ av Skyddsgrupp** , väljer **servrar**.

    ![Välj typ av Skyddsgrupp - 'Servrar'](./media/backup-azure-backup-sql/pg-servers.png)

4. I den **Välj gruppmedlemmar** listan över tillgängliga medlemmar skärmen visar olika datakällor. Klicka på **+** att expandera en mapp och undermapparna. Klicka på kryssrutan för att välja ett objekt.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)

    Alla valda objekt visas i listan valda medlemmar. När du har valt servrar eller databaser som du vill skydda, klickar du på **nästa**.

5. I den **Välj Dataskyddsmetod** skärmen, ange ett namn för skyddsgruppen och välj den **jag vill Onlineskydd** kryssrutan.

    ![Dataskyddsmetod - kortvarigt skydd på disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. I den **ange kortvariga mål** skärmen får innehålla nödvändiga indata för att skapa säkerhetskopiering punkter disk och klicka sedan på **nästa**.

    I det här exemplet **Kvarhållningsintervall** är **5 dagar**, **Synkroniseringsfrekvens** en gång var **15 minuter**, vilket är säkerhetskopieringen frekvensen. **Snabb och fullständig säkerhetskopiering** är inställd på **8:00 P.M**.

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > I exemplet som visas vid 20:00:00 varje dag som en säkerhetskopiering skapas av den ändrade data överförs från föregående dag 8:00 PM säkerhetskopia punkt. Den här processen kallas **snabb fullständig säkerhetskopiering**. Transaktionsloggar synkroniseras var 15: e minut. Om du behöver återställa databasen på 9:00 PM punkten skapas från loggar från senaste snabb fullständig säkerhetskopiering punkt (20: 00 i detta fall).
   >
   >

7. På den **granska diskallokering** skärmen, kontrollera övergripande lagringsutrymme och diskutrymme som potentiellt. Klicka på **Nästa**.

8. I den **Välj Replikskapandemetod**, Välj hur du vill skapa din första återställningspunkt. Du kan överföra den första säkerhetskopian manuellt (av nätverk) för att undvika överbelastning av bandbredd eller via nätverket. Om du väljer att vänta med att överföra den första säkerhetskopian kan ange du tiden för den första överföringen. Klicka på **Nästa**.

    ![Metoden för inledande replikering](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian måste överföra hela datakällan (SQL Server-databas) från produktionsservern (SQL Server-dator) till Azure Backup-Server. Dessa data kan vara stora och överföra data över nätverket kan överstiga bandbredd. Därför kan du välja att överföra den första säkerhetskopian: **manuellt** (med flyttbara media) att undvika överbelastning av bandbredd, eller **automatiskt via nätverket** (på en angiven tid).

    När den första säkerhetskopieringen är klar, är resten av säkerhetskopior säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopieringar brukar vara små och överföras lätt via nätverket.

9. Välj när du vill att konsekvenskontrollen att köra och klicka på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup-servern utför en konsekvenskontroll på integriteten hos säkerhetskopieringspunkt. Azure Backup Server beräknar kontrollsumma för säkerhetskopian på produktionsservern (SQL Server-dator i det här scenariot) och de säkerhetskopierade data för den filen. Om det finns en konflikt, antas den säkerhetskopierade filen på Azure Backup Server är skadad. Azure Backup Server hejdar säkerhetskopierade data genom att skicka block som motsvarar den felaktig matchning av kontrollsumma. Eftersom konsekvenskontroller är processorintensiva prestanda kan du schemalägga en konsekvenskontroll eller kör den automatiskt.

10. Välj databaser att skydda till Azure och klicka på för att ange online-skydd av datakällor **nästa**.

    ![Välj datakällor](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. Välj scheman för säkerhetskopiering och bevarandeprinciper som passar organisationens principer.

    ![Schemat och lagring](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopieringar en gång om dagen med 12:00 och 20: 00 (längst ned på skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några återställningspunkter för kortvarigt skydd på disk för snabb återställning. Dessa återställningspunkter används för operativa återställning. Azure fungerar som en bra extern plats med högre SLA och garanteras tillgänglighet.
    >
    >

    **Bästa praxis**: Om du schemalägger säkerhetskopieringar till Azure för att starta när lokal disk-säkerhetskopiering har slutfört kopieras alltid de senaste disk-säkerhetskopieringarna till Azure.

12. Välj principen bevarandeschemat. Information om hur bevarandeprincipen fungerar tillhandahålls på [Använd Azure Backup för att ersätta band infrastruktur artikeln](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopieringar tas en gång om dagen med 12:00 och 20: 00 (längst ned på skärmen) och bevaras i 180 dagar.
    * Säkerhetskopian på lördag kl. 12:00 sparas i 104 veckor
    * Säkerhetskopian på sista lördag kl. 12:00 sparas i 60 månader
    * Säkerhetskopian på sista lördag mars klockan 12:00. sparas för 10 år
13. Klicka på **nästa** och väljer lämpligt alternativ för att överföra den första säkerhetskopian till Azure. Du kan välja **automatiskt via nätverket**

14. När du granska information om principen i den **sammanfattning** klickar du på **Skapa grupp** att slutföra arbetsflödet. Du kan klicka på **Stäng** och övervaka jobbförloppet på arbetsytan övervakning.

    ![Skapandet av Skyddsgruppen pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhetskopiering på begäran av en SQL Server-databas
När de föregående stegen skapade en princip för säkerhetskopiering, skapas en ”återställningspunkt” endast när den första säkerhetskopieringen inträffar. I stället för väntar Schemaläggaren kan förbättra, peka stegen nedan utlösaren genereringen av en återställningspunkt manuellt.

1. Vänta tills skyddsgruppens status visas **OK** för databasen innan du skapar återställningspunkten.

    ![Skyddsgruppmedlemmar](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen och välj **Skapa återställningspunkt**.

    ![Skapa Onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **Onlineskydd** i den nedrullningsbara menyn och klicka på **OK** starta skapar en återställningspunkt i Azure.

    ![Skapa återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Visa jobbförloppet på den **övervakning** arbetsytan.

    ![Övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure
Följande steg krävs för att återställa en skyddad entitet (SQL Server-databas) från Azure.

1. Öppna hanteringskonsolen för Azure Backup-servern. Gå till **Recovery** arbetsyta där du kan se de skyddade servrarna. Bläddra obligatorisk databas (i det här fallet ReportServer$ MSDPM2012). Välj en **återställning från** tid som har angetts som en **Online** peka.

    ![Välj återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på namnet på databasen och på **återställa**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS visar information om återställningspunkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du typ av återställning **Återställ till ursprunglig instans av SQL Server**. Klicka på **Nästa**.

    ![Återställa till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet återställer MABS databasen till en annan SQL Server-instans eller till en nätverksmapp för fristående.

4. I den **Ange återställningsalternativ** skärmen, som du kan välja återställningsalternativ som begränsning av nätverksbandbredd för att begränsa bandbredden som används av återställningen. Klicka på **Nästa**.

5. I den **sammanfattning** ser du alla recovery konfigurationer som hittills. Klicka på **återställa**.

    Status på återställningen visar databas som återställs. Du kan klicka på **Stäng** att stänga guiden och visa förloppet på den **övervakning** arbetsytan.

    ![Starta återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen har slutförts är den återställda databasen program som är konsekventa.

## <a name="next-steps"></a>Nästa steg

Finns det [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) artikel.
Finns det [säkerhetskopiering SharePoint Azure stacken](backup-mabs-sharepoint-azure-stack.md) artikel.
