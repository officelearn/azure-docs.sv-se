---
title: Installera Azure Backup Server på Azure Stack | Microsoft Docs
description: Använda Azure Backup Server för att skydda eller säkerhetskopiera arbetsbelastningar i Azure Stack.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Azure backup server. skydda arbetsbelastningar; Säkerhetskopiera arbetsbelastningar
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: 14379d82b4b60c0ea555388ac61c9c7b8fbe9e4f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721450"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installera Azure Backup Server på Azure Stack

Den här artikeln beskriver hur du installerar Azure Backup Server på Azure Stack. Med Azure Backup Server kan du skydda infrastruktur som en tjänst (IaaS)-arbetsbelastningar som till exempel virtuella datorer som körs i Azure Stack. En fördel med att använda Azure Backup Server för att skydda dina arbetsbelastningar är att du kan hantera alla skydd av arbetsbelastning från en enda konsol.

> [!NOTE]
> Mer information om funktioner för säkerhet, referera till [dokumentation för Azure Backup-säkerhetsfunktioner](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server
Azure Backup Server skyddar följande arbetsbelastningar i Azure Stack-datorer.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Halvårskanal för Windows Server halvstrukturerade – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 R2 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 – Datacenter/Entprise/Standard | Volymer, filer, mappar |
| Windows Server 2008 R2 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2016 | Servergrupp, databas, klientdel, webbserver |
| SharePoint 2013 | Servergrupp, databas, klientdel, webbserver |
| SharePoint 2010 | Servergrupp, databas, klientdel, webbserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Server-miljö

Överväg att rekommendationerna i det här avsnittet när du installerar Azure Backup Server i Azure Stack-miljön. Azure Backup Server kontrolleras att din miljö har de nödvändiga förutsättningarna, men du kan spara tid genom att förbereda innan du installerar.

### <a name="determining-size-of-virtual-machine"></a>Bestämma storleken på virtuell dator
Kör Azure Backup Server på en virtuell dator i Azure Stack med storlek A2 eller större. För hjälp med att välja en VM-storlek, ladda ned den [Kalkylatorn för storlek på virtuell dator i Azure Stack](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på Azure Stack-datorer
Alla virtuella datorer som används i en Azure Stack-arbetsbelastningen måste tillhöra samma Azure-nätverk och Azure-prenumeration.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server-VM-prestanda
Om delas med andra virtuella datorer, lagringskontot storlek och IOPS-gränser inverkan Azure Backup Server-VM-prestanda. Därför bör du använda ett separat lagringskonto för den virtuella datorn i Azure Backup Server. Azure Backup-agenten som körs på Azure Backup Server behöver tillfällig lagring för:
- sin egen användning (en cacheplats)
- data som återställs från molnet (lokalt mellanlagringsområde)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup-lagring på temporär disk
Varje virtuell dator i Azure Stack levereras med lagring på temporär disk, som är tillgänglig för användaren som volym `D:\`. Det lokala mellanlagringsområdet som krävs av Azure Backup kan konfigureras så att det finns i `D:\`, och cacheplatsen kan läggas på `C:\`. På så sätt kan måste ingen lagring vara högg bort från de diskar som är anslutna till den virtuella datorn i Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagring av säkerhetskopierade data på den lokala disken och i Azure
Azure Backup Server lagrar säkerhetskopierade data på Azure diskar som är anslutna till den virtuella datorn för driftåterställning. Diskar och lagringsutrymme som är kopplade till den virtuella datorn hanterar lagring i Azure Backup Server åt dig. Mängden säkerhetskopieringsdata lagring beror på antalet och storleken på diskar som är anslutna till var och en [VM i Azure Stack](../azure-stack/user/azure-stack-storage-overview.md). Varje Azure Stack VM-storlek har ett maximalt antal diskar som kan kopplas till den virtuella datorn. A2 är till exempel fyra diskar. A3 är åtta diskar. A4 är 16 diskar. Igen, storlek och antalet diskar som anger den totala lagringspoolen för säkerhetskopiering.

> [!IMPORTANT]
> Du bör **inte** avinstallationsalternativ driftåterställning (säkerhetskopiering) på Azure Backup Server-anslutna diskar i mer än fem dagar.
>

Lagring av säkerhetskopierade data i Azure minskar infrastruktur för säkerhetskopiering i Azure Stack. Om data är mer än fem dagar, bör det lagras i Azure.

För att lagra säkerhetskopierade data i Azure, skapa eller använda ett Recovery Services-valv. När du förbereder för säkerhetskopiering av Azure Backup Server-arbetsbelastning du [konfigurera Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). När du konfigurerat varje gång en säkerhetskopiering körs skapas en återställningspunkt i valvet. Varje Recovery Services-valvet innehåller upp till 9 999 återställningspunkter. Beroende på hur många återställningspunkter som skapats och hur länge den ska bevaras, kan du behålla säkerhetskopierade data under många år. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.
 
### <a name="scaling-deployment"></a>Skala distributionen
Om du vill skala distributionen har följande alternativ:
  - Skala upp – öka storleken på Azure Backup Server virtuell dator från en serie till D-serien och öka den lokala lagringen [per VM-instruktionerna Azure Stack](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Omfördela data – skicka äldre data till Azure och spara endast senaste data på det lagringsutrymme som är anslutet till Azure Backup Server.
  - Skala ut – lägga till fler Azure Backup-servrar för att skydda arbetsbelastningarna.

### <a name="net-framework"></a>.NET Framework

.NET framework 3.5 SP1 eller senare måste vara installerat på den virtuella datorn.

### <a name="joining-a-domain"></a>Ansluta till en domän

Den virtuella datorn i Azure Backup Server måste vara ansluten till en domän. En domänanvändare med administratörsbehörighet måste du installera Azure Backup Server på den virtuella datorn.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Med hjälp av en virtuell IaaS-dator i Azure Stack

När du väljer en server för Azure Backup Server, börja med en Windows Server 2012 R2 Datacenter- eller Windows Server 2016 Datacenter galleri-avbildning. I artikeln [skapa din första Windows-dator i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), innehåller en vägledning för att komma igång med den rekommendera virtuella datorn. Rekommenderade minimikraven för den server-datorn (VM) ska vara: Standard A2 med två kärnor och 3,5 GB RAM-minne.

Skydda arbetsbelastningar med Azure Backup Server har många nyanser. I artikeln [Install DPM as Azure-datorer](https://technet.microsoft.com/library/jj852163.aspx), bidrar till att klargöra dessa olika delarna. Läs den här artikeln helt innan du distribuerar datorn.

> [!NOTE]
> Azure Backup Server är avsedd att köras på en dedikerad, enkelt syfte virtuell dator. Du kan inte installera Azure Backup Server på:
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Alltid ansluta till Azure Backup Server till en domän. Om du behöver flytta Azure Backup Server till en annan domän först installera Azure Backup Server och sedan ansluta den till den nya domänen. När du distribuerar Azure Backup Server kan flytta du inte den till en ny domän.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Recovery Services-valv med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder geo-redundant lagring i Recovery Services-valv. Lämna alternativet inställt på geo-redundant lagring om det här valvet är ditt primära valv. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som är mindre beständiga. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och menyn Inställningar. Om den **inställningar** inte öppna menyn, klicka på **alla inställningar** i instrumentpanelen för valvet.
2. På den **inställningar** -menyn klickar du på **infrastruktur för säkerhetskopiering** > **Säkerhetskopieringskonfigurationen** att öppna den **Säkerhetskopieringskonfigurationen**menyn. På den **Säkerhetskopieringskonfigurationen** menyn, Välj alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Ladda ned installationsprogrammet för Azure Backup Server

Det finns två sätt att hämta installationsprogrammet för Azure Backup Server. Du kan ladda ned Azure Backup Server-installationsprogrammet från den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Du kan också hämta installationsprogrammet för Azure Backup Server som du konfigurerar ett Recovery Services-valv. I följande steg vägleder dig genom att ladda ned installationsprogrammet från Azure-portalen när du konfigurerar ett Recovery Services-valv.

1. Från din virtuella dator Azure Stack [logga in på din Azure-prenumeration i Azure-portalen](https://portal.azure.com/).
2. I den vänstra menyn och väljer **alla tjänster**.

    ![Välj alternativet för alla tjänster i huvudmenyn](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. I den **alla tjänster** dialogrutan, skriver du in *återställningstjänster*. När du börjar skriva, filtrerar listan över resurser i dina indata. När du ser det väljer **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-mabs-install-azure-stack/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. Från listan över Recovery Services-valv, väljer du ditt valv för att öppna valvets instrumentpanel.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. I komma igång-menyn för valvet klickar du på **Backup** att öppna guiden komma igång.

    ![Backup komma igång](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Menyn säkerhetskopiering öppnas.

    ![Backup-mål-standard-öppnas](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. På menyn backup från den **var körs din arbetsbelastning** menyn och välj **lokala**. Från den **vad vill du säkerhetskopiera?** nedrullningsbara menyn och välj de arbetsbelastningar du vill skydda med hjälp av Azure Backup Server. Om du inte vet vilka arbetsbelastningar och välj Välj **Hyper-V-datorer** och klicka sedan på **förbereda infrastrukturen**.

    ![lokala och arbetsbelastningar som mål](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Den **Förbered infrastruktur** menyn öppnas.

7. I den **Förbered infrastruktur** -menyn klickar du på **hämta** att öppna en webbsida för att ladda ned installationsfilerna för Azure Backup Server.

    ![Komma igång-guiden Ändra](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Microsoft-webbsida som är värd för de nedladdningsbara filerna för Azure Backup Server öppnas.

8. Välj ett språk i hämtningssidan för Microsoft Azure Backup Server och klicka på **hämta**.

    ![Ladda ned center öppnas](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Azure Backup Server kan består av åtta filer – ett installationsprogram och sju .bin-filer. Kontrollera **filnamn** att välja alla nödvändiga filer och klicka på **nästa**. Ladda ned alla filer i samma mapp.

    ![Ladda ned center 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Hämta storleken på alla installationsfiler är större än 3 GB. På en 10 Mbit/s-nedladdning kan länken, hämtar alla installationsfiler ta upp till 60 minuter. Filerna ladda ned till den angivna platsen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahera installationsfiler för Azure Backup Server

När du har laddat ned alla filer till den virtuella datorn i Azure Stack, går du till nedladdningsplatsen. Den första fasen för att installera Azure Backup Server är att extrahera filerna.

![Ladda ned center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Starta installationen, i listan över hämtade filer, klicka på **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Minst 4GB ledigt utrymme krävs för att extrahera installationsfilerna.
    >

2. I guiden Azure Backup Server klickar du på **nästa** att fortsätta.

    ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Välj väg för Azure Backup Server-filer och klicka på **nästa**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Kontrollera extrahering plats och på **extrahera**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Guiden extraherar du filerna och förbereder sina installationsprocessen.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. När extraheringsprocessen är klar klickar du på **Slutför**. Som standard **köra setup.exe** har valts. När du klickar på **Slutför**, Setup.exe installerar Microsoft Azure Backup Server till den angivna platsen.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installationspaket för programvara

I det föregående steget du klickade på **Slutför** att stänga fasen extrahering och starta installationsguiden för Azure Backup Server.

![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server delar kod med Data Protection Manager. Du hittar referenser till Data Protection Manager och DPM i installationsprogrammet för Azure Backup Server. Även om Azure Backup Server och Data Protection Manager är separata produkter, relaterade dessa produkter nära.

1. Starta installationsguiden genom att klicka på **Microsoft Azure Backup Server**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. På den **Välkommen** klickar du på **nästa**.

    ![Kontrollera Azure Backup Server – Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. På den **Kravkontroller** klickar du på **Kontrollera** att avgöra om maskin- och krav för Azure Backup Server har uppfyllts.

    ![Kontrollera Azure Backup Server – Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Om din miljö har de nödvändiga förutsättningarna, visas ett meddelande om att datorn uppfyller kraven. Klicka på **Nästa**.  

    ![Azure Backup Server - krav har kontrollerats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Om din miljö inte uppfyller kraven, anges problem som. De krav som inte har uppfyllts visas också i DpmSetup.log. Åtgärda nödvändiga felen och kör sedan **kontrollen igen**. Installationen kan inte fortsätta förrän alla krav är uppfyllda.

    ![Azure Backup Server - installationskraven uppfylls inte](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server kräver SQL Server. Installationspaketet för Azure Backup Server levereras tillsammans med lämplig SQL Server-binärfiler. Om du vill använda din egen SQL-installation kan du. Det rekommendera valet är dock kan installationsprogrammet lägga till en ny instans av SQL Server. Om du vill säkerställa att ditt val fungerar med din miljö genom att klicka på **kontrollera och installera**.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärransluten SQL Server-instans. Den instans som används av Azure Backup Server måste vara lokal.
   >

    ![Kontrollera Azure Backup Server – Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    När du har kontrollerat, om den virtuella datorn har de nödvändiga förutsättningarna för att installera Azure Backup Server, klickar du på **nästa**.

    ![Kontrollera Azure Backup Server – Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Om ett fel inträffar med en rekommendation att starta om datorn och starta sedan om datorn. När du har startat om datorn, startar du om installationsprogrammet, och när du kommer till den **SQL-inställningar** klickar du på **kontrollen igen**.

5. I den **installationsinställningar**, ange en plats för installationen av Microsoft Azure Backup server-filer och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Den tillfälliga platsen krävs för att säkerhetskopiera till Azure. Se till att storleken på den tillfälliga platsen som motsvarar minst 5% av de data som ska säkerhetskopieras till Azure. För skydd på disk måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [konfigurera lagringspooler och disklagring](https://technet.microsoft.com/library/hh758075.aspx).

6. På den **säkerhetsinställningar** skärmen, ange ett starkt lösenord för begränsade lokala användarkonton och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. På den **Microsoft Update Opt-In** skärmen, väljer du om du vill använda *Microsoft Update* att söka efter uppdateringar och klicka på **nästa**.

   > [!NOTE]
   > Vi rekommenderar att du har Windows Update som omdirigerar till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Granska den *sammanfattning av inställningar* och klicka på **installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    När Azure Backup Server har installerats startar installationsprogrammet direkt installationsprogrammet för Microsoft Azure Recovery Services-agenten.

9. Microsoft Azure Recovery Services Agent installationsprogrammet öppnas och söker efter Internetanslutning. Om Internet-anslutning är tillgänglig kan du fortsätta med installationen. Om det finns ingen nätverksanslutning, anger du proxyinformationen att ansluta till Internet. När du har angett dina proxyinställningar, klickar du på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Om du vill installera Microsoft Azure Recovery Services-agenten klickar du på **installera**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure Recovery Services-agenten, även kallat Azure Backup-agenten konfigurerar Azure Backup Server till Recovery Services-valvet. När du konfigurerat Azure Backup Server alltid att säkerhetskopiera data till samma Recovery Services-valvet.

11. När Microsoft Azure Recovery Services-agenten har installerats klickar du på **nästa** att starta nästa fas: registrera Azure Backup Server med Recovery Services-valvet.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Installationsprogrammet startar den **guiden för serverregistrering**.

12. Växla till din Azure-prenumeration och Recovery Services-valvet. I den **förbereda infrastrukturen** -menyn klickar du på **hämta** att ladda ned valvautentiseringsuppgifter. Om den **hämta** knappen i steg 2 är inte aktiv, Välj **redan laddats ned eller använder den senaste Azure Backup Server-installationen** att aktivera knappen. Autentiseringsuppgifterna för valvet hämtas till den plats där du lagrar nedladdningar. Tänk på den här platsen eftersom du behöver för nästa steg.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. I den **Vault identifiering** -menyn klickar du på **Bläddra** att hitta autentiseringsuppgifterna för Recovery Services-valvet.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    I den **Välj Valvautentiseringsuppgifter** dialogrutan, gå till nedladdningsplatsen, Välj din valvautentiseringsuppgifter och klicka på **öppna**.

    Sökvägen till autentiseringsuppgifterna som visas i menyn Vault identifiering. Klicka på **nästa** att gå vidare till inställningarna för lösenordskryptering.

14. I den **krypteringsinställning** dialogrutan, ange en lösenfras för säkerhetskopiering kryptering och en plats för att lagra lösenfrasen klicka sedan på **nästa**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Du kan ange dina egna lösenfras eller Använd lösenfras-generator för att skapa en åt dig. Lösenfrasen är ditt och Microsoft inte spara eller hantera lösenfrasen. Om du vill förbereda för en katastrof, spara lösenfrasen på en tillgänglig plats.

    När du klickar på **nästa**, Azure Backup Server har registrerats med Recovery Services-valvet. Installationsprogrammet fortsätter installation av SQL Server och Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. När installationsprogrammet är klart visar statusen att all programvara har installerats.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    När installationen är klar skapas på Azure Backup Server-konsolen och ikoner för Azure Backup Server PowerShell på serverdatorn.

## <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior

Den första säkerhetskopian sparas på lagring som är anslutna till Azure Backup Server-datorn. Läs mer om att lägga till diskar i [lägga till Modern Backup storage](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Du måste lägga till lagring av säkerhetskopior, även om du planerar att skicka data till Azure. I Azure Backup Server-arkitektur Recovery Services-valv innehåller den *andra* kopia av data medan den lokala lagringen innehåller säkerhetskopian första (och obligatoriska).
>
>

## <a name="network-connectivity"></a>Nätverksanslutning

Azure Backup Server kräver anslutning till Azure Backup-tjänsten för produkten som ska fungera korrekt. Du kan kontrollera om datorn är ansluten till Azure genom att använda den ```Get-DPMCloudConnection``` cmdlet i Azure Backup Server PowerShell-konsolen. Om resultatet från cmdleten är sant kommer anslutningen finns, annars det finns ingen nätverksanslutning.

Azure-prenumerationen måste vara i felfritt tillstånd på samma gång. Att ta reda på status för din prenumeration och hantera det, logga in på den [prenumerationsportalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

När du vet för Azure anslutningen och den Azure-prenumerationen kan kan du använda tabellen nedan för att ta reda på inverkan på funktionen säkerhetskopiering/återställning.

| Tillstånd för anslutning | Azure-prenumeration | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställa från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Active |Behörig |Behörig |Behörig |Behörig |
| Ansluten |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppat och Azure återställningspunkter tas bort |Stoppad |
| Förlorad anslutning > 15 dagar |Active |Stoppad |Stoppad |Behörig |Behörig |
| Förlorad anslutning > 15 dagar |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Förlorad anslutning > 15 dagar |Avetableras |Stoppad |Stoppad |Stoppat och Azure återställningspunkter tas bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställa från förlust av anslutning

Om en brandvägg eller en proxy som hindrar åtkomst till Azure, vitlista följande domän-adresser i brandväggen/proxy-profil:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

När anslutningen till Azure har återställts på Azure Backup Server, avgör Azure prenumerationens status vilka åtgärder som kan utföras. När servern är **ansluten**, tabellen i [nätverksanslutningar](backup-mabs-install-azure-stack.md#network-connectivity) att se de tillgängliga åtgärderna.

### <a name="handling-subscription-states"></a>Hantering av prenumerationstillstånd

Det går att ändra en Azure-prenumeration från *har upphört att gälla* eller *avetablerad* till *Active* tillstånd. Prenumerationsstatusen är inte *Active*:

- När en prenumeration är *avetablerad*, förlorar det funktioner. Återställer prenumerationen som ska *Active*, revives funktionen för säkerhetskopiering/återställning. Om du säkerhetskopierade data på den lokala disken behölls med en tillräckligt stor kvarhållningsperiod som säkerhetskopierade data kan hämtas. Säkerhetskopierade data i Azure är dock oåterkalleligen förlorade när prenumerationen försätts den *avetablerad* tillstånd.
- När en prenumeration är *har upphört att gälla*, förlorar det funktioner. Schemalagda säkerhetskopieringar inte kör en prenumeration är *har upphört att gälla*.

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup server misslyckas med fel under installationsfasen (eller säkerhetskopieringen eller återställningen) kan se den [fel koder dokumentet](https://support.microsoft.com/kb/3041338).
Du kan även gå till [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

I artikeln [förbereda din miljö för DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), innehåller information om Azure Backup Server-konfigurationer som stöds.

Du kan använda följande artiklar för att få en djupare förståelse för skydd av arbetsbelastningar med Microsoft Azure Backup Server.

- [SQL Server-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [SharePoint server-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativ server-säkerhetskopiering](backup-azure-alternate-dpm-server.md)
