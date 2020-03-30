---
title: Säkerhetskopiera en Exchange-server via System Center DPM
description: Lär dig hur du säkerhetskopierar en Exchange-server till Azure Backup med System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 389713767409ff49c6fc83a4d6e8dc3c87272fc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614372"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM

I den hÃ¤r artikeln beskrivs hur du konfigurerar en DPM-server (System Center 2012 R2 Data Protection Manager) fÃ¤r säkerhetskopiera en Microsoft Exchange-server till Azure Backup.  

## <a name="updates"></a>Uppdateringar

Om du vill registrera DPM-servern med Azure Backup måste du installera den senaste samlade uppdateringen för System Center 2012 R2 DPM och den senaste versionen av Azure Backup Agent. Hämta den senaste samlade uppdateringen från [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> De här exemplen i den här artikeln är version 2.0.8719.0 av Azure Backup Agent installerad och Samlad uppdatering 6 installeras på System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Krav

Innan du fortsätter kontrollerar du att alla [förutsättningar](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att använda Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. Dessa förutsättningar omfattar följande:

* Ett valv för säkerhetskopiering på Azure-webbplatsen har skapats.
* Agent- och valvautentiseringsuppgifter har hämtats till DPM-servern.
* Agenten är installerad på DPM-servern.
* Autentiseringsuppgifterna för valvet användes för att registrera DPM-servern.
* Om du skyddar Exchange 2016 uppgraderar du till DPM 2012 R2 UR9 eller senare

## <a name="dpm-protection-agent"></a>DPM-skyddsagent

Så här installerar du DPM-skyddsagenten på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [Konfigurera brandväggsund undantag för agenten](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Installera agenten på Exchange-servern genom att klicka på **Hantering >-agenter > Installera** i DPM Administrator Console. Mer information finns [i Installera DPM-skyddsagenten.](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019)

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange-servern

1. Klicka på **Skydd**i DPM-administratörskonsolen och klicka sedan på **Nytt** i verktygsfliksområdet för att öppna guiden **Skapa nytt skydd.**
2. Klicka **på** **Nästa**på välkomstskärmen i guiden .
3. På skärmen **Välj skyddsgrupptyp** väljer du **Servrar** och klickar på **Nästa**.
4. Markera den Exchange-serverdatabas som du vill skydda och klicka på **Nästa**.

   > [!NOTE]
   > Om du skyddar Exchange 2013 kontrollerar du [förutsättningarna för Exchange 2013](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/dn751029(v=sc.12)).
   >
   >

    I följande exempel väljs Exchange 2010-databasen.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj dataskyddsmetod.

    Namnge skyddsgruppen och välj sedan båda följande alternativ:

   * Jag vill ha kortsiktigt skydd med disk.
   * Jag vill ha online-skydd.
6. Klicka på **Nästa**.
7. Välj alternativet **Kör Eseutil för att kontrollera dataintegritet** om du vill kontrollera integriteten för Exchange Server-databaserna.

    När du har valt det här alternativet körs konsekvenskontroll för säkerhetskopiering på DPM-servern för att undvika I/O-trafik som genereras genom att köra **kommandot eseutil** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese.dll och Eseutil.exe till C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin-katalogen på DPM-servern. Annars utlöses följande fel:  
   > ![eseutil fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klicka på **Nästa**.
9. Markera databasen för **Kopiera säkerhetskopiering**och klicka sedan på **Nästa**.

   > [!NOTE]
   > Om du inte väljer "Fullständig säkerhetskopiering" för minst en DAG-kopia av en databas, kommer loggarna inte att trunkeras.
   >
   >
10. Konfigurera målen för **säkerhetskopiering på kort sikt**och klicka sedan på **Nästa**.
11. Granska det tillgängliga diskutrymmet och klicka sedan på **Nästa**.
12. Välj den tidpunkt då DPM-servern ska skapa den första replikeringen och klicka sedan på **Nästa**.
13. Markera alternativen för konsekvenskontroll och klicka sedan på **Nästa**.
14. Välj den databas som du vill säkerhetskopiera till Azure och klicka sedan på **Nästa**. Ett exempel:

    ![Ange onlineskyddsdata](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup**och klicka sedan på **Nästa**. Ett exempel:

    ![Ange onlineschema för säkerhetskopiering](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Online återställningspunkter baseras på uttryckliga fullständiga återställningspunkter. Därför måste du schemalägga återställningspunkten online efter den tid som har angetts för den uttryckliga fullständiga återställningspunkten.
    >
    >
16. Konfigurera bevarandeprincipen för **Azure Backup**och klicka sedan på **Nästa**.
17. Välj ett onlinereplikeringsalternativ och klicka på **Nästa**.

    Om du har en stor databas kan det ta lång tid innan den första säkerhetskopian skapas över nätverket. Undvik det här problemet genom att skapa en offlinesäkerhetskopia.  

    ![Ange bevarandeprincip online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och klicka sedan på **Skapa grupp**.
19. Klicka på **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databasen

1. Om du vill återställa en Exchange-databas klickar du på **Återställning** i DPM-administratörskonsolen.
2. Leta reda på den Exchange-databas som du vill återställa.
3. Välj en återställningspunkt online i listrutan *återställningstid.*
4. Klicka på **Återställ** om du vill starta **återställningsguiden**.

För återställningspunkter online finns det fem återställningstyper:

* **Återställ till den ursprungliga Exchange Server-platsen:** Data återställs till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange Server:** Data återställs till en annan databas på en annan Exchange-server.
* **Återställ till en återställningsdatabas:** Data återställs till en RDB (Exchange Recovery Database).
* **Kopiera till en nätverksmapp:** Data återställs till en nätverksmapp.
* **Kopiera till band:** Om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigurerad på DPM-servern kopieras återställningspunkten till ett kostnadsfritt band.

    ![Välj replikering online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg

* [Vanliga frågor och svar om Azure-säkerhetskopiering](backup-azure-backup-faq.md)
