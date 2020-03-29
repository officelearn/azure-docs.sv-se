---
title: Säkerhetskopiera en SharePoint-servergrupp på Azure Stack
description: Använd Azure Backup Server för att säkerhetskopiera och återställa dina SharePoint-data på Azure Stack. Den här artikeln innehåller information om hur du konfigurerar SharePoint-servergruppen så att önskade data kan lagras i Azure. Du kan återställa skyddade SharePoint-data från disk eller från Azure.
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d080605022cadf121fa6be99c9758fe9c0d878ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673048"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Säkerhetskopiera en SharePoint-servergrupp på Azure Stack

Du säkerhetskopierar en SharePoint-servergrupp på Azure Stack till Microsoft Azure med hjälp av Microsoft Azure Backup Server (MABS) på ungefär samma sätt som du säkerhetskopierar andra datakällor. Azure Backup ger flexibilitet i säkerhetskopieringsschemat för att skapa dagliga, veckovisa, månatliga eller årliga säkerhetskopieringspunkter och ger dig bevarandeprincipalternativ för olika säkerhetskopieringspunkter. Det ger också möjlighet att lagra lokala diskkopior för snabba återställningstidsmål (RTO) och att lagra kopior till Azure för ekonomisk och långsiktig kvarhållning.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint-stödda versioner och relaterade skyddsscenarier

Azure Backup för MABS stöder följande scenarier:

| Arbetsbelastning | Version | Distribution av SharePoint | Skydd och återställning |
| --- | --- | --- | --- |
| SharePoint |SharePoint 2016, SharePoint 2013, SharePoint 2010 |SharePoint distribueras som en virtuell Azure Stack-dator <br> -------------- <br> SQL AlwaysOn | Skydda återställningsalternativ för SharePoint-servergrupp: Återställningsgrupp, databas och fil- eller listobjekt från diskåterställningspunkter.  Återställning av servergrupp och databas från Azure-återställningspunkter. |

## <a name="before-you-start"></a>Innan du börjar

Det finns några saker du måste bekräfta innan du säkerhetskopierar en SharePoint-servergrupp till Azure.

### <a name="prerequisites"></a>Krav

Innan du fortsätter kontrollerar du att du har [installerat och förberett Azure Backup Server](backup-mabs-install-azure-stack.md) för att skydda arbetsbelastningar.

### <a name="protection-agent"></a>Skyddsombud

Azure Backup-agenten måste installeras på servern som kör SharePoint, servrarna som kör SQL Server och alla andra servrar som ingår i SharePoint-servergruppen. Mer information om hur du konfigurerar skyddsagenten finns i [Setup Protection Agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).  Det enda undantaget är att du installerar agenten endast på en enda WFE-server (Web Front end). Azure Backup Server behöver agenten på en WFE-server bara för att fungera som startpunkt för skydd.

### <a name="sharepoint-farm"></a>SharePoint-grupp

För varje 10 miljoner objekt i servergruppen måste det finnas minst 2 GB utrymme på volymen där MABS-mappen finns. Detta utrymme krävs för kataloggenerering. För att MABS ska kunna återställa specifika objekt (webbplatssamlingar, platser, listor, dokumentbibliotek, mappar, enskilda dokument och listobjekt) skapar kataloggenerering en lista över webbadresser som finns i varje innehållsdatabas. Du kan visa listan med webbadresser i fönstret återställningsbara objekt i aktivitetsområdet **Återställning** i MABS-administratörskonsolen.

### <a name="sql-server"></a>SQL Server

Azure Backup Server körs som ett LocalSystem-konto. För att säkerhetskopiera SQL Server-databaser behöver MABS sysadmin-behörigheter för den servern som kör SQL Server. Ange att NT AUTHORITY\SYSTEM *ska sysadmin* på servern som kör SQL Server innan du säkerhetskopierar den.

Om SharePoint-servergruppen har SQL Server-databaser som är konfigurerade med SQL Server-alias installerar du SQL Server-klientkomponenterna på den klientserver som MABS skyddar.

### <a name="whats-not-supported"></a>Vad som inte stöds

* MABS som skyddar en SharePoint-servergrupp skyddar inte sökindex eller programtjänstdatabaser. Du måste konfigurera skyddet av dessa databaser separat.
* MABS tillhandahåller inte säkerhetskopiering av SharePoint SQL Server-databaser som finns på utskalningsfilserver (SOFS) resurser.

## <a name="configure-sharepoint-protection"></a>Konfigurera SharePoint-skydd

Innan du kan använda MABS för att skydda SharePoint måste du konfigurera Tjänsten SharePoint VSS Writer (WSS Writer) med hjälp av **ConfigureSharePoint.exe**.

Du hittar **ConfigureSharePoint.exe** i mappen [MABS-installationssökväg]\bin på klientservern. Det här verktyget ger skyddsagenten autentiseringsuppgifterna för SharePoint-servergruppen. Du kör den på en enda WFE-server. Om du har flera WFE-servrar väljer du bara en när du konfigurerar en skyddsgrupp.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Så här konfigurerar du SharePoint VSS Writer-tjänsten

1. På WFE-servern går du till [MABS-installationsplatsen]\bin\
2. Ange ConfigureSharePoint -EnableSharePointProtection.
3. Ange administratörsbehörigheterna för servergruppen. Det här kontot ska vara medlem av den lokala administratörsgruppen på WFE-servern Om servergruppsadministratören inte är en lokal administratör ger du följande behörigheter på WFE-servern:
   * Ge den WSS_Admin_WPG gruppen fullständig kontroll till DPM-mappen (%Program Files%\Microsoft Azure Backup\DPM).
   * Ge den WSS_Admin_WPG gruppen läsbehörighet till DPM-registernyckeln (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsofts dataskyddshanterare).

> [!NOTE]
> Du måste köra ConfigureSharePoint.exe igen när det sker en ändring i autentiseringsuppgifterna för SharePoint-servergruppens administratör.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Säkerhetskopiera en SharePoint-servergrupp med hjälp av MABS

När du har konfigurerat MABS och SharePoint-servergruppen som tidigare förklarats kan SharePoint skyddas av MABS.

### <a name="to-protect-a-sharepoint-farm"></a>Så här skyddar du en SharePoint-servergrupp

1. Klicka på **Nytt**på fliken **Skydd** i MABS-administratörskonsolen.
    ![Fliken Nytt skydd](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. På sidan **Välj skydd grupptyp** i guiden **Skapa nytt skydd grupp** väljer du **Servrar**och klickar sedan på **Nästa**.

    ![Välj typ Av skyddsgrupp](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Markera kryssrutan för den SharePoint-server som du vill skydda på skärmen **Markera gruppmedlemmar** och klicka på **Nästa**.

    ![Välj gruppmedlemmar](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > När skyddsagenten är installerad kan du se servern i guiden. MABS visar också sin struktur. Eftersom du körde ConfigureSharePoint.exe kommunicerar MABS med SharePoint VSS Writer-tjänsten och dess motsvarande SQL Server-databaser och identifierar SharePoint-serverstrukturen, de associerade innehållsdatabaserna och motsvarande objekt.
   >
   >
4. På sidan **Välj dataskyddsmetod** anger du namnet på **skyddsgruppen**och väljer vilka *skyddsmetoder*du föredrar . Klicka på **Nästa**.

    ![Välj dataskyddsmetod](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Diskskyddsmetoden hjälper till att uppfylla korta återställningstidsmål.
   >
   >
5. På sidan **Ange kortsiktiga mål** väljer du önskat **kvarhållningsintervall**och identifierar när du vill att säkerhetskopieringar ska ske.

    ![Ange kortsiktiga mål](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Eftersom återställning oftast krävs för data som är mindre än fem dagar gamla, valde vi ett kvarhållningsintervall på fem dagar på disken och såg till att säkerhetskopieringen sker under icke-produktionstimmar, för det här exemplet.
   >
   >
6. Granska det lagringsutrymme för lagringspoolen som tilldelats för skyddsgruppen och klicka sedan på **Nästa**.
7. För varje skyddsgrupp allokerar MABS diskutrymme för att lagra och hantera repliker. Nu måste MABS skapa en kopia av de valda data. Välj hur och när du vill att repliken ska skapas och klicka sedan på **Nästa**.

    ![Välj metod för att skapa repliker](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Om du vill vara säker på att nätverkstrafiken inte påverkas väljer du en tid utanför produktionstimmarna.
   >
   >
8. MABS säkerställer dataintegritet genom att utföra konsekvenskontroller på repliken. Det finns två tillgängliga alternativ. Du kan definiera ett schema för att köra konsekvenskontroller eller dpm kan köra konsekvenskontroller automatiskt på repliken när det blir inkonsekvent. Välj önskat alternativ och klicka sedan på **Nästa**.

    ![Konsekvenskontroll](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. På sidan **Ange onlineskyddsdata** markerar du den SharePoint-servergrupp som du vill skydda och klickar sedan på **Nästa**.

    ![DPM SharePoint-skydd1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. På sidan **Ange schema för onlinesäkerhetskopiering** väljer du önskat schema och klickar sedan på **Nästa**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS tillhandahåller högst två dagliga säkerhetskopior till Azure från den sedan tillgängliga senaste disksäkerhetskopieringspunkten. Azure Backup kan också styra mängden WAN-bandbredd som kan användas för säkerhetskopior under hög- och lågtrafiktimmar med hjälp av [Azure Backup Network Throttling](backup-windows-with-mars-agent.md#enable-network-throttling).
    >
    >
11. Beroende på det säkerhetskopieringsschema som du har valt väljer du bevarandeprincipen för dagliga, veckovisa, månatliga och årliga säkerhetskopieringspunkter på sidan **Ange bevarandeprincip** online.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS använder ett farfar-far-son-lagringssystem där en annan bevarandeprincip kan väljas för olika säkerhetskopieringspunkter.
    >
    >
12. I likhet med disken måste en första referenspunktsreplik skapas i Azure. Välj önskat alternativ för att skapa en första säkerhetskopia till Azure och klicka sedan på **Nästa**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Granska de markerade inställningarna på sidan **Sammanfattning** och klicka sedan på **Skapa grupp**. Du kommer att se ett lyckat meddelande när skyddsgruppen har skapats.

    ![Sammanfattning](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Återställa ett SharePoint-objekt från disk med hjälp av MABS

I följande exempel har *återställande av SharePoint-objektet* tagits bort av misstag och måste återställas.
![MABS SharePoint-skydd4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Öppna **DPM-administratörskonsolen**. Alla SharePoint-grupper som skyddas av DPM visas på fliken **Skydd.**

    ![MABS SharePoint-skydd3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Om du vill börja återställa objektet väljer du fliken **Återställning.**

    ![MABS SharePoint-skydd5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Du kan söka i SharePoint för *att återställa SharePoint-objekt* med hjälp av en jokerteckenbaserad sökning inom ett återställningspunktintervall.

    ![MABS SharePoint-skydd6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Välj lämplig återställningspunkt från sökresultaten, högerklicka på objektet och välj sedan **Återställ**.
5. Du kan också bläddra igenom olika återställningspunkter och välja en databas eller ett objekt som ska återställas. Välj **Datum > Återställningstid**och välj sedan rätt **> > > Återställningspunkt > Objekt**.

    ![MABS SharePoint-skydd7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Högerklicka på objektet och välj sedan **Återställ** för att öppna **återställningsguiden**. Klicka på **Nästa**.

    ![Granska val av återställning](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Välj den typ av återställning som du vill utföra och klicka sedan på **Nästa**.

    ![Typ av återställning](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Valet av **Återställ till original** i exemplet återställer objektet till den ursprungliga SharePoint-webbplatsen.
   >
   >
8. Välj den **återställningsprocess** som du vill använda.

   * Välj **Återställ utan att använda en återställningsgrupp** om SharePoint-servergruppen inte har ändrats och är samma som återställningspunkten som återställs.
   * Välj **Återställ med hjälp av en återställningsgrupp** om SharePoint-servergruppen har ändrats sedan återställningspunkten skapades.

     ![Återställningsprocessen](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Ange en sql server-instansplats för mellanlagring för att återställa databasen tillfälligt och tillhandahålla en mellanlagringsfilresurs på MABS och servern som kör SharePoint för att återställa objektet.

    ![Mellanlagringsplats1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS bifogar innehållsdatabasen som är värd för SharePoint-objektet till den tillfälliga SQL Server-instansen. Från innehållsdatabasen återställs objektet och placeras på mellanlagringsfilens plats på MABS. Det återställda objektet som finns på mellanlagringsplatsen måste nu exporteras till mellanlagringsplatsen i SharePoint-servergruppen.

    ![Mellanlagringsplats2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Välj **Ange återställningsalternativ**och använd säkerhetsinställningar på SharePoint-servergruppen eller använd säkerhetsinställningarna för återställningspunkten. Klicka på **Nästa**.

    ![Alternativ för återställning](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Du kan välja att begränsa användningen av nätverkets bandbredd. Detta minimerar påverkan på produktionsservern under produktionstimmar.
    >
    >
11. Granska sammanfattningsinformationen och klicka sedan på **Återställ** för att påbörja återställningen av filen.

    ![Sammanfattning av återställning](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Välj nu fliken **Övervakning** i **MABS-administratörskonsolen** för att visa **återställningens status.**

    ![Återställningsstatus](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Filen är nu återställd. Du kan uppdatera SharePoint-webbplatsen för att kontrollera den återställda filen.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Återställa en SharePoint-databas från Azure med DPM

1. Om du vill återställa en SharePoint-innehållsdatabas bläddrar du igenom olika återställningspunkter (som tidigare) och väljer den återställningspunkt som du vill återställa.

    ![MABS SharePoint-skydd8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dubbelklicka på återställningspunkten för SharePoint för att visa tillgänglig SharePoint-kataloginformation.

   > [!NOTE]
   > Eftersom SharePoint-servergruppen är skyddad för långsiktig kvarhållning i Azure är ingen kataloginformation (metadata) tillgänglig på MABS. När en sharepoint-innehållsdatabas behöver återställas måste du därför katalogisera SharePoint-servergruppen igen.
   >
   >
3. Klicka på **Omkatalog.**

    ![MABS SharePoint-skydd10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Statusfönstret **För molnrealog** öppnas.

    ![MABS SharePoint-skydd11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    När katalogiseringen är klar ändras statusen till *Framgång*. Klicka på **Stäng**.

    ![MABS SharePoint-skydd12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klicka på SharePoint-objektet som visas på fliken **MABS-återställning** för att hämta innehållsdatabasstrukturen. Högerklicka på objektet och klicka sedan på **Återställ**.

    ![MABS SharePoint-skydd13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Följ nu återställningsstegen tidigare i den här artikeln för att återställa en SharePoint-innehållsdatabas från disken.

## <a name="faqs"></a>Vanliga frågor och svar

F: Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint konfigureras med SQL AlwaysOn (med skydd på disk)?<br>
S: Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

F: Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med SQL AlwaysOn?<br>
S: Eftersom SharePoint-databaser är konfigurerade i SQL AlwaysOn kan de inte ändras om inte tillgänglighetsgruppen tas bort. Därför kan MABS inte återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Se [artikel om säkerhetskopieringsfiler och program.](backup-mabs-files-applications-azure-stack.md)
Se artikeln [Backup SQL Server på Azure Stack.](backup-mabs-sql-azure-stack.md)
