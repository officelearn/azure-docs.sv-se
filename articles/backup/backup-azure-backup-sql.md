---
title: Azure Backup för SQL Server-arbetsbelastningar med DPM
description: En introduktion till att säkerhetskopiera SQL Server-databaser med Azure Backup-tjänsten
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: d7d94c7b238f8d413d8837c3c34468c6cd653fe3
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300701"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Säkerhetskopiera SQL Server till Azure som en DPM-arbetsbelastning
Den här artikeln leder dig genom konfigurationsstegen för säkerhetskopiering av SQL Server-databaser med hjälp av Azure Backup.

Du behöver ett Azure-konto för att säkerhetskopiera SQL Server-databaser till Azure. Om du inte har ett konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

Hantering av säkerhetskopiering av SQL Server-databas till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure.
2. Skapa säkerhetskopior av på begäran till Azure.
3. Återställ databasen från Azure.

## <a name="before-you-start"></a>Innan du börjar
Innan du börjar måste du kontrollera att alla de [krav](backup-azure-dpm-introduction.md#prerequisites-and-limitations) med Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. Kraven omfattar aktiviteter som: skapa ett säkerhetskopieringsvalv, laddar ned valvautentiseringsuppgifter, installera Azure Backup-agenten och registrera servern med valvet.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure
1. På DPM-servern klickar du på den **Protection** arbetsyta.
2. Klicka på verktygsfliken **New** att skapa en ny skyddsgrupp.

    ![Skapa Skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)
3. DPM visar startskärmen med vägledning om hur du skapar en **Skyddsgrupp**. Klicka på **Nästa**.
4. Välj **servrar**.

    ![Välj typ av Skyddsgrupp - 'Servers ”](./media/backup-azure-backup-sql/pg-servers.png)
5. Expandera den SQL Server-datorn där de databaser som ska säkerhetskopieras finns. DPM visar olika datakällor som kan säkerhetskopieras från den här servern. Expandera den **alla SQL-resurser** och väljer du databaserna (i det här fallet vi valt ReportServer$ MSDPM2012 och ReportServer$ MSDPM2012TempDB) som ska säkerhetskopieras. Klicka på **Nästa**.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)
6. Ange ett namn för skyddsgruppen och välj den **jag vill ha ett Onlineskydd** kryssrutan.

    ![Dataskyddsmetod - kortvarigt disk och Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. I den **ange kortsiktiga mål** skärmen, innehåller de nödvändiga indata för att skapa säkerhetskopieringspunkter till disk.

    Här ser vi att **Kvarhållningsintervall** är inställd på *5 dagar*, **Synkroniseringsfrekvens** är inställd på en gång varje *15 minuter* som är den frekvens för säkerhetskopiering har gjorts. **Snabb och fullständig säkerhetskopiering** är inställd på *8:00 P.M*.

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Kl. 8:00 (enligt skärmen indata) skapas en säkerhetskopieringspunkt varje dag genom att överföra de data som har ändrats från föregående dag 8:00 PM säkerhetskopieringspunkt. Den här processen kallas **Express fullständig säkerhetskopiering**. När transaktionen loggar synkroniseras snabba var femtonde minut, om det är nödvändigt att återställa databasen kl. 9:00 – punkten har skapats genom att spela upp loggar från senaste och fullständiga säkerhetskopieringspunkt (20: 00 i det här fallet).
   >
   >

8. Klicka på **Nästa**

    DPM visar övergripande lagringsutrymme och potentiella diskarna.

    ![Tilldelning av disken](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar DPM en volym per datakälla (SQL Server-databas) som används för den första säkerhetskopian. Med den här metoden kan begränsar LDM Logical Disk Manager () DPM-skydd till 300 datakällor (SQL Server-databaser). Du kan undvika den här begränsningen genom att välja den **samplacera data i DPM-lagringspoolen**, alternativet. Om du använder det här alternativet kan använder DPM en enskild volym för flera datakällor, vilket gör att DPM ska skydda upp till 2 000 SQL-databaser.

    Om **Utöka automatiskt volymerna** alternativet väljs, DPM kan beakta för ökad säkerhetskopieringsvolymen när de produktion växer. Om **Utöka automatiskt volymerna** alternativet inte är markerat, DPM begränsar säkerhetskopieringslagring som används för att datakällorna i skyddsgruppen.
9. Administratörer får välja att överföra den första säkerhetskopieringen manuellt (av network) för att undvika överbelastning av bandbredd eller via nätverket. De kan också konfigurera tiden då den inledande överföringen kan inträffa. Klicka på **Nästa**.

    ![Inledande replikeringsmetod](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databas) från produktionsservern (SQL Server-dator) till DPM-servern. Dessa data kan vara stora och överföra data via nätverket kan överstiga bandbredd. Administratörer kan välja att överföra den första säkerhetskopieringen av den anledningen: **Manuellt** (med flyttbara medier) att undvika överbelastning av bandbredd, eller **automatiskt över nätverket** (vid en viss tidpunkt).

    När den första säkerhetskopieringen har slutförts, är resten av säkerhetskopiorna säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopieringar tenderar att vara liten och överföras enkelt via nätverket.
10. Välj när du vill att konsekvenskontrollen att köra och klicka på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan utföra en konsekvenskontroll att kontrollera integriteten hos säkerhetskopieringspunkten. Kontrollsumman för den säkerhetskopiera filen på produktionsservern (SQL Server-dator i det här scenariot) och de säkerhetskopierade data för filen i DPM beräknas. Om det finns en konflikt förutsätts det att DPM säkerhetskopierade filen är skadad. DPM hejdar säkerhetskopierade data genom att skicka de block som motsvarar den felaktig matchning av kontrollsumma. Eftersom konsekvenskontrollen en prestandakrävande åtgärd, har administratörer möjlighet att schemalägga konsekvenskontrollen eller körs den automatiskt.
11. Om du vill ange online-skydd av datakällor, väljer du databaserna som ska skyddas till Azure och klicka på **nästa**.

    ![Välj datakällor](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratörer kan välja säkerhetskopieringsscheman och lagringsprinciper som passar deras organisationsprinciper.

    ![Schema och kvarhållning](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet kommer säkerhetskopior en gång om dagen vid 12:00 och 20: 00 (längst ned på skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några kortsiktiga återställningspunkter på disk för snabb återställning. Dessa återställningspunkter används för ”återställning”. Azure fungerar som en bra extern plats med högre serviceavtal och garanterad tillgänglighet.
    >
    >

    **Bästa praxis**: Se till att Azure-säkerhetskopieringar är schemalagda på lokal disk säkerhetskopior med hjälp av DPM är färdig. Detta gör att den senaste disksäkerhetskopian som ska kopieras till Azure.

13. Välj bevarandeschema för principen. Information om hur bevarandeprincipen fungerar finns på [Använd Azure Backup för att ersätta band infrastruktur artikeln](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhetskopior tas en gång om dagen vid 12:00 och 20: 00 (längst ned på skärmen) och bevaras i 180 dagar.
    * Säkerhetskopieringen av lördag klockan 12:00. behålls i 104 veckor
    * Säkerhetskopieringen av senaste lördag klockan 12:00. behålls i 60 månader
    * Säkerhetskopieringen av sista lördagen i mars klockan 12:00. sparas i tio år
14. Klicka på **nästa** och väljer lämpligt alternativ för att överföra den första säkerhetskopian till Azure. Du kan välja **automatiskt över nätverket** eller **Offlinesäkerhetskopiering**.

    * **Automatiskt över nätverket** överförs säkerhetskopierade data till Azure enligt det schema som valts för säkerhetskopiering.
    * Hur **Offlinesäkerhetskopiering** fungerar förklaras på [arbetsflöde för Offlinesäkerhetskopiering i Azure Backup](backup-azure-backup-import-export.md).

    Välj relevant dataöverföringsmekanismen för att skicka den första säkerhetskopian till Azure och klicka på **nästa**.
15. När du granska information om principen i den **sammanfattning** skärmen, klickar du på den **Skapa grupp** knappen för att slutföra arbetsflödet. Du kan klicka på den **Stäng** knappen och övervaka jobbförloppet i arbetsytan övervakning.

    ![Skapandet av Skyddsgruppen pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhetskopiering på begäran för en SQL Server-databas
Även om föregående steg skapade du en princip för säkerhetskopiering, skapas en ”återställningspunkt” endast när den första säkerhetskopieringen har körts. I stället väntar scheduler för att sätta peka stegen nedan utlösaren skapandet av en återställning manuellt.

1. Vänta tills skyddsgruppens status visas **OK** för databasen innan du skapar återställningspunkten.

    ![Skyddsgruppmedlemmar](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen Välj **Skapa återställningspunkt**.

    ![Skapa Onlineåterställningspunkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **Onlineskydd** i den nedrullningsbara menyn och klicka på **OK**. Detta startar skapandet av en återställningspunkt i Azure.

    ![Skapa återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Du kan visa jobbförloppet i den **övervakning** arbetsyta där du hittar en pågående jobb som det visas i nästa figur.

    ![Övervakningskonsolen](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server-databas från Azure
Följande steg krävs för att återställa en skyddad entitet (SQL Server-databas) från Azure.

1. Öppna hanteringskonsolen för DPM-servern. Gå till **Recovery** arbetsyta där du kan se servrarna som säkerhetskopieras av DPM. Bläddra i databasen (i det här fallet ReportServer$ MSDPM2012). Välj en **återställning från** som slutar med **Online**.

    ![Välj återställningspunkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på namnet på databasen och på **återställa**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM visar information om återställningspunkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du typ av återställning **Återställ till ursprunglig instans av SQL Server**. Klicka på **Nästa**.

    ![Återställa till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet kan DPM återställning av databasen till en annan SQL Server-instans eller till en nätverksmapp för fristående.
4. I den **Ange återställningsalternativ** skärmen, kan du välja återställningsalternativ som begränsning av nätverksbandbredd för att begränsa bandbredden som används av recovery. Klicka på **Nästa**.
5. I den **sammanfattning** ser du alla recovery-konfigurationer som tillhandahålls hittills. Klicka på **återställa**.

    Status på återställningen visar databas som återställs. Du kan klicka på **Stäng** att Stäng guiden och visa förloppet i den **övervakning** arbetsyta.

    ![Påbörja återställningsprocessen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen programkonsekvent.

### <a name="next-steps"></a>Nästa steg:
• [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
