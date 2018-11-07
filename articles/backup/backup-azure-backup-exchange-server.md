---
title: Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM
description: Lär dig hur du säkerhetskopierar en Exchange-server till Azure Backup med System Center 2012 R2 DPM
services: backup
author: adigan
manager: NKolli1
ms.service: backup
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: adigan
ms.openlocfilehash: 7e95fa0cc6b5f9476df68ab44e5dbc8bb5432d52
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234468"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Säkerhetskopiera en Exchange-server till Azure Backup med System Center 2012 R2 DPM
Den här artikeln beskriver hur du konfigurerar en server för System Center 2012 R2 Data Protection Manager (DPM) för säkerhetskopiering av en Microsoft Exchange-server till Azure Backup.  

## <a name="updates"></a>Uppdateringar
För att kunna registrera DPM-servern med Azure Backup, måste du installera den senaste samlade uppdateringen för System Center 2012 R2 DPM och den senaste versionen av Azure Backup-agenten. Hämta den senaste samlade uppdateringen från den [Microsoft Catalog](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> I exemplen i den här artikeln version 2.0.8719.0 av Azure Backup-agenten är installerad och Samlad uppdatering 6 installeras på System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter kontrollerar du att alla de [krav](backup-azure-dpm-introduction.md#prerequisites-and-limitations) med Microsoft Azure Backup för att skydda arbetsbelastningar har uppfyllts. De här kraven är som följer:

* Ett backup-valv på Azure-webbplatsen har skapats.
* Agent och valvautentiseringsuppgifterna har hämtats till DPM-servern.
* Agenten har installerats på DPM-servern.
* Autentiseringsuppgifterna för valvet har använts för att registrera DPM-servern.
* Om du skyddar Exchange 2016 kan du uppgradera till DPM 2012 R2 UR9 eller senare

## <a name="dpm-protection-agent"></a>DPM-skyddsagenten
Följ dessa steg om du vill installera DPM-skyddsagenten på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [konfigurera brandväggsundantag för agenten](https://technet.microsoft.com/library/Hh758204.aspx).
2. Installera agenten på Exchange-servern genom att klicka på **Management > agenter > installera** i DPM-administratörskonsolen. Se [installera DPM-skyddsagenten](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange-servern
1. I DPM-administratörskonsolen klickar du på **Protection**, och klicka sedan på **New** på verktygsfliken för att öppna den **Skapa ny Skyddsgrupp** guiden.
2. På den **Välkommen** skärmen i guiden klickar du på **nästa**.
3. På den **Välj typ av skyddsgrupp** , väljer **servrar** och klicka på **nästa**.
4. Välj den Exchange server-databas som du vill skydda och klickar på **nästa**.

   > [!NOTE]
   > Om du skyddar Exchange 2013 kan du kontrollera den [krav för Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    I följande exempel, har Exchange 2010-databas valts.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Välj en dataskyddsmetod.

    Namn på skyddsgruppen och välj sedan båda av följande alternativ:

   * Jag vill ha kortvarigt skydd med Disk.
   * Jag vill ha ett onlineskydd.
6. Klicka på **Nästa**.
7. Välj den **kör Eseutil för att kontrollera dataintegriteten** om du vill kontrollera integriteten hos Exchange Server-databaserna.

    När du har valt det här alternativet säkerhetskopiering konsekvenskontroller ska köras på DPM-servern för att undvika att i/o-trafik som genereras genom att köra den **eseutil** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese.dll och Eseutil.exe till katalogen C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin på DPM-servern. I annat fall utlöses följande fel:  
   > ![Eseutil-fel](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Klicka på **Nästa**.
9. Välj databas för **kopiering av säkerhetskopia**, och klicka sedan på **nästa**.

   > [!NOTE]
   > Om du inte väljer ”fullständig säkerhetskopiering” för minst en DAG kopia av en databas måste trunkeras inte loggar.
   >
   >
10. Konfigurera mål för **kortsiktig säkerhetskopiering**, och klicka sedan på **nästa**.
11. Granska tillgängligt diskutrymme och klicka sedan på **nästa**.
12. Välj tiden då DPM-servern kommer att skapa den första replikeringen, och klicka sedan på **nästa**.
13. Välj alternativ för konsekvenskontroll och klicka sedan på **nästa**.
14. Välj den databas som du vill säkerhetskopiera till Azure och klicka sedan på **nästa**. Exempel:

    ![Ange onlineskyddsdata](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definiera schemat för **Azure Backup**, och klicka sedan på **nästa**. Exempel:

    ![Ange onlinesäkerhetskopieringsschema](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Observera onlineåterställningspunkter baseras på express fullständig återställningspunkter. Därför måste du schemalägga online-återställningspunkt när den tid som har angetts för snabb och fullständig återställningspunkt.
    >
    >
16. Konfigurera bevarandeprincipen för **Azure Backup**, och klicka sedan på **nästa**.
17. Välj ett alternativ för onlinereplikering och klicka på **nästa**.

    Om du har en stor databas kan ta det lång tid för den första säkerhetskopieringen skapas via nätverket. För att undvika det här problemet kan skapa du en offlinesäkerhetskopiering.  

    ![Ange princip för onlinebevarande](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Bekräfta inställningarna och klicka sedan på **Skapa grupp**.
19. Klicka på **Stäng**.

## <a name="recover-the-exchange-database"></a>Återställa Exchange-databas
1. Om du vill återställa en Exchange-databas, klickar du på **Recovery** i DPM-administratörskonsolen.
2. Leta upp den Exchange-databas som du vill återställa.
3. Välj en onlineåterställningspunkt från den *återställningstid* listrutan.
4. Klicka på **återställa** att starta den **Återställningsguiden**.

Onlineåterställningspunkter finns det fem typer av återställning:

* **Återställ till den ursprungliga Exchange-serverplatsen:** data kommer att återställas till den ursprungliga Exchange-servern.
* **Återställ till en annan databas på en Exchange-Server:** data kommer att återställas till en annan databas på en annan Exchange server.
* **Återställ till Återställningsdatabas:** data kommer att återställas till en Exchange Recovery databasen (RDB).
* **Kopiera till en nätverksmapp:** data kommer att återställas till en nätverksmapp.
* **Kopiera till band:** om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigurerats på DPM-servern, återställningspunkten som ska kopieras till ett ledigt band.

    ![Välj onlinereplikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
