---
title: Säkerhetskopiera Exchange Server med Azure Backup Server
description: Lär dig hur du säkerhetskopierar en Exchange-Server till Azure Backup med Azure Backup Server
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: dbd37bbb7418560a0426ed47d7869bf9d949d2e2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617574"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Säkerhetskopiera en Exchange-Server till Azure med Azure Backup Server

Den här artikeln beskriver hur du konfigurerar Microsoft Azure Backup Server (MABS) för att säkerhetskopiera en Microsoft Exchange-Server till Azure.  

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att Azure Backup Server är [installerat och förberett](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>MABS skydds agent

Följ dessa steg om du vill installera MABS-skyddsagenten på Exchange-servern:

1. Kontrol lera att brand väggarna är korrekt konfigurerade. Se [Konfigurera brand Väggs undantag för agenten](h https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Installera agenten på Exchange-servern genom att klicka på **hantering > agenter > installera** i Mabs administratörskonsol. Se [Installera Mabs Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) för detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skydds grupp för Exchange-servern

1. I MABS Administratörskonsol klickar du på **skydd**och sedan på **nytt** i menyfliksområdet för att öppna guiden **Skapa ny skydds grupp** .
2. På **välkomst** skärmen i guiden klickar du på **Nästa**.
3. På skärmen **Välj typ av skydds grupp** väljer du **servrar** och klickar på **Nästa**.
4. Välj den Exchange Server-databas som du vill skydda och klicka på **Nästa**.

   > [!NOTE]
   > Om du skyddar Exchange 2013 kontrollerar du kraven för [exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    I följande exempel är Exchange 2010-databasen vald.

    ![Välj grupp medlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj data skydds metod.

    Ge skydds gruppen ett namn och välj sedan båda följande alternativ:

   * Jag vill ha kortvarigt skydd med disk.
   * Jag vill ha onlineskydd.
6. Klicka på **Next**.
7. Markera alternativet **Kör Eseutil för att kontrol lera data integriteten** om du vill kontrol lera integriteten för Exchange Server-databaserna.

    När du har valt det här alternativet körs konsekvens kontroll av säkerhets kopiering på MABS för att undvika den I/O-trafik som genereras genom att köra **eseutil** -kommandot på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese. dll och eseutil. exe till katalogen C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin på en monoklonal-Server. Annars utlöses följande fel:  
   > ![eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klicka på **Next**.
9. Välj databasen för kopiering av **säkerhets kopia**och klicka sedan på **Nästa**.

   > [!NOTE]
   > Om du inte väljer fullständig säkerhets kopiering för minst en DAG kopia av en databas så trunkeras loggarna inte.
   >
   >
10. Konfigurera målen för **kortsiktig säkerhets kopiering**och klicka sedan på **Nästa**.
11. Granska det tillgängliga disk utrymmet och klicka sedan på **Nästa**.
12. Välj den tid då monoklonal-servern ska skapa den inledande replikeringen och klicka sedan på **Nästa**.
13. Välj alternativ för konsekvens kontroll och klicka sedan på **Nästa**.
14. Välj den databas som du vill säkerhetskopiera till Azure och klicka sedan på **Nästa**. Några exempel:

    ![Ange skydds data online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup**och klicka sedan på **Nästa**. Några exempel:

    ![Ange schemat för onlinesäkerhetskopiering](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Antecknings återställnings punkter baseras på snabba och fullständiga återställnings punkter. Därför måste du schemalägga onlineåterställningspunkt efter den tid som har angetts för den fullständiga snabb återställnings punkten.
    >
    >
16. Konfigurera bevarande principen för **Azure Backup**och klicka sedan på **Nästa**.
17. Välj ett alternativ för replikering online och klicka på **Nästa**.

    Om du har en stor databas kan det ta lång tid för den första säkerhets kopieringen att skapas över nätverket. För att undvika det här problemet kan du skapa en säkerhets kopiering offline.  

    ![Ange bevarande princip online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och klicka sedan på **Skapa grupp**.
19. Klicka på **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databasen

1. Om du vill återställa en Exchange-databas klickar du på **återställning** i MABS-administratörskonsol.
2. Leta upp Exchange-databasen som du vill återställa.
3. Välj en online återställnings punkt i list rutan *återställnings tid* .
4. Klicka på **Återställ** för att starta **återställnings guiden**.

För online-återställnings punkter finns fem återställnings typer:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data kommer att återställas till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange-Server:** Data kommer att återställas till en annan databas på en annan Exchange-Server.
* **Återställ till en återställnings databas:** Data kommer att återställas till en Exchange Recovery-databas (RDB).
* **Kopiera till en nätverksmapp:** Data kommer att återställas till en nätverksmapp.
* **Kopiera till band:** Om du har ett band bibliotek eller en fristående band enhet som är ansluten och konfigurerad på MABS, kopieras återställnings punkten till ett ledigt band.

    ![Välj online-replikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
