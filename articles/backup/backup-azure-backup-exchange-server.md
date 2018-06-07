---
title: Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM
description: Lär dig hur du säkerhetskopierar en Exchange-server till Azure Backup med System Center 2012 R2 DPM
services: backup
author: MaanasSaran
manager: NKolli1
ms.service: backup
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: adigan
ms.openlocfilehash: 4edec499d12261add398e5a9297f039ecfb252e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605108"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM
Den här artikeln beskriver hur du konfigurerar en server för System Center 2012 R2 Data Protection Manager (DPM) att säkerhetskopiera en Microsoft Exchange-server till Azure Backup.  

## <a name="updates"></a>Uppdateringar
För att kunna registrera DPM-servern med Azure Backup, måste du installera den senaste samlade uppdateringen för System Center 2012 R2 DPM och den senaste versionen av Azure Backup Agent. Hämta den senaste samlade uppdateringen från den [Microsoft Catalog](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> I exemplen i den här artikeln, version 2.0.8719.0 av Azure Backup-agenten är installerad och Samlad uppdatering 6 installeras på System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter, kontrollera att alla de [krav](backup-azure-dpm-introduction.md#prerequisites) för att använda Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. Dessa krav är följande:

* Ett säkerhetskopieringsvalv på Azure-webbplatsen har skapats.
* Agent och valvautentiseringsuppgifterna har laddats ned till DPM-servern.
* Agenten har installerats på DPM-servern.
* Valvautentiseringsuppgifter som användes för att registrera DPM-servern.
* Om du skyddar Exchange 2016 kan du uppgradera till DPM 2012 R2 UR9 eller senare

## <a name="dpm-protection-agent"></a>DPM-skyddsagenten
Följ dessa steg om du vill installera DPM-skyddsagenten på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [konfigurera brandväggsundantag för agenten](https://technet.microsoft.com/library/Hh758204.aspx).
2. Installera agenten på Exchange-servern genom att klicka på **Management > agenter > installera** i DPM-administratörskonsolen. Se [installera DPM-skyddsagenten](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange server
1. I DPM-administratörskonsolen klickar du på **skydd**, och klicka sedan på **ny** på verktygsfliken för att öppna den **Skapa ny Skyddsgrupp** guiden.
2. På den **Välkommen** skärmen i guiden klickar du på **nästa**.
3. På den **Välj typ av skyddsgrupp** , väljer **servrar** och på **nästa**.
4. Välj den Exchange server-databas som du vill skydda och klicka på **nästa**.

   > [!NOTE]
   > Om du skyddar Exchange 2013, kontrollera den [krav för Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    I följande exempel har Exchange 2010-databas valts.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj dataskyddsmetod.

    Namn på skyddsgrupp och välj sedan båda av följande alternativ:

   * Jag vill ha kortvarigt skydd med Disk.
   * Jag vill använda onlineskydd.
6. Klicka på **Nästa**.
7. Välj den **kör Eseutil för att kontrollera dataintegriteten** om du vill kontrollera integriteten hos Exchange Server-databaser.

    När du har valt det här alternativet säkerhetskopiering konsekvenskontroll körs på DPM-servern för att undvika att i/o-trafik som genereras genom att köra den **eseutil** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese.dll och Eseutil.exe till katalogen C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin på DPM-servern. Annars utlöses av följande fel:  
   > ![Eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klicka på **Nästa**.
9. Välj databas för **kopiering av säkerhetskopia**, och klicka sedan på **nästa**.

   > [!NOTE]
   > Om du inte väljer ”fullständiga säkerhetskopieringen” för minst en DAG kopia av en databas trunkeras inte loggar.
   >
   >
10. Konfigurera mål för **kortsiktig säkerhetskopiering**, och klicka sedan på **nästa**.
11. Granska tillgängligt diskutrymme och klicka sedan på **nästa**.
12. Välj den tid då DPM-servern skapar den inledande replikeringen, och klicka sedan på **nästa**.
13. Välj alternativ för konsekvenskontroll och klicka sedan på **nästa**.
14. Välj den databas som du vill säkerhetskopiera till Azure och klicka sedan på **nästa**. Exempel:

    ![Ange onlineskyddsdata](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup**, och klicka sedan på **nästa**. Exempel:

    ![Ange onlinesäkerhetskopieringsschema](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observera onlineåterställningspunkter baseras på snabb och fullständig återställningspunkter. Därför måste du schemalägga onlineåterställningspunkt efter den tid som har angetts för snabb och fullständig återställningspunkt.
    >
    >
16. Konfigurera bevarandeprincipen för **Azure Backup**, och klicka sedan på **nästa**.
17. Välj ett alternativ för onlinereplikeringsmetoden och klicka på **nästa**.

    Om du har en stor databas, kan det ta lång tid för den första säkerhetskopian ska skapas via nätverket. För att undvika det här problemet kan skapa du en offlinesäkerhetskopiering.  

    ![Ange bevarandeprincip](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och klicka sedan på **Skapa grupp**.
19. Klicka på **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databasen
1. Om du vill återställa en Exchange-databas, klickar du på **Recovery** i DPM-administratörskonsolen.
2. Leta reda på Exchange-databasen som du vill återställa.
3. Välj en onlineåterställningspunkt från den *återställningstid* listrutan.
4. Klicka på **återställa** att starta den **Återställningsguiden**.

Onlineåterställningspunkter finns det fem typer av återställning:

* **Återställ till den ursprungliga Exchange-serverplatsen:** data kommer att återställas till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange Server:** data kommer att återställas till en annan databas på en annan Exchange server.
* **Återställ till Återställningsdatabas:** data kommer att återställas till en Exchange Recovery databasen (RDB).
* **Kopiera till en nätverksmapp:** data kommer att återställas till en nätverksmapp.
* **Kopiera till band:** om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigurerats på DPM-servern återställningspunkten ska kopieras till ett ledigt band.

    ![Välj online replikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
