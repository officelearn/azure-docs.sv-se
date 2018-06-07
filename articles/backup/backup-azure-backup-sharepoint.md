---
title: DPM/Azure Backup server skydd av en SharePoint-servergrupp till Azure
description: Den här artikeln innehåller en översikt över DPM-/ Azure Backup server skydd av en SharePoint-grupp till Azure
services: backup
author: adigan
manager: Nkolli1
ms.service: backup
ms.topic: conceptual
ms.date: 09/29/2016
ms.author: adigan
ms.openlocfilehash: 728850fe70fb3f9e64b0fa25b4ceebb1a1b51cd4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606661"
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Säkerhetskopiera en SharePoint-servergrupp till Azure
Du säkerhetskopiera en SharePoint-grupp till Microsoft Azure med hjälp av System Center Data Protection Manager (DPM) på samma sätt som du säkerhetskopiera andra datakällor. Azure-säkerhetskopiering ger flexibilitet i schemat för säkerhetskopiering att skapa varje dag, varje vecka, månad eller årlig säkerhetskopiering pekar och ger alternativ för kvarhållning av princip för säkerhetskopiering punkter. DPM ger möjlighet att lagra lokal diskkopior för snabb återställningstiden mål (RTO) och för att lagra kopior till Azure för ekonomiska och långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skyddsscenarier
Azure Backup för DPM har stöd för följande scenarier:

| Arbetsbelastning | Version | Distribution av SharePoint | DPM-distributionstyp | DPM – System Center 2012 R2 | Skydd och återställning |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010 SharePoint 2007 SharePoint 3.0 |SharePoint distribueras som en fysisk server eller VMware-Hyper-V virtuell dator <br> -------------- <br> SQL AlwaysOn |Fysisk server eller lokal Hyper-V virtuell dator |Har stöd för säkerhetskopiering till Azure från Samlad uppdatering 5 |Skydda SharePoint-servergruppen återställningsalternativ: återställningsgruppen, databas och fil- eller listobjekt från diskåterställningspunkter.  Servergruppen och databasen återställning från återställningspunkter i Azure. |

## <a name="before-you-start"></a>Innan du börjar
Det finns några saker du måste bekräfta innan du säkerhetskopierar en SharePoint-grupp till Azure.

### <a name="prerequisites"></a>Förutsättningar
Innan du går vidare kontrollerar du att du uppfyller alla de [krav för att använda Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites) att skydda arbetsbelastningar. Vissa krav omfattar: skapa ett säkerhetskopieringsvalv, hämta autentiseringsuppgifter för valv, installera Azure Backup-agenten och registrera DPM-/ Azure Backup-servern med valvet.

### <a name="dpm-agent"></a>DPM-agenten
DPM-agenten måste installeras på den server som kör SharePoint, servrar som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Mer information om hur du ställer in protection agent finns [installationsprogrammet Protection Agent](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Det enda undantaget är att du installerar agenten endast på en enda webbserver för klientdel (WFE). DPM måste agenten på en WFE-server bara ska fungera som startpunkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp
För per 10 miljoner objekt i servergruppen måste det finnas minst 2 GB utrymme på volymen där DPM-mappen finns. Den här utrymme som krävs för kataloggenerering. För DPM ska kunna återställa specifika objekt (webbplatssamlingar, platser, listor, dokumentbibliotek, mappar, enskilda dokument och listobjekt) skapar kataloggenerering en lista över URL: er som ingår i varje innehållsdatabas. Du kan visa listan över URL: er i fönstret återställningsbara objekt i den **Recovery** aktivitetsområdet DPM-administratörskonsolen.

### <a name="sql-server"></a>SQL Server
DPM som körs som LocalSystem-kontot. Om du vill säkerhetskopiera SQL Server-databaser, måste DPM sysadmin-behörigheter på det kontot för den server som kör SQL Server. Ange NT AUTHORITY\SYSTEM till *sysadmin* på den server som kör SQL Server innan du säkerhetskopiera den.

Om SharePoint-servergruppen har SQL Server-databaser som är konfigurerade med SQL Server-alias, installerar du SQL Server-klientkomponenterna på klientwebbservern som DPM ska skydda.

### <a name="sharepoint-server"></a>SharePoint Server
Medan prestanda beror på många faktorer, till exempel storleken på SharePoint-servergruppen, som en allmän vägledning kan en DPM-server skydda en SharePoint-grupp med 25 TB.

### <a name="dpm-update-rollup-5"></a>DPM Samlad uppdatering 5
Du måste installera Samlad uppdatering för DPM 5 eller senare för att börja skydda en SharePoint-grupp till Azure. Samlad uppdatering 5 ger möjlighet att skydda en SharePoint-grupp till Azure om servergruppen har konfigurerats med hjälp av SQL AlwaysOn.
Mer information finns i bloggen post som introducerar [DPM Samlad uppdatering 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Vad som inte stöds
* DPM som skyddar en SharePoint-servergrupp skyddar inte sökindexen eller databaser för tjänsten. Behöver du konfigurera skydd för dessa databaser separat.
* DPM ger inte någon säkerhetskopia av SharePoint SQL Server-databaser som finns på filresurser för skalbar filserver (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd
Innan du kan använda DPM för att skydda SharePoint, måste du konfigurera tjänsten SharePoint VSS Writer (WSS Writer-tjänsten) med hjälp av **ConfigureSharePoint.exe**.

Du kan hitta **ConfigureSharePoint.exe** i mappen [DPM-installationssökvägen] \bin på klientwebbservern. Det här verktyget förser skyddsagenten med autentiseringsuppgifter för SharePoint-servergruppen. Du kan köra den på en enskild WFE-server. Om du har flera WFE-servrar kan du bara välja en när du konfigurerar en skyddsgrupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Att konfigurera tjänsten SharePoint VSS Writer
1. På WFE-servern i en kommandotolk, gå till \bin\ [DPM-installationsplatsen]
2. Ange ConfigureSharePoint - EnableSharePointProtection.
3. Ange administratörsbehörigheterna för servergruppen. Det här kontot ska vara medlem i den lokala administratörsgruppen på WFE-servern. Om servergruppsadministratören är inte en lokal administratör bevilja följande behörigheter på WFE-servern:
   * Tilldela WSS_Admin_WPG-gruppen fullständig behörighet till DPM-mappen (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Bevilja läsbehörighet för WSS_Admin_WPG-gruppen till DPM-registernyckeln (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Du behöver köra ConfigureSharePoint.exe när en ändring i administratörsbehörigheterna för SharePoint-servergruppen.
> 
> 

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av DPM
När du har konfigurerat DPM och SharePoint-servergruppen som tidigare förklarats, skyddas SharePoint av DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Skydda en SharePoint-grupp
1. Från den **skydd** fliken DPM-administratörskonsolen klickar du på **ny**.
    ![Skyddsfliken](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På den **Välj typ av Skyddsgrupp** sida av den **Skapa ny Skyddsgrupp** guiden, Välj **servrar**, och klicka sedan på **nästa**.
   
    ![Välj Skyddsgruppen typ](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. På den **Välj gruppmedlemmar** , väljer du kryssrutan för SharePoint-server som du vill skydda och klickar på **nästa**.
   
    ![Välj gruppmedlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)
   
   > [!NOTE]
   > Du kan se servern i guiden med DPM-agenten är installerad. DPM visar även dess struktur. Eftersom du körde ConfigureSharePoint.exe DPM kommunicerar med tjänsten SharePoint VSS Writer och dess motsvarande SQL Server-databaser och identifierar strukturen för SharePoint-servergruppen, associerade innehållsdatabaserna och motsvarande objekt.
   > 
   > 
4. På den **Välj Dataskyddsmetod** anger du namnet på den **Skyddsgrupp**, och välj *skyddsmetod*. Klicka på **Nästa**.
   
    ![Välj dataskyddsmetod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)
   
   > [!NOTE]
   > Skyddsmetod disk hjälper till att uppfylla kort återställningstiden mål. Azure är en ekonomiska och långsiktigt skydd med band. Mer information finns i [Använd Azure Backup för att ersätta infrastrukturen band](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   > 
   > 
5. På den **ange kortvariga mål** sidan, Välj **Kvarhållningsintervall** och identifiera när du vill att säkerhetskopieringen ska göras.
   
    ![Ange kortsiktiga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)
   
   > [!NOTE]
   > Eftersom återställningen krävs oftast för data som är mindre än fem dagar gammal, vi valt ett Kvarhållningsintervall på fem dagar på disken och kontrollerat att säkerhetskopieringen sker under icke-produktionstider för det här exemplet.
   > 
   > 
6. Granska lagringspoolen diskutrymme som allokerats för skyddsgruppen och sedan **nästa**.
7. För varje skyddsgrupp allokerar DPM diskutrymme för att lagra och hantera repliker. DPM måste nu skapa en kopia av valda data. Välj hur och när du vill använda den replik som har skapats och klicka sedan på **nästa**.
   
    ![Välj metod för skapande av replik](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)
   
   > [!NOTE]
   > Om du vill säkerställa att nätverkstrafiken genomförs inte och välj en tid utanför produktionstider.
   > 
   > 
8. DPM garanterar dataintegriteten genom att utföra en konsekvenskontroll på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvenskontroll eller DPM kan köra konsekvenskontroll på repliken automatiskt varje gång den blir inkonsekvent. Välj önskade alternativ och klicka sedan på **nästa**.
   
    ![Konsekvenskontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På den **ange Onlineskyddsdata** markerar du den SharePoint-grupp som du vill skydda och klicka sedan på **nästa**.
   
    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På den **Ange schema för Online säkerhetskopiering** , väljer önskat schema, och klickar sedan på **nästa**.
    
    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)
    
    > [!NOTE]
    > DPM ger högst två daglig säkerhetskopiering till Azure vid olika tidpunkter. Azure Backup kan också styra hur mycket av WAN-bandbredd som kan användas för säkerhetskopieringar i belastning och låg belastning via [Azure Backup nätverksbegränsning](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).
    > 
    > 
11. Beroende på schemat för säkerhetskopiering som du valde på den **ange bevarandeprincip** väljer bevarandeprincipen för dagliga, veckovisa, månatliga och årliga säkerhetskopiering punkter.
    
    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)
    
    > [!NOTE]
    > DPM använder ett farfar-pappa-son kvarhållning-schema som du kan välja en annan bevarandeprincip för olika tidpunkter som säkerhetskopieringen.
    > 
    > 
12. Liknande till disk, en referens för inledande punkt replik måste skapas i Azure. Välj önskade alternativ att skapa en första säkerhetskopiering till Azure och klicka sedan på **nästa**.
    
    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de angivna inställningarna på den **sammanfattning** , och klickar sedan på **Skapa grupp**. Ett meddelande visas när skyddsgruppen har skapats.
    
    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Återställa SharePoint-objektet från disken med hjälp av DPM
I följande exempel visas den *återställa SharePoint-objektet* har tagits bort av misstag och behöver återställas.
![DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna den **DPM-administratörskonsolen**. Alla SharePoint-servergrupper som skyddas av DPM som visas i den **skydd** fliken.
   
    ![DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Om du vill börja att återskapa objektet, Välj den **Recovery** fliken.
   
    ![DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka SharePoint för *återställa SharePoint-objektet* peka intervall genom att använda en sökning med jokertecken-baserade inom en återställning.
   
    ![DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställningspunkt i sökresultatet, högerklicka på objektet och välj sedan **återställa**.
5. Du kan också bläddra igenom olika återställningspunkter och välja en databas eller objektet om du vill återställa. Välj **datum > återställningstid**, och välj rätt **databasen > SharePoint-servergruppen > återställningspunkt > objektet**.
   
    ![DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
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
9. Ange en SQL Server-instansen mellanlagringsplatsen för att återställa databasen tillfälligt och ger en fristående filresurs på DPM-servern och den server som kör SharePoint om du vill återställa objektet.
   
    ![Mellanlagring Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)
   
    DPM bifogar innehållsdatabasen som är värd för SharePoint-objektet till den tillfälliga SQL-serverinstansen. DPM-servern återställer objektet i innehållsdatabasen och placeras på mellanlagringsplatsen filen på DPM-servern. Det återställda objektet som nu är på mellanlagringsplatsen för DPM-servern måste exporteras till mellanlagringsplatsen på SharePoint-servergruppen.
   
    ![Mellanlagring Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställningsalternativ**, och tillämpa säkerhetsinställningar till SharePoint-servergrupp eller Använd säkerhetsinställningar för återställningspunkten. Klicka på **Nästa**.
    
    ![Återställningsalternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)
    
    > [!NOTE]
    > Du kan välja att begränsa nätverkets bandbredd. Detta minskar inverkan på produktionsservern under produktionstider.
    > 
    > 
11. Granska sammanfattningen och klicka sedan på **återställa** att börja återställa filen.
    
    ![Översikt över återställning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nu välja de **övervakning** fliken i den **DPM-administratörskonsolen** att visa den **Status** av återställningspunkter.
    
    ![Status för återställning](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)
    
    > [!NOTE]
    > Filen återställs nu. Du kan uppdatera SharePoint-webbplatsen om du vill kontrollera den återställda filen.
    > 
    > 

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM
1. Om du vill återställa en SharePoint-innehållsdatabas Bläddra igenom olika återställningspunkter (som visas tidigare) och välj den återställningspunkt som du vill återställa.
   
    ![DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställningspunkt för att visa tillgängliga kataloginformationen för SharePoint.
   
   > [!NOTE]
   > Ingen kataloginformation (metadata) är tillgänglig på DPM-servern eftersom SharePoint-servergruppen skyddas för långsiktig kvarhållning i Azure. Därför när en tidpunkt i SharePoint-innehållsdatabas måste återställas, måste du katalogisera SharePoint-gruppen igen.
   > 
   > 
3. Klicka på **omkatalogiseringen**.
   
    ![DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)
   
    Den **moln katalogisera** status öppnas.
   
    ![DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)
   
    När omkatalogiseras har slutförts ändras statusen till *lyckade*. Klicka på **Stäng**.
   
    ![DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på SharePoint-objektet som visas i DPM **Recovery** fliken för att få innehållsdatabasen strukturen. Högerklicka på objektet och klicka sedan på **återställa**.
   
    ![DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Nu följer den [recovery stegen ovan](#restore-a-sharepoint-item-from-disk-using-dpm) att återställa en SharePoint-innehållsdatabas från disken.

## <a name="faqs"></a>Vanliga frågor och svar
F: vilka versioner av DPM har stöd för SQL Server 2014 och SQL 2012 (SP2)?<br>
S: DPM 2012 R2 med Samlad uppdatering 4 stöder både.

F: kan jag återställa SharePoint-objektet till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn (med skydd på disk)?<br>
S: Ja kan objektet återställas till den ursprungliga SharePoint-webbplatsen.

F: kan jag återställa SharePoint-databasen till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?<br>
S: eftersom SharePoint-databaserna har konfigurerats i SQL AlwaysOn, kan de inte ändras om inte tillgänglighetsgruppen tas bort. Därför kan inte DPM återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg
* Mer information om DPM-skydd av SharePoint - Se [Video serie - DPM-skydd av SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Granska [viktig information för System Center 2012 – Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Granska [viktig information för Data Protection Manager i System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

