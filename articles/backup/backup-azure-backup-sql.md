---
title: "Azure-säkerhetskopiering av SQL Server-arbetsbelastningar med hjälp av DPM | Microsoft Docs"
description: "En introduktion till att säkerhetskopiera SQL Server-databaser med Azure Backup-tjänsten"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli
editor: 
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: c9edc066ea2edc9cd4b8453047d5584a588174dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Säkerhetskopiera SQL Server till Azure som en DPM-arbetsbelastning
Den här artikeln leder dig igenom konfigurationsstegen för säkerhetskopiering av SQL Server-databaser med hjälp av Azure Backup.

Du behöver ett Azure-konto för att säkerhetskopiera SQL Server-databaser till Azure. Om du inte har ett konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

Hantering av säkerhetskopiering av SQL Server-databas till Azure och återställning från Azure omfattar tre steg:

1. Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure.
2. Skapa säkerhetskopior för på-begäran till Azure.
3. Återställ databasen från Azure.

## <a name="before-you-start"></a>Innan du börjar
Innan du börjar bör du se till att alla de [krav](backup-azure-dpm-introduction.md#prerequisites) för att använda Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. Kraven omfattar aktiviteter som: skapa ett säkerhetskopieringsvalv, hämta autentiseringsuppgifter för valv, installera Azure Backup-agenten och registrerar servern i valvet.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Skapa en princip för säkerhetskopiering för att skydda SQL Server-databaser till Azure
1. På DPM-servern klickar du på den **skydd** arbetsytan.
2. Klicka på verktygsfliken **ny** att skapa en ny skyddsgrupp.

    ![Skapa en Skyddsgrupp](./media/backup-azure-backup-sql/protection-group.png)
3. DPM visar startskärmen med vägledning om hur du skapar en **Skyddsgrupp**. Klicka på **Nästa**.
4. Välj **servrar**.

    ![Välj typ av Skyddsgrupp - 'Servrar'](./media/backup-azure-backup-sql/pg-servers.png)
5. Expandera den SQL Server-datorn där databaserna säkerhetskopieras finns. DPM visar olika datakällor som kan säkerhetskopieras från servern. Expandera den **alla SQL-resurser** och väljer du databaserna (i det här fallet vi valt ReportServer$ MSDPM2012 och ReportServer$ MSDPM2012TempDB) som ska säkerhetskopieras. Klicka på **Nästa**.

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

    DPM visar övergripande lagringsutrymme och potentiella diskarna.

    ![Diskallokering](./media/backup-azure-backup-sql/pg-storage.png)

    DPM skapar en volym per datakälla (SQL Server-databas) som används för den första säkerhetskopian som standard. Med den här metoden begränsar LDM Logical Disk Manager () DPM-skydd till 300 datakällor (SQL Server-databaser). För att undvika denna begränsning, Välj den **samplacera data i DPM-lagringspoolen**, alternativet. Om du använder det här alternativet använder DPM en enda volym för flera datakällor, vilket gör att DPM kan skydda upp till 2000 SQL-databaser.

    Om **Utöka automatiskt volymerna** alternativet har valts, DPM kan kontot för ökad säkerhetskopieringsvolymen när produktionsdata växer. Om **Utöka automatiskt volymerna** alternativet inte är markerat, DPM begränsar säkerhetskopieringslagring som används för att datakällorna i skyddsgruppen.
9. Administratörer ges valet att överföra den här första säkerhetskopieringen manuellt (av nätverk) för att undvika överbelastning av bandbredd eller via nätverket. De kan också konfigurera den tid då den första synkroniseringen kan inträffa. Klicka på **Nästa**.

    ![Metoden för inledande replikering](./media/backup-azure-backup-sql/pg-manual.png)

    Den första säkerhetskopian kräver överföring av hela datakällan (SQL Server-databas) från produktionsservern (SQL Server-dator) till DPM-servern. Dessa data kan vara stora och överföra data över nätverket kan överstiga bandbredd. Därför kan administratörer kan välja att överföra den första säkerhetskopian: **manuellt** (med flyttbara media) att undvika överbelastning av bandbredd, eller **automatiskt via nätverket** (på en angiven tid).

    När den första säkerhetskopieringen är klar, är resten av säkerhetskopior säkerhetskopior på den första säkerhetskopian. Inkrementella säkerhetskopieringar brukar vara små och överföras lätt via nätverket.
10. Välj när du vill att konsekvenskontrollen att köra och klicka på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM kan utföra en konsekvenskontroll att kontrollera integriteten hos säkerhetskopieringspunkt. Beräkna kontrollsummor för säkerhetskopian på produktionsservern (SQL Server-dator i det här scenariot) och de säkerhetskopierade data för filen på DPM. Om en konflikt uppstår förutsätts det att den säkerhetskopierade filen i DPM är skadad. DPM hejdar säkerhetskopierade data genom att skicka block som motsvarar den felaktig matchning av kontrollsumma. Konsekvenskontroll är en igen prestandakrävande, har administratörer möjlighet att schemalägga en konsekvenskontroll eller kör automatiskt.
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
