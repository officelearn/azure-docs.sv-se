---
title: Säkerhetskopiera en SharePoint-servergrupp till Azure med hjälp av Azure Backup server
description: Säkerhetskopiera och återställa SharePoint-data med hjälp av Azure Backup Server. Den här artikeln innehåller information om du vill konfigurera SharePoint-servergruppen så att önskade data kan lagras i Azure. Du kan återställa skyddade SharePoint-data från disken eller från Azure.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: kasinh
ms.openlocfilehash: 7fa68e11ccac69db9335e589f5048264df9d0a47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60645338"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Säkerhetskopiera en SharePoint-servergrupp till Azure
Du säkerhetskopiera en SharePoint-servergrupp till Microsoft Azure med hjälp av Microsoft Azure Backup Server (MABS) på samma sätt som du säkerhetskopiera andra datakällor. Azure Backup erbjuder flexibilitet i schemat för säkerhetskopiering så att dagligen, varje vecka, månad eller årlig säkerhetskopiering pekar och ger dig alternativ för kvarhållning för olika säkerhetskopieringspunkter. Den omfattar också möjligheten att lagra kopior av lokal disk för snabb återställning-återställningstiden (RTO) och för att lagra kopior till Azure för mer ekonomiskt, långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade scenarier för dataskydd
Azure Backup för DPM har stöd för följande scenarier:

| Arbetsbelastning | Version | SharePoint-distributionen | Skydd och återställning |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint som distribueras som fysisk server eller VMware-Hyper-V-dator <br> -------------- <br> SQL AlwaysOn | Skydda SharePoint-servergruppen återställningsalternativ: Återställningsgruppen, databas och fil- eller listobjekt från diskåterställningspunkter.  Servergruppen och återställning av databaser från återställningspunkter i Azure. |

## <a name="before-you-start"></a>Innan du börjar
Det finns några saker du behöver för att bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du fortsätter måste du se till att du har [installerad och förberedd Azure Backup Server](backup-azure-microsoft-azure-backup.md) att skydda arbetsbelastningar.

### <a name="protection-agent"></a>Skyddsagent
Azure Backup-agenten måste installeras på den server som kör SharePoint, de servrar som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Läs mer om hur du ställer in skyddsagenten [installationsprogrammet Skyddsagenten](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Det enda undantaget är att du installerar agenten endast på en enda webbserver för klientdel (WFE). Azure Backup Server behöver du agenten på WFE-servrar endast som fungerar som ingångspunkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp
För varje 10 miljoner objekt i servergruppen måste det finnas minst 2 GB utrymme på volymen där MABS-mappen finns. Detta utrymme krävs för kataloggenerering. För MABS att återställa specifika objekt (webbplatssamlingar, platser, listor, dokumentbibliotek, mappar, enskilda dokument och listobjekt) kataloggenereringen en lista över URL: er som ingår i varje innehållsdatabas. Du kan visa listan över URL: er i fönstret återställningsbart objekt i den **Recovery** aktivitetsområdet MABS-administratörskonsolen.

### <a name="sql-server"></a>SQL Server
Azure Backup Server körs som LocalSystem-kontot. Om du vill säkerhetskopiera SQL Server-databaser, behöver MABS sysadmin-behörighet på det kontot för den server som kör SQL Server. Ange NT AUTHORITY\SYSTEM *sysadmin* på den server som kör SQL Server innan du säkerhetskopiera den.

Om SharePoint-servergruppen har SQL Server-databaser som är konfigurerade med SQL Server-alias, installerar du SQL Server-klientkomponenterna på klientwebbservern som MABS ska skydda.

### <a name="sharepoint-server"></a>SharePoint Server
Medan prestanda beror på många faktorer, till exempel storleken på SharePoint-servergruppen, som en allmän vägledning kan en MABS skydda en SharePoint-servergrupp med 25 TB.

### <a name="whats-not-supported"></a>Vad som inte stöds
* MABS som skyddar en SharePoint-servergrupp skyddar inte sökindex eller programtjänstdatabaser. Du måste konfigurera skyddet av databaserna separat.
* MABS ger inte säkerhetskopiering av SharePoint SQL Server-databaser som finns på serverresurser för klustrad uppskalningsfilserver (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd
Innan du kan använda MABS för att skydda SharePoint måste du konfigurera tjänsten SharePoint VSS Writer (WSS Writer-tjänst) med hjälp av **ConfigureSharePoint.exe**.

Du kan hitta **ConfigureSharePoint.exe** i mappen [MABS-installationssökväg] \bin på klientwebbservern. Det här verktyget förser skyddsagenten med autentiseringsuppgifter för SharePoint-servergruppen. Du kan köra den på en enskild WFE-server. Om du har flera WFE-servrar kan bara välja en när du konfigurerar en skyddsgrupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurera tjänsten SharePoint VSS Writer
1. Gå till [MABS-installationsplats] \bin\ på WFE-servern i en kommandotolk
2. Ange ConfigureSharePoint - EnableSharePointProtection.
3. Ange administratörsbehörigheterna för servergruppen. Det här kontot ska vara medlem i den lokala administratörsgruppen på WFE-servern. Om servergruppsadministratören är inte en lokal administratör ska du bevilja följande behörigheter på WFE-servern:
   * Bevilja den WSS_Admin_WPG-gruppen fullständig kontrollen till DPM-mappen (% Program Files%\Microsoft Azure Backup\DPM).
   * Tilldela WSS_Admin_WPG-gruppen läsbehörighet för DPM-registernyckeln (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Du måste köra ConfigureSharePoint.exe när det finns en ändring i administratörsbehörigheterna för SharePoint-servergruppen.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av MABS
När du har konfigurerat MABS och SharePoint-servergruppen enligt beskrivningen ovan, kan SharePoint skyddas av MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Skydda en SharePoint-grupp
1. Från den **Protection** fliken MABS-administratörskonsolen klickar du på **New**.
    ![Ny Skyddsfliken](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På den **Välj typ av Skyddsgrupp** för den **Skapa ny Skyddsgrupp** guiden, Välj **servrar**, och klicka sedan på **nästa**.

    ![Välj Skyddsgruppen typ](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. På den **Välj gruppmedlemmar** skärmen, markerar kryssrutan för SharePoint-servern som du vill skydda och klickar på **nästa**.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Med den skyddsagent som installerats, ser du servern i guiden. MABS visar även dess struktur. Eftersom du har kört ConfigureSharePoint.exe MABS kommunicerar med tjänsten SharePoint VSS Writer och dess motsvarande SQL Server-databaser och strukturen för SharePoint-servergruppen, associerade innehållsdatabaserna och alla objekt som motsvarande.
   >
   >
4. På den **Välj Dataskyddsmetod** anger du namnet på den **Skyddsgrupp**, och välj *skyddsmetoder*. Klicka på **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Skyddsmetod disk hjälper till att uppfylla målen för korta tiden för återställning.
   >
   >
5. På den **ange kortsiktiga mål** väljer du föredrar **Kvarhållningsintervall** och identifiera när du vill att säkerhetskopieringen ska göras.

    ![Ange kortvariga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Eftersom återställningen krävs oftast för data som är mindre än fem dagar, vi valt ett Kvarhållningsintervall på fem dagar på disken och sett till att säkerhetskopieringen sker under icke-produktion timmar, i det här exemplet.
   >
   >
6. Granska lagringspoolen diskutrymme som allokerats för skyddsgruppen och klicka sedan **nästa**.
7. För varje skyddsgrupp allokerar MABS diskutrymme för att lagra och hantera repliker. MABS måste nu skapa en kopia av valda data. Välj hur och när du vill att den replik som har skapats och klickar sedan på **nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om du vill kontrollera att nätverkstrafik påverkas inte och välj en tid utanför produktionstider.
   >
   >
8. MABS säkerställer dataintegriteten genom att utföra en konsekvenskontroll på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvenskontroll eller DPM kan köra konsekvenskontroller automatiskt på repliken när den blir inkonsekvent. Välj önskade alternativ och klicka sedan på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På den **ange Onlineskyddsdata** väljer du den SharePoint-grupp som du vill skydda och klicka sedan på **nästa**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På den **Ange schema för Online Backup** sidan, Välj önskat schema och klicka sedan på **nästa**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS ger högst två daglig säkerhetskopiering till Azure från den sedan tillgänglig säkerhetskopieringspunkten för senaste disk. Azure Backup kan också styra hur mycket av WAN-bandbredd som kan användas för säkerhetskopieringar i tider med hög och låg belastning med hjälp av [nätverksbegränsning för Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Beroende på schemat för säkerhetskopiering som du markerat, går den **ange bevarandeprincip** väljer du bevarandeprincip för dagliga, veckovisa, månatliga och årliga säkerhetskopieringspunkter.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS använder ett kvarhållningsschema för farfar-far-son där du kan välja en annan bevarandeprincip för olika säkerhetskopieringspunkter.
    >
    >
12. Liknar disk, en referens för inledande punkt repliken måste skapas i Azure. Välj önskade alternativ för att skapa en första säkerhetskopian till Azure och klicka sedan på **nästa**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de angivna inställningarna på den **sammanfattning** , och klicka sedan på **Skapa grupp**. Ett meddelande visas när skyddsgruppen har skapats.

    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Återställa ett SharePoint-objekt från en disk med hjälp av MABS
I följande exempel visas den *återställa SharePoint-objektet* har tagits bort av misstag och måste återställas.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna den **DPM-administratörskonsolen**. Alla SharePoint-grupper som skyddas av DPM visas i den **Protection** fliken.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Om du vill börja att återskapa objektet, Välj den **Recovery** fliken.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka SharePoint för *återställa SharePoint-objektet* peka intervallet med hjälp av en sökning med jokertecken-baserade inom en återställning.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställningspunkten från sökresultaten, högerklicka på objektet och välj sedan **återställa**.
5. Du kan också bläddra igenom olika återställningspunkter och välja en databas eller objektet om du vill återställa. Välj **datum > återställningstid**, och välj rätt **databas > SharePoint-servergrupp > återställningspunkt > objektet**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **återställa** att öppna den **Återställningsguiden**. Klicka på **Nästa**.

    ![Granska val av återställning](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Välj vilken typ av återställning som du vill utföra och klicka sedan på **nästa**.

    ![Typ av återställning](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Valet av **återställa till ursprungliga** i det här exemplet återställer objekt till den ursprungliga SharePoint-webbplatsen.
   >
   >
8. Välj den **återställningsprocessen** som du vill använda.

   * Välj **återställa utan en återställningsgrupp** om SharePoint-servergruppen inte har ändrats och är samma som den återställningspunkt som håller på att återställas.
   * Välj **återställa med hjälp av en återställningsgrupp** om SharePoint-servergruppen har ändrats sedan återställningspunkten skapades.

     ![Återställningsprocessen](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Ange en SQL Server-instansen mellanlagringsplats för att återställa databasen tillfälligt och innehåller en mellanlagrings filresurs om MABS och den server som kör SharePoint om du vill återställa objektet.

    ![Mellanlagring Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS bifogar innehållsdatabasen som är värd för SharePoint-objektet till den tillfälliga SQL Server-instansen. Från innehållsdatabasen, återställer objektet och placerar den på mellanlagringsplatsen fil på MABS. Det återställda objektet som nu är på mellanlagringsplatsen måste exporteras till mellanlagringsplatsen på SharePoint-servergruppen.

    ![Mellanlagring Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställningsalternativ**, och tillämpa säkerhetsinställningar på SharePoint-servergruppen eller Använd säkerhetsinställningar för återställningspunkten. Klicka på **Nästa**.

    ![Återställningsalternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa nätverkets bandbredd. Detta minskar påverkan på produktionsservern under produktionstider.
    >
    >
11. Granska informationen i sammanfattningen och klicka sedan på **återställa** att börja återställa filen.

    ![Sammanfattning av återställning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Välj nu den **övervakning** fliken i den **MABS-administratörskonsolen** att visa den **Status** av.

    ![Återställningsstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen återställs nu. Du kan uppdatera SharePoint-webbplatsen för att kontrollera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM
1. Om du vill återställa en SharePoint-innehållsdatabas, bläddra igenom olika återställningspunkter (som visas tidigare) och välj den återställningspunkt som du vill återställa.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställningspunkt för att visa tillgängliga kataloginformation för SharePoint.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen skyddas för långsiktig kvarhållning i Azure, finns inga kataloginformation (metadata) på MABS. Därmed kan varje gång en point-in-time SharePoint-innehållsdatabas måste återställas, måste du katalogisera SharePoint-gruppen igen.
   >
   >
3. Klicka på **omkatalogiserar**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Den **molnet katalogisera om** status öppnas.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När katalogiserar är klar ändras statusen till *lyckades*. Klicka på **Stäng**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på SharePoint-objekt som visas i MABS **Recovery** fliken för att få innehållsdatabasen strukturen. Högerklicka på objektet och klicka sedan på **återställa**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nu Följ återställningsstegen tidigare i den här artikeln för att återställa en SharePoint-innehållsdatabas från disken.

## <a name="faqs"></a>Vanliga frågor och svar
F: Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn (med skydd på disk)?<br>
S: Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

F: Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?<br>
S: Eftersom SharePoint-databaserna har konfigurerats i SQL AlwaysOn, kan inte de ändras om inte tillgänglighetsgruppen tas bort. Därför kan inte MABS återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Se den [säkerhetskopiera Exchange-servern](backup-azure-exchange-mabs.md) artikeln.
Se den [säkerhetskopiera SQL Server](backup-azure-sql-mabs.md) artikeln.
