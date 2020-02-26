---
title: Säkerhetskopiera en SharePoint-grupp till Azure med DPM
description: Den här artikeln innehåller en översikt över DPM/Azure Backup Server skydd för en SharePoint-grupp till Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: b766c0401dde10fdc257044e004de3dbf8a7b84c
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586486"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Säkerhetskopiera en SharePoint-grupp till Azure med DPM

Du säkerhetskopierar en SharePoint-grupp till Microsoft Azure med hjälp av System Center Data Protection Manager (DPM) på samma sätt som du säkerhetskopierar andra data källor. Azure Backup ger flexibilitet i schemat för säkerhets kopiering för att skapa dagliga, vecko Visa eller årliga säkerhets kopierings punkter och ger dig bevarande princip alternativ för olika säkerhets kopierings punkter. DPM ger möjlighet att lagra lokala disk kopior för snabba återställnings mål (RTO) och för att lagra kopior till Azure för ekonomisk, långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-versioner som stöds och relaterade skydds scenarier

Azure Backup för DPM stöder följande scenarier:

| Arbetsbelastning | Version | SharePoint-distribution | DPM-distributionstyp | DPM – System Center 2012 R2 | Skydd och återställning |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3,0 |SharePoint distribueras som en fysisk server eller Hyper-V/VMware virtuell dator <br> -------------- <br> SQL AlwaysOn |Fysisk server eller lokal virtuell Hyper-V-dator |Stöder säkerhets kopiering till Azure från Samlad uppdatering 5 |Skydda SharePoint-gruppens återställnings alternativ: återställnings grupp, databas och fil-eller List objekt från disk återställnings punkter.  Server grupp och databas återställning från Azure-återställnings punkter. |

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker du behöver bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter kontrollerar du att du har uppfyllt alla [krav för att använda Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) för att skydda arbets belastningar. Vissa uppgifter för krav är: skapa ett säkerhets kopierings valv, Hämta autentiseringsuppgifter för valvet, installera Azure Backup Agent och registrera DPM/Azure Backup Server med valvet.

### <a name="dpm-agent"></a>DPM-agent

DPM-agenten måste vara installerad på den server som kör SharePoint, de servrar som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Mer information om hur du konfigurerar skydds agenten finns i [installations skydds agenten](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  Ett undantag är att du bara installerar agenten på en enda front webb server (WFE). DPM behöver agenten på en WFE-server som endast fungerar som start punkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp

För varje 10 000 000-objekt i Server gruppen måste det finnas minst 2 GB utrymme på den volym där DPM-mappen finns. Det här utrymmet krävs för att skapa kataloger. För att DPM ska kunna återställa specifika objekt (webbplats samlingar, webbplatser, listor, dokument bibliotek, mappar, enskilda dokument och list objekt) skapar katalog skapande en lista över de URL: er som finns i varje innehålls databas. Du kan visa listan över URL: er i fönstret återställnings Bart objekt i **återställnings** aktivitets avsnittet i DPM-administratörskonsol.

### <a name="sql-server"></a>SQL Server

DPM körs som ett LocalSystem-konto. Om du vill säkerhetskopiera SQL Server-databaser behöver DPM sysadmin-behörighet för kontot för den server som kör SQL Server. Ange NT instans\system till *sysadmin* på den server som kör SQL Server innan du säkerhetskopierar den.

Om SharePoint-servergruppen har SQL Server databaser som har kon figurer ATS med SQL Server-alias, installerar du SQL Server-klient komponenterna på den klient webb server som DPM ska skydda.

### <a name="sharepoint-server"></a>SharePoint Server

Även om prestanda beror på många faktorer, till exempel storleken på SharePoint-servergruppen, kan en DPM-server skydda en 25 TB SharePoint-grupp.

### <a name="dpm-update-rollup-5"></a>DPM-Samlad uppdatering 5

Om du vill börja skydda en SharePoint-grupp till Azure måste du installera DPM-Samlad uppdatering 5 eller senare. Med Samlad uppdatering 5 kan du skydda en SharePoint-grupp i Azure om Server gruppen har kon figurer ATS med hjälp av SQL AlwaysOn.
Mer information finns i blogg inlägget som introducerar DPM samlad [uppdatering 5](https://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>Vad som inte stöds

* DPM som skyddar en SharePoint-servergrupp skyddar inte Sök index eller program tjänst databaser. Du måste konfigurera skyddet av dessa databaser separat.
* DPM tillhandahåller inte säkerhets kopior av SharePoint SQL Server-databaser som finns på en skalbar fil server (SOFS) resurser.

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd

Innan du kan använda DPM för att skydda SharePoint måste du konfigurera tjänsten VSS Writer för SharePoint (tjänsten VSS Writer) genom att använda **ConfigureSharePoint. exe**.

Du kan hitta **ConfigureSharePoint. exe** i mappen [DPM-installations Sök väg] \Bin på klient webb servern. Det här verktyget tillhandahåller skydds agenten med autentiseringsuppgifterna för SharePoint-servergruppen. Du kör det på en enskild WFE-server. Om du har flera WFE-servrar väljer du bara en när du konfigurerar en skydds grupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurera SharePoint VSS Writer-tjänsten

1. Öppna en kommando tolk på WFE-servern och gå till [DPM-installations plats] \bin\
2. Ange ConfigureSharePoint-EnableSharePointProtection.
3. Ange administratörs behörighet för Server gruppen. Det här kontot ska vara medlem i den lokala administratörs gruppen på WFE-servern. Om servergruppsadministratören är inte en lokal administratör ska du bevilja följande behörigheter på WFE-servern:
   * Bevilja WSS_Admin_WPG gruppen fullständig kontroll över DPM-mappen (% Program Files%\Microsoft Data Protection Manager\DPM).
   * Bevilja WSS_Admin_WPG gruppen Läs behörighet till DPM-registernyckeln (HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Du måste köra ConfigureSharePoint. exe på nytt när det finns en ändring i administratörs uppgifterna för SharePoint-servergruppen.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av DPM

När du har konfigurerat DPM och SharePoint-gruppen enligt förklarat tidigare, kan SharePoint skyddas av DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Skydda en SharePoint-grupp

1. Klicka på **nytt**på fliken **skydd** i DPM-administratörskonsol.
    Fliken ![nytt skydd](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På sidan **Välj typ av skydds grupp** i guiden **Skapa ny skydds grupp** väljer du **servrar**och klickar sedan på **Nästa**.

    ![Välj typ av skydds grupp](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. På sidan **Välj grupp medlemmar** markerar du kryss rutan för den SharePoint-Server som du vill skydda och klickar på **Nästa**.

    ![Välj grupp medlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > När DPM-agenten är installerad kan du se servern i guiden. DPM visar också dess struktur. Eftersom du körde ConfigureSharePoint. exe kommunicerar DPM med SharePoint VSS Writer-tjänsten och dess motsvarande SQL Server-databaser och känner igen SharePoint-servergruppen, de associerade innehålls databaserna och alla motsvarande objekt.
   >
   >
4. På sidan **Välj data skydds metod** anger du namnet på **skydds gruppen**och väljer önskade *skydds metoder*. Klicka på **Next**.

    ![Välj data skydds metod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Disk skydds metoden hjälper till att uppfylla korta återställnings tids mål. Azure är ett ekonomiskt, långsiktigt skydds mål jämfört med band. Mer information finns i [använda Azure Backup för att ersätta din band infrastruktur](./backup-azure-backup-cloud-as-tape.md)
   >
   >
5. På sidan **Ange kortvariga mål** väljer du önskat **kvarhållningsintervall** och identifierar när du vill att säkerhets kopieringen ska ske.

    ![Ange kortsiktiga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Eftersom återställning ofta krävs för data som är mindre än fem dagar gamla, valde vi ett kvarhållningsintervall på fem dagar på disken och säkerställer att säkerhets kopieringen sker under icke-produktions timmar, i det här exemplet.
   >
   >
6. Granska det disk utrymme som allokerats i lagringspoolen för skydds gruppen och klicka sedan på **Nästa**.
7. För varje skydds grupp allokerar DPM disk utrymme för att lagra och hantera repliker. I det här läget måste DPM skapa en kopia av de valda data. Välj hur och när du vill att repliken ska skapas och klicka sedan på **Nästa**.

    ![Välj metod för skapande av replik](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > För att se till att nätverks trafiken inte påverkas väljer du en tid utanför produktions timmarna.
   >
   >
8. DPM säkerställer data integriteten genom att utföra konsekvens kontroll på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvens kontroller, eller så kan DPM köra konsekvens kontroller automatiskt på repliken när det blir inkonsekvent. Välj önskat alternativ och klicka sedan på **Nästa**.

    ![Konsekvens kontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På sidan **Ange online skydds data** väljer du den SharePoint-grupp som du vill skydda och klickar sedan på **Nästa**.

    ![DPM SharePoint-Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På sidan **Ange schema för onlinesäkerhetskopiering** väljer du önskat schema och klickar sedan på **Nästa**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > DPM tillhandahåller högst två dagliga säkerhets kopieringar till Azure vid olika tidpunkter. Azure Backup kan också styra mängden WAN-bandbredd som kan användas för säkerhets kopieringar vid hög belastning och låg belastnings tid genom att använda [Azure Backup nätverks begränsning](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. Beroende på vilket schema för säkerhets kopiering som du har valt väljer du bevarande principen för säkerhets kopierings punkter per dag, vecka, månad och år på sidan **Ange princip för kvarhållning av online** .

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > DPM använder ett farfar-överordnat son-kvarhållningsdatum där en annan bevarande princip kan väljas för olika säkerhets kopierings punkter.
    >
    >
12. Precis som på disk måste en ursprunglig referens punkts replik skapas i Azure. Välj önskat alternativ för att skapa en första säkerhets kopia till Azure och klicka sedan på **Nästa**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de valda inställningarna på sidan **Sammanfattning** och klicka sedan på **Skapa grupp**. Ett meddelande visas när skydds gruppen har skapats.

    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Återställa ett SharePoint-objekt från en disk med hjälp av DPM

I följande exempel har den återställda *SharePoint-objektet* tagits bort av misstag och måste återställas av misstag.
![DPM SharePoint-Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna **DPM-administratörskonsol**. Alla SharePoint-grupper som skyddas av DPM visas på fliken **skydd** .

    ![DPM SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Välj fliken **återställning** för att börja återställa objektet.

    ![DPM SharePoint-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka i SharePoint efter återställning av *SharePoint-objekt* med hjälp av en jokertecken sökning inom ett återställnings punkt intervall.

    ![DPM SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställnings punkt i Sök resultaten, högerklicka på objektet och välj sedan **Återställ**.
5. Du kan också bläddra igenom olika återställnings punkter och välja en databas eller ett objekt som ska återställas. Välj **datum > återställnings tid**och välj sedan rätt **databas > SharePoint-servergrupp > återställnings punkt > objekt**.

    ![DPM SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **Återställ** för att öppna **återställnings guiden**. Klicka på **Next**.

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
9. Ange en mellanlagringsplats SQL Server instans plats för att återställa databasen tillfälligt och tillhandahålla en mellanlagrings fil resurs på DPM-servern och den server som kör SharePoint för att återställa objektet.

    ![Location1 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location1.png)

    DPM kopplar innehålls databasen som är värd för SharePoint-objektet till den tillfälliga SQL Server-instansen. Från innehålls databasen återställer DPM-servern objektet och placerar det på den tillfälliga sökvägen på DPM-servern. Det återställda objektet som finns på mellanlagringsplatsen på DPM-servern måste nu exporteras till mellanlagringsplatsen i SharePoint-servergruppen.

    ![Location2 för mellanlagring](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställnings alternativ**och tillämpa säkerhets inställningar på SharePoint-servergruppen eller tillämpa säkerhets inställningarna för återställnings punkten. Klicka på **Next**.

    ![Återställnings alternativ](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa användningen av nätverks bandbredd. Detta minimerar påverkan på produktions servern under produktions timmar.
    >
    >
11. Granska sammanfattnings informationen och klicka sedan på **Återställ** för att påbörja återställningen av filen.

    ![Återställnings Sammanfattning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Klicka nu på fliken **övervakning** i **DPM-administratörskonsol** för att visa **status** för återställningen.

    ![Återställnings status](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen har återställts. Du kan uppdatera SharePoint-webbplatsen för att kontrol lera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med hjälp av DPM

1. Du återställer en SharePoint-innehållstyp genom att bläddra igenom olika återställnings punkter (se tidigare) och välja den återställnings punkt som du vill återställa.

    ![DPM SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på SharePoint-återställnings punkten för att Visa tillgänglig information om SharePoint-katalogen.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure är ingen katalog information (metadata) tillgänglig på DPM-servern. När en innehålls databas för SharePoint-databasen måste återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Klicka på **ny katalog**.

    ![DPM SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Fönstret status för **moln Omkatalogisering** öppnas.

    ![DPM SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När fil förteckningen har slutförts ändras statusen till *lyckades*. Klicka på **Stäng**.

    ![DPM SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på det SharePoint-objekt som visas på fliken DPM- **återställning** för att hämta strukturen för innehålls databasen. Högerklicka på objektet och klicka sedan på **Återställ**.

    ![DPM SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ återställnings stegen tidigare i den här artikeln för att återställa en SharePoint-innehålls databas från disken.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om DPM-skydd av SharePoint – se [video serie – DPM-skydd för SharePoint](https://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Läs [viktig information för System Center 2012 – Data Protection Manager](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj860415(v=sc.12)?redirectedfrom=MSDN)
* Läs [viktig information om Data Protection Manager i System Center 2012 SP1](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj860394(v=sc.12)?redirectedfrom=MSDN)
