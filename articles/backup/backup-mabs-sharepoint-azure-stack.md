---
title: Säkerhetskopiera en SharePoint-grupp på Azure Stack
description: Använd Azure Backup Server för att säkerhetskopiera och återställa dina SharePoint-data på Azure Stack. Den här artikeln innehåller information om hur du konfigurerar SharePoint-servergruppen så att önskade data kan lagras i Azure. Du kan återställa skyddade SharePoint-data från disk eller från Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2018
ms.author: dacurwin
ms.openlocfilehash: 8200bfb4e99d7847dc1661a4258b2171f24a9aed
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210265"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Säkerhetskopiera en SharePoint-grupp på Azure Stack
Du säkerhetskopierar en SharePoint-grupp på Azure Stack till Microsoft Azure genom att använda Microsoft Azure Backup Server (MABS) på ett mycket sätt som du säkerhetskopierar andra data källor. Azure Backup ger flexibilitet i schemat för säkerhets kopiering för att skapa dagliga, vecko Visa eller årliga säkerhets kopierings punkter och ger dig bevarande princip alternativ för olika säkerhets kopierings punkter. Det ger också möjlighet att lagra lokala disk kopior för snabba återställnings mål (RTO) och för att lagra kopior till Azure för ekonomisk, långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skydds scenarier
Azure Backup för MABS har stöd för följande scenarier:

| Arbetsbelastning | Version | SharePoint-distribution | Skydd och återställning |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint distribuerad som en Azure Stack virtuell dator <br> -------------- <br> SQL AlwaysOn | Skydda SharePoint-gruppens återställnings alternativ: Återställnings grupp, databas och fil-eller List objekt från disk återställnings punkter.  Server grupp och databas återställning från Azure-återställnings punkter. |

## <a name="before-you-start"></a>Innan du börjar
Det finns några saker du behöver bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Förutsättningar
Innan du fortsätter måste du kontrol lera att du har [installerat och förberett Azure Backup Server](backup-mabs-install-azure-stack.md) för att skydda arbets belastningar.

### <a name="protection-agent"></a>Skydds agent
Azure Backup agenten måste vara installerad på den server som kör SharePoint, de servrar som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Mer information om hur du konfigurerar skydds agenten finns i [installations skydds agenten](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Ett undantag är att du bara installerar agenten på en enda front webb server (WFE). Azure Backup Server behöver agenten på en WFE-server som endast fungerar som start punkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp
För varje 10 000 000-objekt i Server gruppen måste det finnas minst 2 GB utrymme på den volym där MABS-mappen finns. Det här utrymmet krävs för att skapa kataloger. För att MABS ska kunna återställa specifika objekt (webbplats samlingar, webbplatser, listor, dokument bibliotek, mappar, enskilda dokument och list objekt) skapar katalog skapande en lista över de URL: er som finns i varje innehålls databas. Du kan visa listan över URL: er i fönstret återställnings Bart objekt i återställnings aktivitets avsnittet i Mabs administratörskonsol.

### <a name="sql-server"></a>SQL Server
Azure Backup Server körs som ett LocalSystem-konto. Om du vill säkerhetskopiera SQL Server-databaser behöver MABS sysadmin-behörighet för kontot för den server som kör SQL Server. Ange NT instans\system till *sysadmin* på den server som kör SQL Server innan du säkerhetskopierar den.

Om SharePoint-servergruppen har SQL Server databaser som kon figurer ATS med SQL Server alias, installerar du SQL Server-klient komponenterna på den klient webb server som MABS ska skydda.

### <a name="whats-not-supported"></a>Vad som inte stöds
* MABS som skyddar en SharePoint-servergrupp skyddar inte Sök index eller program tjänst databaser. Du måste konfigurera skyddet av dessa databaser separat.
* MABS tillhandahåller inte säkerhets kopior av SharePoint SQL Server-databaser som finns på SOFS-resurser (Scale-Out File Server).

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd
Innan du kan använda MABS för att skydda SharePoint måste du konfigurera tjänsten SharePoint VSS Writer (tjänsten WSS Writer) genom att använda **ConfigureSharePoint. exe**.

Du kan hitta **ConfigureSharePoint. exe** i mappen [Mabs installations Sök väg] \Bin på klient webb servern. Det här verktyget tillhandahåller skydds agenten med autentiseringsuppgifterna för SharePoint-servergruppen. Du kör den på en enskild WFE-Server. Om du har flera WFE-servrar väljer du bara en när du konfigurerar en skydds grupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurera SharePoint VSS Writer-tjänsten
1. Öppna en kommando tolk på WFE-servern och gå till [MABS installation location] \bin\
2. Ange ConfigureSharePoint-EnableSharePointProtection.
3. Ange administratörs behörighet för Server gruppen. Det här kontot ska vara medlem i den lokala administratörs gruppen på WFE-servern. Om Server grupps administratören inte är en lokal administratör ger du följande behörigheter på WFE-servern:
   * Bevilja WSS_Admin_WPG-gruppen fullständig kontroll över DPM-mappen (% Program Files%\Microsoft Azure Backup\DPM).
   * Bevilja WSS_Admin_WPG-gruppen Läs behörighet till register nyckeln för DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Du måste köra ConfigureSharePoint. exe på nytt när det finns en ändring i administratörs uppgifterna för SharePoint-servergruppen.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av MABS
När du har konfigurerat MABS och SharePoint-gruppen enligt förklarat tidigare, kan SharePoint skyddas av MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Skydda en SharePoint-grupp
1. Klicka på **nytt**på fliken **skydd** i Mabs administratörskonsol.
    ![Fliken ny skydd](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På sidan **Välj typ av skydds grupp** i **guiden Skapa ny skydds grupp** väljer du **servrar**och klickar sedan på **Nästa**.

    ![Välj typ av skydds grupp](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. På sidan **Välj grupp medlemmar** markerar du kryss rutan för den SharePoint-Server som du vill skydda och klickar på **Nästa**.

    ![Välj grupp medlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > När skydds agenten är installerad kan du se servern i guiden. MABS visar också dess struktur. Eftersom du körde ConfigureSharePoint. exe kommunicerar MABS med SharePoint VSS Writer-tjänsten och dess motsvarande SQL Server-databaser och känner igen SharePoint-servergruppen, de associerade innehålls databaserna och alla motsvarande objekt.
   >
   >
4. På sidan **Välj data skydds metod** anger du namnet på skydds **gruppen**och väljer önskade *skydds metoder*. Klicka på **Nästa**.

    ![Välj data skydds metod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Disk skydds metoden hjälper till att uppfylla korta återställnings tids mål.
   >
   >
5. På sidan **Ange kortvariga mål** väljer du önskat **kvarhållningsintervall** och identifierar när du vill att säkerhets kopieringen ska ske.

    ![Ange kortsiktiga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Eftersom återställning ofta krävs för data som är mindre än fem dagar gamla, valde vi ett kvarhållningsintervall på fem dagar på disken och säkerställer att säkerhets kopieringen sker under icke-produktions timmar, i det här exemplet.
   >
   >
6. Granska det disk utrymme som allokerats i lagringspoolen för skydds gruppen och klicka sedan på **Nästa**.
7. För varje skydds grupp allokerar MABS disk utrymme för att lagra och hantera repliker. I det här läget måste MABS skapa en kopia av de valda data. Välj hur och när du vill att repliken ska skapas och klicka sedan på **Nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > För att se till att nätverks trafiken inte påverkas väljer du en tid utanför produktions timmarna.
   >
   >
8. MABS garanterar data integriteten genom att utföra konsekvens kontroll på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvens kontroller, eller så kan DPM köra konsekvens kontroller automatiskt på repliken när det blir inkonsekvent. Välj önskat alternativ och klicka sedan på **Nästa**.

    ![Konsekvens kontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På sidan **Ange online skydds data** väljer du den SharePoint-grupp som du vill skydda och klickar sedan på **Nästa**.

    ![DPM SharePoint-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På sidan **Ange schema för onlinesäkerhetskopiering** väljer du önskat schema och klickar sedan på **Nästa**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS tillhandahåller maximalt två dagliga säkerhets kopieringar till Azure från den senaste säkerhets kopierings punkten för disken. Azure Backup kan också styra mängden WAN-bandbredd som kan användas för säkerhets kopieringar vid hög belastning och låg belastnings tid genom att använda [Azure Backup nätverks begränsning](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Beroende på vilket schema för säkerhets kopiering som du har valt väljer du bevarande principen för säkerhets kopierings punkter per dag, vecka, månad och år på sidan **Ange princip** för kvarhållning av online.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS använder ett farfar-överordnat son-kvarhållningsdatum där en annan bevarande princip kan väljas för olika säkerhets kopierings punkter.
    >
    >
12. Precis som på disk måste en ursprunglig referens punkts replik skapas i Azure. Välj önskat alternativ för att skapa en första säkerhets kopia till Azure och klicka sedan på **Nästa**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de valda inställningarna på sidan **Sammanfattning** och klicka sedan på **Skapa grupp**. Ett meddelande visas när skydds gruppen har skapats.

    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Återställa ett SharePoint-objekt från en disk med hjälp av MABS
I följande exempel har den återställda *SharePoint-objektet* tagits bort av misstag och måste återställas av misstag.
![MABS SharePoint-Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna **DPM-administratörskonsol**. Alla SharePoint-grupper som skyddas av DPM visas på fliken **skydd** .

    ![MABS SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Välj fliken **återställning** för att börja återställa objektet.

    ![MABS SharePoint-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka i SharePoint efter återställning av *SharePoint-objekt* med hjälp av en jokertecken sökning inom ett återställnings punkt intervall.

    ![MABS SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställnings punkt i Sök resultaten, högerklicka på objektet och välj sedan **Återställ**.
5. Du kan också bläddra igenom olika återställnings punkter och välja en databas eller ett objekt som ska återställas. Välj **datum > återställnings tid**och välj sedan rätt **databas > SharePoint-servergrupp > återställnings punkt > objekt**.

    ![MABS SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **Återställ** för att öppna återställnings **guiden**. Klicka på **Nästa**.

    ![Granska val av återställning](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Välj den typ av återställning du vill utföra och klicka sedan på **Nästa**.

    ![Återställnings typ](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Valet av **Återställ till original** i exemplet återställer objektet till den ursprungliga SharePoint-webbplatsen.
   >
   >
8. Välj den **återställnings process** som du vill använda.

   * Välj **Återställ utan att använda en återställnings grupp** om SharePoint-servergruppen inte har ändrats och är samma som återställnings punkten som återställs.
   * Välj **Återställ med en återställnings grupp** om SharePoint-servergruppen har ändrats sedan återställnings punkten skapades.

     ![Återställnings process](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Ange en mellanlagringsplats SQL Server instans plats för att återställa databasen tillfälligt och tillhandahålla en mellanlagrings fil resurs på MABS och servern som kör SharePoint för att återställa objektet.

    ![Location1 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS bifogar innehålls databasen som är värd för SharePoint-objektet till den tillfälliga SQL Server-instansen. Från innehålls databasen återställer den objektet och placerar det på mellanlagringsplatsen på MABS. Det återställda objektet som finns på mellanlagringsplatsen måste nu exporteras till mellanlagringsplatsen i SharePoint-servergruppen.

    ![Location2 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställnings alternativ**och tillämpa säkerhets inställningar på SharePoint-servergruppen eller tillämpa säkerhets inställningarna för återställnings punkten. Klicka på **Nästa**.

    ![Återställnings alternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa användningen av nätverks bandbredd. Detta minimerar påverkan på produktions servern under produktions timmar.
    >
    >
11. Granska sammanfattnings informationen och klicka sedan på **Återställ** för att påbörja återställningen av filen.

    ![Återställnings Sammanfattning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Klicka nu på fliken **övervakning** i **Mabs-administratörskonsol** för att visa **status** för återställningen.

    ![Återställnings status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen har återställts. Du kan uppdatera SharePoint-webbplatsen för att kontrol lera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM
1. Du återställer en SharePoint-innehållstyp genom att bläddra igenom olika återställnings punkter (se tidigare) och välja den återställnings punkt som du vill återställa.

    ![MABS SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställnings punkten för att Visa tillgänglig information om SharePoint-katalogen.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure finns det ingen katalog information (metadata) på MABS. När en innehålls databas för SharePoint-databasen måste återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Klicka på **ny katalog**.

    ![MABS SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Fönstret status för **moln Omkatalogisering** öppnas.

    ![MABS SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När fil förteckningen har slutförts ändras statusen till *lyckades*. Klicka på **Stäng**.

    ![MABS SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på det SharePoint-objekt som visas på fliken MABS **återställning** för att hämta struktur för innehålls databasen. Högerklicka på objektet och klicka sedan på **Återställ**.

    ![MABS SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ återställnings stegen tidigare i den här artikeln för att återställa en SharePoint-innehålls databas från disken.

## <a name="faqs"></a>Vanliga frågor och svar
F: Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint har kon figurer ATS med hjälp av SQL AlwaysOn (med skydd på disk)?<br>
S: Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

F: Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?<br>
S: Eftersom SharePoint-databaser är konfigurerade i SQL AlwaysOn, kan de inte ändras om inte tillgänglighets gruppen tas bort. Därför kan MABS inte återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Se artikeln [säkerhetskopiera filer och program](backup-mabs-files-applications-azure-stack.md) .
Se [säkerhets kopierings SQL Server i Azure Stack](backup-mabs-sql-azure-stack.md) artikeln.
