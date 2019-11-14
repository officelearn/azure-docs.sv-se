---
title: Azure Backup för SQL Server-arbetsbelastningar med DPM
description: En introduktion till att säkerhetskopiera SQL Server databaser med hjälp av tjänsten Azure Backup
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 069d1e7cb81fe8d3528b27e676886710f57ea5f1
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074273"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Säkerhetskopiera SQL Server till Azure som en DPM-arbetsbelastning

Den här artikeln vägleder dig genom konfigurations stegen för säkerhets kopiering av SQL Server databaser med hjälp av Azure Backup.

Om du vill säkerhetskopiera SQL Server-databaser till Azure behöver du ett Azure-konto. Om du inte har något konto kan du skapa ett kostnads fritt utvärderings konto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

Hantering av SQL Server databas säkerhets kopiering till Azure och återställning från Azure omfattar tre steg:

1. Skapa en säkerhets kopierings princip för att skydda SQL Server databaser till Azure.
2. Skapa säkerhets kopior på begäran till Azure.
3. Återställ databasen från Azure.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar bör du kontrol lera att alla [krav](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att använda Microsoft Azure Backup för att skydda arbets belastningar har uppfyllts. Förutsättningarna avser uppgifter som: skapa ett säkerhets kopierings valv, Hämta autentiseringsuppgifter för valvet, installera Azure Backup Agent och registrera servern med valvet.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en säkerhets kopierings princip för att skydda SQL Server databaser till Azure

1. Klicka på arbets ytan **skydd** på DPM-servern.
2. I menyfliksområdet verktyg klickar du på **nytt** för att skapa en ny skydds grupp.

    ![Skapa skydds grupp](./media/backup-azure-backup-sql/protection-group.png)
3. DPM visar start skärmen med vägledning om hur du skapar en **skydds grupp**. Klicka på **Nästa**.
4. Välj **servrar**.

    ![Välj typ av skydds grupp-servrar](./media/backup-azure-backup-sql/pg-servers.png)
5. Expandera SQL Server datorn där databaserna som ska säkerhets kopie ras finns. DPM visar olika data källor som kan säkerhets kopie ras från den servern. Expandera **alla SQL-resurser** och välj databaserna (i det här fallet har vi valt ReportServer $ MSDPM2012 och ReportServer $ MSDPM2012TempDB) som ska säkerhets kopie ras. Klicka på **Nästa**.

    ![Välj SQL-databas](./media/backup-azure-backup-sql/pg-databases.png)
6. Ange ett namn på skydds gruppen och markera kryss rutan **Jag vill ha onlineskydd** .

    ![Data skydds metod – kortsiktig disk & online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. På skärmen **Ange kortvariga mål** inkluderar du nödvändiga indata för att skapa säkerhets kopierings punkter på disk.

    Här ser vi att **kvarhållningsintervallet är** inställt på *5 dagar*och att **synkroniseringsfrekvensen** är inställt på var *15: e minut*, vilket är den frekvens som säkerhets kopieringen görs för. **Fullständig snabb säkerhets kopiering** har angetts till *8:00 P. M*.

    ![Kortsiktiga mål](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Vid 8:00 PM (beroende på skärm indata) skapas en säkerhets kopierings punkt varje dag genom att överföra data som har ändrats från föregående dagars 8:00 PM-säkerhetskopia. Den här processen kallas **snabb och fullständig säkerhets kopiering**. Medan transaktions loggarna synkroniseras var 15: e minut, om det finns behov av att återställa databasen på 9:00 PM, skapas punkten genom att spela upp loggarna igen från den senaste fullständiga snabb säkerhets kopierings punkten (20 i det här fallet).
   >
   >

8. Klicka på **Nästa**

    DPM visar det övergripande tillgängliga lagrings utrymmet och den potentiella användningen av disk utrymme.

    ![Diskallokering](./media/backup-azure-backup-sql/pg-storage.png)

    Som standard skapar DPM en volym per data källa (SQL Server databas) som används för den första säkerhets kopian. Med den här metoden begränsar LDM (Logical Disk Manager) DPM-skydd till 300-data källor (SQL Server databaser). Undvik den här begränsningen genom att välja **samplacera data i DPM-lagringspool**, alternativ. Om du använder det här alternativet använder DPM en enda volym för flera data källor, vilket gör att DPM kan skydda upp till 2000 SQL-databaser.

    Om alternativet **för att utöka automatiskt** är markerat kan du med DPM få ett konto för den ökade säkerhets kopierings volymen eftersom produktions data växer. Om alternativet **för att utöka automatiskt** inte är valt, begränsar DPM den lagrings enhet för säkerhets kopior som används för data källorna i skydds gruppen.
9. Administratörer kan välja att överföra denna inledande säkerhets kopiering manuellt (av nätverket) för att undvika överbelastning av bandbredd eller över nätverket. De kan också konfigurera tiden då den första överföringen kan ske. Klicka på **Nästa**.

    ![Metod för inledande replikering](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhets kopian kräver överföring av hela data källan (SQL Server databas) från produktions servern (SQL Server datorn) till DPM-servern. Dessa data kan vara stora och överföring av data över nätverket kan överskrida bandbredden. Av den anledningen kan administratörer välja att överföra den första säkerhets kopian: **manuellt** (med hjälp av flyttbara medier) för att undvika överbelastning av bandbredd eller **automatiskt över nätverket** (vid en viss tidpunkt).

    När den första säkerhets kopieringen är klar är resten av säkerhets kopieringarna stegvisa säkerhets kopior på den första säkerhets kopian. Stegvisa säkerhets kopieringar tenderar att vara små och överförs enkelt över nätverket.
10. Välj när du vill att konsekvens kontrollen ska köras och klicka på **Nästa**.

    ![Konsekvens kontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan utföra en konsekvens kontroll för att kontrol lera säkerhets kopierings platsens integritet. Den beräknar säkerhets kopian för säkerhets kopian på produktions servern (SQL Server datorn i det här scenariot) och de säkerhetskopierade data för den filen på DPM. Om det uppstår en konflikt förutsätts det att den säkerhetskopierade filen på DPM är skadad. DPM reserverar säkerhetskopierade data genom att skicka block som motsvarar matchnings fel för kontroll summan. Eftersom konsekvens kontrollen är en prestanda intensiv åtgärd har administratörer möjlighet att schemalägga konsekvens kontrollen eller köra den automatiskt.
11. Om du vill ange onlineskydd för data källorna väljer du de databaser som ska skyddas till Azure och klickar på **Nästa**.

    ![Välj data källor](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Administratörer kan välja scheman för säkerhets kopiering och bevarande principer som passar deras organisations principer.

    ![Schema och kvarhållning](./media/backup-azure-backup-sql/pg-schedule.png)

    I det här exemplet tas säkerhets kopiorna en gång om dagen på 12:00 PM och 8 PM (nedre delen av skärmen)

    > [!NOTE]
    > Det är en bra idé att ha några kortsiktiga återställnings punkter på disk för snabb återställning. Dessa återställnings punkter används för "drift återställning". Azure fungerar som en annan plats med högre service avtal och garanterad tillgänglighet.
    >
    >

    **Bästa praxis**: se till att Azure-säkerhetskopieringar är schemalagda när du har slutfört säkerhets kopieringen av lokala diskar med DPM. Detta gör att den senaste disk säkerhets kopieringen kan kopieras till Azure.

13. Välj schema för bevarande princip. Information om hur bevarande principen fungerar finns i [använda Azure Backup för att ersätta din band infrastruktur artikel](backup-azure-backup-cloud-as-tape.md).

    ![Bevarandeprincip](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    I det här exemplet:

    * Säkerhets kopieringar tas en gång om dagen på 12:00 PM och 20:00 (nedre delen av skärmen) och bevaras i 180 dagar.
    * Säkerhets kopian på lördag kl. 12:00 sparas i 104 veckor
    * Säkerhets kopian den senaste lördagen klockan 12:00 behålls i 60 månader
    * Säkerhets kopian på sista lördagen i mars kl 12:00 behålls i 10 år
14. Klicka på **Nästa** och välj lämpligt alternativ för att överföra den första säkerhets kopian till Azure. Du kan välja **automatiskt över nätverket** eller **offline-säkerhetskopiering**.

    * **Automatiskt över nätverket** överför säkerhetskopierade data till Azure enligt det schema som valts för säkerhets kopiering.
    * Hur **offline-säkerhetskopiering** fungerar förklaras i [arbets flödet offline i Azure Backup](backup-azure-backup-import-export.md).

    Välj den relevanta överförings mekanismen för att skicka den första säkerhets kopian till Azure och klicka på **Nästa**.
15. När du har granskat princip informationen på sidan **Sammanfattning** klickar du på knappen **Skapa grupp** för att slutföra arbets flödet. Du kan klicka på knappen **Stäng** och övervaka jobb förloppet i arbets ytan övervakning.

    ![Skapande av skydds grupp pågår](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Säkerhets kopiering på begäran av en SQL Server databas

Medan föregående steg skapade en säkerhets kopierings policy skapas en "återställnings punkt" endast när den första säkerhets kopieringen sker. I stället för att vänta på att Schemaläggaren ska sätta igång, utlöser stegen nedan skapandet av en återställnings punkt manuellt.

1. Vänta tills skydds gruppens status visar **OK** för databasen innan du skapar återställnings punkten.

    ![Medlemmar i skydds grupp](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Högerklicka på databasen och välj **skapa återställnings punkt**.

    ![Skapa online återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Välj **onlineskydd** på den nedrullningsbara menyn och klicka på **OK**. Detta startar skapandet av en återställnings punkt i Azure.

    ![Skapa återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Du kan visa jobb förloppet i arbets ytan **övervakning** där du hittar ett pågående jobb som det som visas i nästa figur.

    ![Övervaknings konsol](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Återställa en SQL Server databas från Azure

Följande steg krävs för att återställa en skyddad entitet (SQL Server databas) från Azure.

1. Öppna DPM-serverns hanterings konsol. Gå till **återställnings** arbets ytan där du kan se servrarna som säkerhets kopie ras av DPM. Bläddra igenom den nödvändiga databasen (i det här fallet ReportServer $ MSDPM2012). Välj en **återställning från** tid som slutar med **online**.

    ![Välj återställnings punkt](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. Högerklicka på databas namnet och klicka på **Återställ**.

    ![Återställa från Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM visar information om återställnings punkten. Klicka på **Nästa**. Om du vill skriva över databasen väljer du återställnings typen **Återställ till den ursprungliga instansen av SQL Server**. Klicka på **Nästa**.

    ![Återställ till ursprunglig plats](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    I det här exemplet tillåter DPM återställning av databasen till en annan SQL Server instans eller till en fristående nätverksmapp.
4. På skärmen **Ange återställnings alternativ** kan du välja återställnings alternativ som begränsning av nätverks bandbredd för att begränsa bandbredden som används vid återställning. Klicka på **Nästa**.
5. På sidan **Sammanfattning** visas alla återställnings konfigurationer som du har angett hittills. Klicka på **Återställ**.

    Återställnings status visar den databas som återställs. Du kan klicka på **Stäng** för att stänga guiden och se förloppet i arbets ytan **övervakning** .

    ![Starta återställnings processen](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    När återställningen är klar är den återställda databasen program konsekvent.

## <a name="next-steps"></a>Nästa steg

* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
