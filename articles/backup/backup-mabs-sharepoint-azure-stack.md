---
title: Säkerhetskopiera en SharePoint-grupp på Azure-stacken
description: Använda Azure Backup Server för att säkerhetskopiera och återställa SharePoint-data på Azure-stacken. Den här artikeln innehåller information för att konfigurera SharePoint-servergruppen så att önskade data kan lagras i Azure. Du kan återställa skyddade SharePoint-data från disken eller från Azure.
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: 309e817426fff1eb877ab02ae9aa16ddc8f5cf16
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751905"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Säkerhetskopiera en SharePoint-grupp på Azure-stacken
Du säkerhetskopiera en SharePoint-grupp på Azure-stacken till Microsoft Azure med hjälp av Microsoft Azure Backup Server (MABS) på samma sätt som du säkerhetskopiera andra datakällor. Azure-säkerhetskopiering ger flexibilitet i schemat för säkerhetskopiering att skapa varje dag, varje vecka, månad eller årlig säkerhetskopiering pekar och ger alternativ för kvarhållning av princip för säkerhetskopiering punkter. Det ger också möjlighet att lagra lokal diskkopior för snabb återställningstiden mål (RTO) och för att lagra kopior till Azure för ekonomiska och långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skyddsscenarier
Azure-säkerhetskopiering av MABS stöder följande scenarion:

| Arbetsbelastning | Version | Distribution av SharePoint | Skydd och återställning |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010 SharePoint 2007 SharePoint 3.0 |SharePoint distribueras som en fysisk server eller VMware-Hyper-V virtuell dator <br> -------------- <br> SQL AlwaysOn | Skydda SharePoint-servergruppen återställningsalternativ: återställningsgruppen, databas och fil- eller listobjekt från diskåterställningspunkter.  Servergruppen och databasen återställning från återställningspunkter i Azure. |

## <a name="before-you-start"></a>Innan du börjar
Det finns några saker du måste bekräfta innan du säkerhetskopierar en SharePoint-grupp till Azure.

### <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter, kontrollera att du har [installerad och förberedda Azure Backup Server](backup-mabs-install-azure-stack.md) att skydda arbetsbelastningar.

### <a name="protection-agent"></a>Skyddsagent
Azure Backup-agenten måste installeras på den server som kör SharePoint, servrar som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Mer information om hur du ställer in protection agent finns [installationsprogrammet Protection Agent](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Det enda undantaget är att du installerar agenten endast på en enda webbserver för klientdel (WFE). Azure Backup-Server måste agenten på en WFE-server bara ska fungera som startpunkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp
För per 10 miljoner objekt i servergruppen måste det finnas minst 2 GB utrymme på volymen där MABS mappen finns. Den här utrymme som krävs för kataloggenerering. För MABS återställa specifika objekt (webbplatssamlingar, platser, listor, dokumentbibliotek, mappar, enskilda dokument och listobjekt) skapas kataloggenerering en lista över URL: er som ingår i varje innehållsdatabas. Du kan visa listan över URL: er i fönstret återställningsbara objekt i den **Recovery** aktivitetsområdet MABS-administratörskonsolen.

### <a name="sql-server"></a>SQL Server
Azure Backup-servern körs som LocalSystem-kontot. Om du vill säkerhetskopiera SQL Server-databaser, måste MABS sysadmin-behörigheter på det kontot för den server som kör SQL Server. Ange NT AUTHORITY\SYSTEM till *sysadmin* på den server som kör SQL Server innan du säkerhetskopiera den.

Om SharePoint-servergruppen har SQL Server-databaser som är konfigurerade med SQL Server-alias, installerar du SQL Server-klientkomponenterna på klientwebbservern som MABS ska skydda.

### <a name="whats-not-supported"></a>Vad som inte stöds
* MABS som skyddar en SharePoint-servergrupp skyddar inte sökindexen eller databaser för tjänsten. Behöver du konfigurera skydd för dessa databaser separat.
* MABS ger inte någon säkerhetskopia av SharePoint SQL Server-databaser som finns på filresurser för skalbar filserver (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd
Innan du kan använda MABS för att skydda SharePoint, måste du konfigurera tjänsten SharePoint VSS Writer (WSS Writer-tjänsten) med hjälp av **ConfigureSharePoint.exe**.

Du kan hitta **ConfigureSharePoint.exe** i mappen [MABS installationssökväg] \bin på klientwebbservern. Det här verktyget förser skyddsagenten med autentiseringsuppgifter för SharePoint-servergruppen. Du kan köra den på en enskild WFE-server. Om du har flera WFE-servrar kan du bara välja en när du konfigurerar en skyddsgrupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Att konfigurera tjänsten SharePoint VSS Writer
1. Gå till [MABS installationsplats] \bin\ på WFE-servern i en kommandotolk
2. Ange ConfigureSharePoint - EnableSharePointProtection.
3. Ange administratörsbehörigheterna för servergruppen. Det här kontot ska vara medlem i den lokala administratörsgruppen på WFE-servern. Om servergruppsadministratören är inte en lokal administratör bevilja följande behörigheter på WFE-servern:
   * Tilldela WSS_Admin_WPG-gruppen fullständig behörighet till DPM-mappen (% Program Files%\Microsoft Azure Backup\DPM).
   * Bevilja läsbehörighet för WSS_Admin_WPG-gruppen till DPM-registernyckeln (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Du behöver köra ConfigureSharePoint.exe när en ändring i administratörsbehörigheterna för SharePoint-servergruppen.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av MABS
När du har konfigurerat MABS och SharePoint-servergruppen som tidigare förklarats, skyddas SharePoint av MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Skydda en SharePoint-grupp
1. Från den **skydd** fliken MABS-administratörskonsolen klickar du på **ny**.
    ![Skyddsfliken](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På den **Välj typ av Skyddsgrupp** sida av den **Skapa ny Skyddsgrupp** guiden, Välj **servrar**, och klicka sedan på **nästa**.

    ![Välj Skyddsgruppen typ](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. På den **Välj gruppmedlemmar** , väljer du kryssrutan för SharePoint-server som du vill skydda och klickar på **nästa**.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > Du kan se servern i guiden med skyddsagenten är installerad. MABS visar även dess struktur. Eftersom du körde ConfigureSharePoint.exe MABS kommunicerar med tjänsten SharePoint VSS Writer och dess motsvarande SQL Server-databaser och identifierar strukturen för SharePoint-servergruppen, associerade innehållsdatabaserna och motsvarande objekt.
   >
   >
4. På den **Välj Dataskyddsmetod** anger du namnet på den **Skyddsgrupp**, och välj *skyddsmetod*. Klicka på **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Skyddsmetod disk hjälper till att uppfylla kort återställningstiden mål.
   >
   >
5. På den **ange kortvariga mål** sidan, Välj **Kvarhållningsintervall** och identifiera när du vill att säkerhetskopieringen ska göras.

    ![Ange kortsiktiga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Eftersom återställningen krävs oftast för data som är mindre än fem dagar gammal, vi valt ett Kvarhållningsintervall på fem dagar på disken och kontrollerat att säkerhetskopieringen sker under icke-produktionstider för det här exemplet.
   >
   >
6. Granska lagringspoolen diskutrymme som allokerats för skyddsgruppen och sedan **nästa**.
7. För varje skyddsgrupp allokerar MABS diskutrymme för att lagra och hantera repliker. MABS måste nu skapa en kopia av valda data. Välj hur och när du vill använda den replik som har skapats och klicka sedan på **nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om du vill säkerställa att nätverkstrafiken genomförs inte och välj en tid utanför produktionstider.
   >
   >
8. MABS garanterar dataintegriteten genom att utföra en konsekvenskontroll på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvenskontroll eller DPM kan köra konsekvenskontroll på repliken automatiskt varje gång den blir inkonsekvent. Välj önskade alternativ och klicka sedan på **nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På den **ange Onlineskyddsdata** markerar du den SharePoint-grupp som du vill skydda och klicka sedan på **nästa**.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På den **Ange schema för Online säkerhetskopiering** , väljer önskat schema, och klickar sedan på **nästa**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS ger högst två daglig säkerhetskopiering till Azure från den sedan tillgänglig säkerhetskopieringspunkt för senaste disk. Azure Backup kan också styra hur mycket av WAN-bandbredd som kan användas för säkerhetskopieringar i belastning och låg belastning via [Azure Backup nätverksbegränsning](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Beroende på schemat för säkerhetskopiering som du valde på den **ange bevarandeprincip** väljer bevarandeprincipen för dagliga, veckovisa, månatliga och årliga säkerhetskopiering punkter.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS använder ett farfar-pappa-son kvarhållning-schema som du kan välja en annan bevarandeprincip för olika tidpunkter som säkerhetskopieringen.
    >
    >
12. Liknande till disk, en referens för inledande punkt replik måste skapas i Azure. Välj önskade alternativ att skapa en första säkerhetskopiering till Azure och klicka sedan på **nästa**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de angivna inställningarna på den **sammanfattning** , och klickar sedan på **Skapa grupp**. Ett meddelande visas när skyddsgruppen har skapats.

    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Återställa SharePoint-objektet från disken med hjälp av MABS
I följande exempel visas den *återställa SharePoint-objektet* har tagits bort av misstag och behöver återställas.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna den **DPM-administratörskonsolen**. Alla SharePoint-servergrupper som skyddas av DPM som visas i den **skydd** fliken.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Om du vill börja att återskapa objektet, Välj den **Recovery** fliken.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka SharePoint för *återställa SharePoint-objektet* peka intervall genom att använda en sökning med jokertecken-baserade inom en återställning.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställningspunkt i sökresultatet, högerklicka på objektet och välj sedan **återställa**.
5. Du kan också bläddra igenom olika återställningspunkter och välja en databas eller objektet om du vill återställa. Välj **datum > återställningstid**, och välj rätt **databasen > SharePoint-servergruppen > återställningspunkt > objektet**.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **återställa** att öppna den **Återställningsguiden**. Klicka på **Nästa**.

    ![Granska val av återställning](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Välj typ av återställning som du vill utföra och klicka sedan på **nästa**.

    ![Typ av återställning](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Valet av **återställa till ursprungliga** i exempel återställer objekt till den ursprungliga SharePoint-webbplatsen.
   >
   >
8. Välj den **återställningsprocessen** som du vill använda.

   * Välj **återställa utan en återställningsgrupp** om SharePoint-servergruppen inte har ändrats och är samma som den återställningspunkt som återställs.
   * Välj **återställa en återställningsgrupp** om SharePoint-servergruppen har ändrats sedan återställningspunkten skapades.

     ![Återställningsprocessen](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Ange en SQL Server-instansen mellanlagringsplatsen för att återställa databasen tillfälligt och ger en fristående filresurs MABS och den server som kör SharePoint om du vill återställa objektet.

    ![Mellanlagring Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS bifogar innehållsdatabasen som är värd för SharePoint-objektet till den tillfälliga SQL-serverinstansen. I innehållsdatabasen återställer objektet och placeras på mellanlagringsplatsen fil på MABS. Det återställda objektet som nu är på mellanlagringsplatsen måste exporteras till mellanlagringsplatsen på SharePoint-servergruppen.

    ![Mellanlagring Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställningsalternativ**, och tillämpa säkerhetsinställningar till SharePoint-servergrupp eller Använd säkerhetsinställningar för återställningspunkten. Klicka på **Nästa**.

    ![Återställningsalternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa nätverkets bandbredd. Detta minskar inverkan på produktionsservern under produktionstider.
    >
    >
11. Granska sammanfattningen och klicka sedan på **återställa** att börja återställa filen.

    ![Översikt över återställning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nu välja de **övervakning** fliken i den **MABS administratörskonsolen** att visa den **Status** av återställningspunkter.

    ![Status för återställning](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen återställs nu. Du kan uppdatera SharePoint-webbplatsen om du vill kontrollera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM
1. Om du vill återställa en SharePoint-innehållsdatabas Bläddra igenom olika återställningspunkter (som visas tidigare) och välj den återställningspunkt som du vill återställa.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställningspunkt för att visa tillgängliga kataloginformationen för SharePoint.

   > [!NOTE]
   > Ingen kataloginformation (metadata) är tillgängligt på MABS eftersom SharePoint-servergruppen skyddas för långsiktig kvarhållning i Azure. Därför när en tidpunkt i SharePoint-innehållsdatabas måste återställas, måste du katalogisera SharePoint-gruppen igen.
   >
   >
3. Klicka på **omkatalogiseringen**.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Den **moln katalogisera** status öppnas.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När omkatalogiseras har slutförts ändras statusen till *lyckade*. Klicka på **Stäng**.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på SharePoint-objektet som visas i MABS **Recovery** fliken för att få innehållsdatabasen strukturen. Högerklicka på objektet och klicka sedan på **återställa**.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nu följer den [recovery stegen ovan](#restore-a-sharepoint-item-from-disk-using-dpm) att återställa en SharePoint-innehållsdatabas från disken.

## <a name="faqs"></a>Vanliga frågor och svar
F: kan jag återställa SharePoint-objektet till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn (med skydd på disk)?<br>
S: Ja kan objektet återställas till den ursprungliga SharePoint-webbplatsen.

F: kan jag återställa SharePoint-databasen till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?<br>
S: eftersom SharePoint-databaserna har konfigurerats i SQL AlwaysOn, kan de inte ändras om inte tillgänglighetsgruppen tas bort. Därför kan inte MABS återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Finns det [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) artikel.
Finns det [säkerhetskopiering SQL Server på Azure-stacken](backup-mabs-sql-azure-stack.md) artikel.
