---
title: Säkerhetskopiera en Exchange-server till Azure Backup med Azure Backup Server
description: Lär dig hur du säkerhetskopierar en Exchange-server till Azure Backup med hjälp av Azure Backup Server
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 03/24/2017
ms.author: kasinh
ms.openlocfilehash: 40541596b4da9e0590d497785afd7d6d7f4cbcb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641507"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Säkerhetskopiera en Exchange-server till Azure med Azure Backup Server
Den här artikeln beskriver hur du konfigurerar Microsoft Azure Backup Server (MABS) för att säkerhetskopiera en Microsoft Exchange-server till Azure.  

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du fortsätter, kontrollera att Azure Backup Server är [installerad och förberedd](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>MABS-skyddsagenten
Följ dessa steg om du vill installera skyddsagenten MABS på Exchange-servern:

1. Kontrollera att brandväggarna är korrekt konfigurerade. Se [konfigurera brandväggsundantag för agenten](https://technet.microsoft.com/library/Hh758204.aspx).
2. Installera agenten på Exchange-servern genom att klicka på **Management > agenter > installera** i MABS-administratörskonsolen. Se [installera skyddsagenten MABS](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) detaljerade anvisningar.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Skapa en skyddsgrupp för Exchange-servern
1. I MABS-administratörskonsolen klickar du på **Protection**, och klicka sedan på **New** på verktygsfliken för att öppna den **Skapa ny Skyddsgrupp** guiden.
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

    När du har valt det här alternativet säkerhetskopiering konsekvenskontroller ska köras på MABS att undvika att i/o-trafik som genereras genom att köra den **eseutil** på Exchange-servern.

   > [!NOTE]
   > Om du vill använda det här alternativet måste du kopiera filerna Ese.dll och Eseutil.exe till C:\Program Files\Microsoft Azure Backup\DPM\DPM\bin katalog på MAB-servern. I annat fall utlöses följande fel:  
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
12. Väljer du den tid då MAB-servern skapar den inledande replikeringen, och klicka sedan på **nästa**.
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
1. Om du vill återställa en Exchange-databas, klickar du på **Recovery** i MABS-administratörskonsolen.
2. Leta upp den Exchange-databas som du vill återställa.
3. Välj en onlineåterställningspunkt från den *återställningstid* listrutan.
4. Klicka på **återställa** att starta den **Återställningsguiden**.

Onlineåterställningspunkter finns det fem typer av återställning:

* **Återställa till den ursprungliga Exchange-serverplatsen:** Data återställs till den ursprungliga Exchange-servern.
* **Återställa till en annan databas på en Exchange Server:** Data återställs till en annan databas på en annan Exchange server.
* **Återställ till Återställningsdatabas:** Data återställs till en Exchange Recovery databasen (RDB).
* **Kopiera till en nätverksmapp:** Data återställs till en nätverksmapp.
* **Kopiera till band:** Om du har ett bandbibliotek eller en fristående bandenhet ansluten och konfigureras på MABS kan kopieras återställningspunkten till ett ledigt band.

    ![Välj onlinereplikering](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Nästa steg
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
