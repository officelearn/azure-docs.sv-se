---
title: Säkerhetskopiera en Exchange-Server via System Center DPM
description: Lär dig hur du säkerhetskopierar en Exchange-Server till Azure Backup med System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 389713767409ff49c6fc83a4d6e8dc3c87272fc9
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614372"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM

Den här artikeln beskriver hur du konfigurerar en DPM-server (System Center 2012 R2 Data Protection Manager) att säkerhetskopiera en Microsoft Exchange-Server till Azure Backup.  

## <a name="updates"></a>Uppdateringar

För att kunna registrera DPM-servern med Azure Backup måste du installera den senaste samlade uppdateringen för System Center 2012 R2 DPM och den senaste versionen av Azure Backup agenten. Hämta den senaste samlade uppdateringen från [Microsoft-katalogen](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> I exemplen i den här artikeln installeras version 2.0.8719.0 av Azure Backup-agenten och Samlad uppdatering 6 är installerad på System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter bör du kontrol lera att alla [krav](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att använda Microsoft Azure Backup för att skydda arbets belastningar har uppfyllts. Följande krav är uppfyllda:

* Ett säkerhets kopierings valv på Azure-webbplatsen har skapats.
* Autentiseringsuppgifter för agent och valv har laddats ned till DPM-servern.
* Agenten är installerad på DPM-servern.
* Autentiseringsuppgifterna för valvet användes för att registrera DPM-servern.
* Om du skyddar Exchange 2016 ska du uppgradera till DPM 2012 R2 UR9 eller senare

## <a name="dpm-protection-agent"></a>DPM-skyddsagenten

Följ dessa steg om du vill installera DPM-skyddsagenten på Exchange-servern:

1. Kontrol lera att brand väggarna är korrekt konfigurerade. Se [Konfigurera brand Väggs undantag för agenten](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Installera agenten på Exchange-servern genom att klicka på **hantering > agenter > installera** i DPM-administratörskonsol. Se [Installera DPM-skyddsagenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) för detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skydds grupp för Exchange-servern

1. Klicka på **skydd**i DPM-administratörskonsol och klicka sedan på **nytt** i menyfliksområdet verktyg för att öppna guiden **Skapa ny skydds grupp** .
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

    När du har valt det här alternativet körs konsekvens kontroll av säkerhets kopiering på DPM-servern för att undvika den I/O-trafik som genereras genom att köra **eseutil** -kommandot på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese. dll och eseutil. exe till katalogen C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin på DPM-servern. Annars utlöses följande fel:  
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
12. Välj den tid då DPM-servern ska skapa den inledande replikeringen och klicka sedan på **Nästa**.
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

1. Om du vill återställa en Exchange-databas klickar du på **återställning** i DPM-administratörskonsol.
2. Leta upp Exchange-databasen som du vill återställa.
3. Välj en online återställnings punkt i list rutan *återställnings tid* .
4. Klicka på **Återställ** för att starta **återställnings guiden**.

För online-återställnings punkter finns fem återställnings typer:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data kommer att återställas till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange-Server:** Data kommer att återställas till en annan databas på en annan Exchange-Server.
* **Återställ till en återställnings databas:** Data kommer att återställas till en Exchange Recovery-databas (RDB).
* **Kopiera till en nätverksmapp:** Data kommer att återställas till en nätverksmapp.
* **Kopiera till band:** Om du har ett band bibliotek eller en fristående band enhet som är ansluten och konfigurerad på DPM-servern, kopieras återställnings punkten till ett ledigt band.

    ![Välj online-replikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
