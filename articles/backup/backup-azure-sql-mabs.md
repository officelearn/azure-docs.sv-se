---
title: Azure-säkerhetskopiering av SQL Server-arbetsbelastningar med Azure Backup Server
description: En introduktion till att säkerhetskopiera SQL Server-databaser med Azure Backup Server
services: backup
author: pvrk
manager: Shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 5d0fc66ff81672116d3d98ee9970456515ab3c9e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606400"
---
# <a name="back-up-sql-server-to-azure-with-azure-backup-server"></a>Säkerhetskopiera SQL Server till Azure med Azure Backup Server
Den här artikeln leder dig igenom konfigurationsstegen för säkerhetskopiering av SQL Server-databaser med Microsoft Azure Backup Server (MABS).

Hantering av säkerhetskopiering av SQL Server-databas till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure.
2. Skapa säkerhetskopior för på-begäran till Azure.
3. Återställ databasen från Azure.

## <a name="before-you-start"></a>Innan du börjar
Innan du börjar bör du kontrollera att du har [installerad och förberedda Azure Backup Server](backup-azure-microsoft-azure-backup.md).

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure
1. Klicka på Azure Backup Server UI, den **skydd** arbetsytan.
2. Klicka på verktygsfliken **ny** att skapa en ny skyddsgrupp.

    ![Skapa en Skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)
3. MABS visar startskärmen med vägledning om hur du skapar en **Skyddsgrupp**. Klicka på **Nästa**.
4. Välj **servrar**.

    ![Välj typ av Skyddsgrupp - 'Servrar'](./media/backup-azure-backup-sql/pg-servers.png)
5. Expandera den SQL Server-datorn där databaserna säkerhetskopieras finns. MABS visas olika datakällor som kan säkerhetskopieras från servern. Expandera den **alla SQL-resurser** och väljer du databaserna (i det här fallet vi valt ReportServer$ MSDPM2012 och ReportServer$ MSDPM2012TempDB) som ska säkerhetskopieras. Klicka på **Nästa**.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)
6. Ange ett namn för skyddsgruppen och välj den **jag vill Onlineskydd** kryssrutan.

    ![Dataskyddsmetod - kortvarigt disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. I den **ange kortvariga mål** skärmen får innehålla nödvändiga indata för att skapa säkerhetskopiering pekar på disk.

    Här visas som **Kvarhållningsintervall** är inställd på *5 dagar*, **synkroniseringsfrekvensen** anges en gång till varje *15 minuter* som är frekvensen för säkerhetskopia görs. **Snabb och fullständig säkerhetskopiering** är inställd på *8:00 P.M*.

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Vid 20:00:00 (enligt skärmen indata) skapas en säkerhetskopiering varje dag genom att överföra data som har ändrats från föregående dag 8:00 PM säkerhetskopieringspunkt. Den här processen kallas **snabb fullständig säkerhetskopiering**. Om det är nödvändigt att återställa databasen vid 21:00:00 – punkten skapas genom att spela upp loggar från senaste när transaktionen loggar synkroniseras var 15: e minut snabba och fullständiga säkerhetskopieringspunkt (20: 00 i detta fall).
   >
   >

8. Klicka på **Nästa**

    MABS visar det övergripande lagringsutrymmet och potentiella diskarna.

    ![Diskallokering](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar MABS en volym per datakälla (SQL Server-databas) som används för den första säkerhetskopian. Med den här metoden begränsar LDM Logical Disk Manager () MABS skydd till 300 datakällor (SQL Server-databaser). För att undvika denna begränsning, Välj den **samplacera data i DPM-lagringspoolen**, alternativet. Om du använder det här alternativet använder MABS en enda volym för flera datakällor, vilket gör att MABS att skydda upp till 2000 SQL-databaser.

    Om **Utöka automatiskt volymerna** alternativet har valts, MABS kan kontot för ökad säkerhetskopieringsvolymen när produktionsdata växer. Om **Utöka automatiskt volymerna** alternativet inte är markerat, MABS begränsar säkerhetskopieringslagring som används för att datakällorna i skyddsgruppen.
9. Administratörer ges valet att överföra den här första säkerhetskopieringen manuellt (av nätverk) för att undvika överbelastning av bandbredd eller via nätverket. De kan också konfigurera den tid då den första synkroniseringen kan inträffa. Klicka på **Nästa**.

    ![Metoden för inledande replikering](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databas) från produktionsservern (SQL Server-dator) till MABS. Dessa data kan vara stora och överföra data över nätverket kan överstiga bandbredd. Därför kan administratörer kan välja att överföra den första säkerhetskopian: **manuellt** (med flyttbara media) att undvika överbelastning av bandbredd, eller **automatiskt via nätverket** (på en angiven tid).

    När den första säkerhetskopieringen är klar, är resten av säkerhetskopior säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopieringar brukar vara små och överföras lätt via nätverket.
10. Välj när du vill att konsekvenskontrollen att köra och klicka på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    MABS kan utföra en konsekvenskontroll att kontrollera integriteten hos säkerhetskopieringspunkt. Kontrollsumman för säkerhetskopian på produktionsservern (SQL Server-dator i det här scenariot) och de säkerhetskopierade data för filen på MABS beräknas. Om en konflikt uppstår förutsätts det att den säkerhetskopierade filen på MABS är skadad. MABS hejdar säkerhetskopierade data genom att skicka block som motsvarar den felaktig matchning av kontrollsumma. Konsekvenskontroll är en igen prestandakrävande, har administratörer möjlighet att schemalägga en konsekvenskontroll eller kör automatiskt.
11. Välj databaser att skydda till Azure och klicka på för att ange online-skydd av datakällor **nästa**.

    ![Välj datakällor](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratörer kan välja scheman för säkerhetskopiering och bevarandeprinciper som passar organisationens principer.

    ![Schemat och lagring](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhetskopieringar en gång om dagen med 12:00 och 20: 00 (längst ned på skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några återställningspunkter för kortvarigt skydd på disk för snabb återställning. Dessa återställningspunkter används för ”operativa återställning”. Azure fungerar som en bra extern plats med högre SLA och garanteras tillgänglighet.
    >
    >

    **Bästa praxis**: Kontrollera att Azure-säkerhetskopieringar är schemalagda efter slutförandet av lokal disk-säkerhetskopiering med DPM. Detta gör att den senaste disksäkerhetskopian av som ska kopieras till Azure.

13. Välj principen bevarandeschemat. Information om hur bevarandeprincipen fungerar tillhandahålls på [Använd Azure Backup för att ersätta band infrastruktur artikeln](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopieringar tas en gång om dagen med 12:00 och 20: 00 (längst ned på skärmen) och bevaras i 180 dagar.
    * Säkerhetskopian på lördag kl. 12:00 sparas i 104 veckor
    * Säkerhetskopian på sista lördag kl. 12:00 sparas i 60 månader
    * Säkerhetskopian på sista lördag mars klockan 12:00. sparas för 10 år
14. Klicka på **nästa** och väljer lämpligt alternativ för att överföra den första säkerhetskopian till Azure. Du kan välja **automatiskt via nätverket** eller **Offline säkerhetskopiering**.

    * **Automatiskt via nätverket** överförs säkerhetskopierade data till Azure enligt det schema som valts för säkerhetskopiering.
    * Hur **Offline säkerhetskopiering** fungerar förklaras på [Offline säkerhetskopiering arbetsflöde i Azure Backup](backup-azure-backup-import-export.md).

    Välja relevant överföring mekanism för att skicka den första säkerhetskopian till Azure och klicka på **nästa**.
15. När du granska information om principen i den **sammanfattning** skärmen, klicka på den **Skapa grupp** för att slutföra arbetsflödet. Du kan klicka på den **Stäng** knappen och övervaka jobbförloppet på arbetsytan övervakning.

    ![Skapandet av Skyddsgruppen pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhetskopiering på begäran av en SQL Server-databas
När de föregående stegen skapade en princip för säkerhetskopiering, skapas en ”återställningspunkt” endast när den första säkerhetskopieringen inträffar. I stället för väntar Schemaläggaren kan förbättra, peka stegen nedan utlösaren genereringen av en återställningspunkt manuellt.

1. Vänta tills skyddsgruppens status visas **OK** för databasen innan du skapar återställningspunkten.

    ![Skyddsgruppmedlemmar](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen och välj **Skapa återställningspunkt**.

    ![Skapa Onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **Onlineskydd** i den nedrullningsbara menyn och klicka på **OK**. Detta startar skapandet av en återställningspunkt i Azure.

    ![Skapa återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Du kan visa jobbförloppet på den **övervakning** arbetsyta där du hittar en pågående jobb som det beskrivs i nästa figur.

    ![Övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure
Följande steg krävs för att återställa en skyddad entitet (SQL Server-databas) från Azure.

1. Öppna hanteringskonsolen för DPM-servern. Gå till **Recovery** arbetsyta där du kan se servrarna som säkerhetskopieras av DPM. Bläddra obligatorisk databas (i det här fallet ReportServer$ MSDPM2012). Välj en **återställning från** som slutar med **Online**.

    ![Välj återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på namnet på databasen och på **återställa**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM visar information om återställningspunkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du typ av återställning **Återställ till ursprunglig instans av SQL Server**. Klicka på **Nästa**.

    ![Återställa till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet kan DPM återställning av databasen till en annan SQL Server-instans eller till en nätverksmapp för fristående.
4. I den **Ange återställningsalternativ** skärmen, som du kan välja återställningsalternativ som begränsning av nätverksbandbredd för att begränsa bandbredden som används av återställningen. Klicka på **Nästa**.
5. I den **sammanfattning** ser du alla recovery konfigurationer som hittills. Klicka på **återställa**.

    Status på återställningen visar databas som återställs. Du kan klicka på **Stäng** att stänga guiden och visa förloppet på den **övervakning** arbetsytan.

    ![Starta återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen har slutförts är den återställda databasen program som är konsekventa.

### <a name="next-steps"></a>Nästa steg:
• [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
