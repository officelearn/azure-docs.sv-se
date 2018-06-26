---
title: Installera Azure Backup-Server på Azure-stacken | Microsoft Docs
description: Använda Azure Backup Server för att skydda eller säkerhetskopiera arbetsbelastningar i Azure-stacken.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: Azure backup-servern. skydda arbetsbelastningar; Säkerhetskopiera arbetsbelastningar
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/5/2018
ms.author: markgal
ms.openlocfilehash: c9dd6a1818b0afeb5e577724568a8254a70c8228
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753362"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installera Azure Backup Server på Azure Stack

Den här artikeln beskriver hur du installerar Azure Backup Server på Azure-stacken. Med Azure Backup Server, kan du skydda infrastruktur som en tjänst (IaaS) arbetsbelastningar som till exempel virtuella datorer som körs i Azure-stacken. En fördel med att använda Azure Backup Server för att skydda dina arbetsbelastningar är att du kan hantera alla arbetsbelastningsskydd från en enda konsol.

> [!NOTE]
> Om du vill veta mer om säkerhetsfunktioner, referera till [dokumentation för Azure Backup-säkerhetsfunktioner](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server
Azure Backup-Server skyddar följande Azure Stack virtuella arbetsbelastningar.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Windows Server semikolon årligt kanal - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2016 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2012 R2 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| Windows Server 2012 - Datacenter-Entprise-Standard | Volymer, filer, mappar |
| Windows Server 2008 R2 - Datacenter-Enterprise-standarden | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2016 | Servergrupp, databas, klientdel, webbserver |
| SharePoint 2013 | Servergrupp, databas, klientdel, webbserver |
| SharePoint 2010 | Servergrupp, databas, klientdel, webbserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Server-miljö

Tänk på i det här avsnittet när du installerar Azure Backup Server i din Azure Stack-miljö. Azure Backup Server kontrolleras att miljön innehåller kraven, men du kan spara tid genom att förbereda innan du installerar.

### <a name="determining-size-of-virtual-machine"></a>Bestämma storleken på virtuella datorn
Kör Azure Backup Server på en virtuell dator i Azure-stacken med storlek A2 eller större. Hjälp med att välja en storlek på virtuell dator, hämtar den [Kalkylatorn för storlek på Azure-stacken VM](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på Azure-stacken virtuella datorer
Alla virtuella datorer som används i en arbetsbelastning i Azure-stacken måste tillhöra samma virtuella Azure-nätverket och Azure-prenumeration.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestanda
Om delas med andra virtuella datorer kan kontot lagringen storlek och IOPS-gränser inverkan Azure Backup Server VM prestanda. Därför bör du använda ett separat lagringskonto för den virtuella datorn i Azure Backup Server. Azure Backup-agenten körs på Azure Backup-servern behöver tillfällig lagring för:
- egen användning (en cacheplats)
- data återställs från molnet (lokalt mellanlagringsområde)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera Azure Backup tillfälliga disklagring
Varje virtuell dator i Azure-stacken medföljer tillfälliga diskutrymme, som är tillgängliga för användaren som volym `D:\`. Det lokala mellanlagringsområdet som krävs av Azure Backup kan konfigureras så att det finns i `D:\`, och cacheplatsen kan placeras på `C:\`. På så sätt behöver ingen lagring hämtas från datadiskar som är kopplade till den virtuella datorn i Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhetskopierade data på lokal disk och i Azure
Azure Backup-servern lagrar säkerhetskopierade data på Azure diskar som är anslutna till den virtuella datorn för operativa återställning. Diskarna och lagringsutrymmet är kopplade till den virtuella datorn hanterar Azure Backup Server lagring för dig. Hur mycket lagringsutrymme för säkerhetskopierade data beror på antalet och storleken på diskar som är anslutna till varje [virtuella Azure-stacken](../azure-stack/user/azure-stack-storage-overview.md). Varje storleken på stacken virtuella Azure-datorn har ett maximalt antal diskar som kan kopplas till den virtuella datorn. A2 är till exempel fyra diskar. A3 är åtta diskar. A4 är 16 diskar. Igen, storlek och antalet diskar avgör den totala lagringspoolen för säkerhetskopiering.

> [!IMPORTANT]
> Du bör **inte** behålla operativa återställningsdata (säkerhetskopiering) på Azure Backup-Server-anslutna diskar för mer än fem dagar.
>

Lagra säkerhetskopierade data i Azure minskar infrastrukturen för säkerhetskopiering på Azure-stacken. Om data är mer än fem dagar gammal, bör det lagras i Azure.

Skapa för att lagra säkerhetskopierade data i Azure, eller Använd Recovery Services-valvet. När du förbereder att säkerhetskopiera arbetsbelastningen Azure Backup Server du [konfigurera Recovery Services-valvet](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). När du konfigurerat varje gång en säkerhetskopiering körs skapas en återställningspunkt i valvet. Varje Recovery Services-valvet innehåller upp till 9999 återställningspunkter. Du kan behålla säkerhetskopierade data i många år beroende på antalet återställningspunkter som skapats och hur länge de bevaras. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.
 
### <a name="scaling-deployment"></a>Skalning distribution
Om du vill skala distributionen har följande alternativ:
  - Skala upp – öka storleken på Azure Backup Server virtuell dator från en serie D-serien och öka den lokala lagringen [per Azure Stack virtuella instruktioner](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Omfördela data: skicka äldre data till Azure och spara endast senaste data på det lagringsutrymme som är kopplade till Azure Backup-Server.
  - Skala ut – Lägg till fler Azure Backup-servrar för att skydda arbetsbelastningarna.

### <a name="net-framework"></a>.NET framework

.NET framework 3.5 SP1 eller senare måste vara installerat på den virtuella datorn.

### <a name="joining-a-domain"></a>Ansluta till en domän

Den virtuella datorn i Azure Backup Server måste vara ansluten till en domän. En domänanvändare med administratörsprivilegier måste installera Azure Backup Server på den virtuella datorn.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Med hjälp av en IaaS-VM i Azure-stacken

När du väljer en server för Azure Backup Server, börja med en avbildning för Windows Server 2012 R2 Datacenter eller Windows Server 2016 Datacenter-galleriet. I artikeln [skapa din första Windows-dator i Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), innehåller en vägledning för att komma igång med den rekommenderade virtuella datorn. Rekommenderade minimikraven för den virtuella datorn (VM) ska vara: A2 Standard med två kärnor och 3.5 GB RAM-minne.

Skydda arbetsbelastningar med Azure Backup Server har många olika delarna. I artikeln [installera DPM som en virtuell Azure-dator](https://technet.microsoft.com/library/jj852163.aspx), hjälper beskrivs de olika delarna. Läs den här artikeln helt innan du distribuerar datorn.

> [!NOTE]
> Azure Backup-Server är avsedd att köras på en dedikerad, enkelt syfte virtuell dator. Du kan inte installera Azure Backup Server på:
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Alltid ansluta till Azure Backup Server till en domän. Om du behöver flytta Azure Backup Server till en annan domän först installera Azure Backup Server och sedan ansluta den till den nya domänen. När du distribuerar Azure Backup Server kan flytta du inte den till en ny domän.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Recovery Services-valvet lagringsalternativ replikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv geo-redundant lagring. Om det här valvet är ditt primära valv lämna lagringsalternativet inställt på geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som är mindre beständiga. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj din valvet för att öppna instrumentpanelen valvet och menyn Inställningar. Om den **inställningar** inte öppna menyn, klicka på **alla inställningar** i instrumentpanelen för valvet.
2. På den **inställningar** -menyn klickar du på **säkerhetskopiering infrastruktur** > **konfigurering av säkerhetskopiering** att öppna den **konfigurering av säkerhetskopiering**menyn. På den **konfigurering av säkerhetskopiering** -menyn väljer du alternativet lagring replikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Hämta Azure Backup Server installer

Det finns två sätt att hämta installationsprogrammet för Azure Backup Server. Du kan hämta Azure Backup Server installationsprogrammet från den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55269). Du kan också hämta installationsprogram för Azure Backup Server som du konfigurerar en Recovery Services-valvet. Följande steg beskriver hur du hämtar installationsprogrammet från Azure-portalen när du konfigurerar en Recovery Services-valvet.

1. Från den virtuella datorn Azure Stack [logga in till din Azure-prenumeration i Azure portal](https://portal.azure.com/).
2. Välj i den vänstra menyn **alla tjänster**.

    ![Välj alternativet för alla tjänster på huvudmenyn](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. I den **alla tjänster** dialogrutan, ange *återställningstjänster*. När du börjar skriva filtrerar listan över resurser i dina indata. När du ser det välja **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-mabs-install-azure-stack/all-services.png)

    Lista över Recovery Services-valv i prenumerationen visas.

4. Välj din valvet för att öppna dess instrumentpanelen från listan över Recovery Services-valv.

    ![Skriv återställningstjänster i dialogrutan för alla tjänster](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Komma igång-menyn på valvet **säkerhetskopiering** att öppna guiden komma igång.

    ![Säkerhetskopiering komma igång](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Säkerhetskopiering menyn öppnas.

    ![Backup-mål-standard-öppnas](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. I menyn säkerhetskopiering från den **var körs din arbetsbelastning** väljer du **lokalt**. Från den **vad vill du säkerhetskopiera?** nedrullningsbara menyn och väljer de arbetsbelastningar som du vill skydda med hjälp av Azure Backup-Server. Om du inte vet vilka arbetsbelastningar kan välja välja **Hyper-V-datorer** och klicka sedan på **Förbered infrastrukturen**.

    ![lokalt och arbetsbelastningar som mål](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Den **Förbered infrastruktur** menyn öppnas.

7. I den **Förbered infrastruktur** -menyn klickar du på **hämta** att öppna en webbsida för att ladda ned installationsfilerna för Azure Backup Server.

    ![Komma igång-guiden Ändra](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Microsoft öppnas som värd för nedladdningsbara filer för Azure Backup Server.

8. Välj ett språk i hämtningssidan för Microsoft Azure Backup Server och klickar på **hämta**.

    ![Hämta center öppnas](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Installationsprogram för Azure Backup Server består av åtta filer – ett installationsprogram och sju .bin-filer. Kontrollera **filnamn** väljer alla nödvändiga filer och klickar på **nästa**. Hämta alla filer i samma mapp.

    ![Hämta center 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Hämta storleken på alla installationsfiler är större än 3 GB. På en 10 Mbit/s-hämtning kan länken, hämtas alla installationsfiler ta upp till 60 minuter. Filerna som hämtas till den angivna platsen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahera Azure Backup Server installation av filer

När du har laddat ned alla filer till den virtuella datorn i Azure-stacken, gå till nedladdningsplatsen. Den första fasen av Azure Backup Server-installationen är att extrahera filerna.

![Hämta center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Starta installationen från listan över filer som hämtas, klicka på **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Minst 4GB ledigt diskutrymme krävs för att extrahera installationsfilerna.
    >

2. Klicka på Azure Backup Server-guiden **nästa** att fortsätta.

    ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Välj sökvägen till Azure Backup Server-filerna och på **nästa**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Kontrollera platsen för extrahering och på **extrahera**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Guiden extraherar filerna och förbereder sina installationsprocessen.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. När extraheringsprocessen är klar klickar du på **Slutför**. Som standard **köra setup.exe** är markerad. När du klickar på **Slutför**, Setup.exe installerar Microsoft Azure Backup Server till den angivna platsen.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installationspaket för programvara

I föregående steg du klickade på **Slutför** avsluta fasen extrahering och starta guiden för installation av Azure Backup Server.

![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server delar kod med Data Protection Manager. Du hittar referenser till Data Protection Manager och DPM i Azure Backup Server-installationsprogrammet. Även om Azure Backup Server och Data Protection Manager är separata produkter, är dessa produkter nära relaterade.

1. Om du vill starta guiden klickar du på **Microsoft Azure Backup Server**.

   ![Installationsguiden för Microsoft Azure Backup](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. På den **Välkommen** klickar du på **nästa**.

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. På den **nödvändiga kontrollerar** klickar du på **Kontrollera** att avgöra om kraven för maskinvara och programvara för Azure Backup Server är uppfyllda.

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Om miljön innehåller kraven, visas ett meddelande som anger att datorn uppfyller kraven. Klicka på **Nästa**.  

    ![Azure Backup-Server - kraven har kontrollerats](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Om din miljö inte uppfyller kraven, ska problem anges. De krav som inte har uppfyllts visas också i DpmSetup.log. Lös feltexten och kör sedan **kontrollen igen**. Installationen kan inte fortsätta förrän alla krav är uppfyllda.

    ![Azure Backup-Server - installationskraven inte uppfylldes](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server kräver SQL Server. Azure Backup Server installationspaketet medföljer lämplig SQL Server-binärfilerna. Om du vill använda din egen SQL-installationen kan du. Rekommenderade valet är dock låta installationsprogrammet lägger till en ny instans av SQL Server. Kontrollera ditt val fungerar med din miljö, klicka på **kontrollera och installera**.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärrinstans av SQL Server. Den instans som används av Azure Backup Server måste vara lokal.
   >

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    När du har kontrollerat, om den virtuella datorn har kraven för installation av Azure Backup Server, klickar du på **nästa**.

    ![Kontrollera om Azure Backup Server - Välkommen och förutsättningar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Om det uppstår ett fel med en rekommendation att starta om datorn, startar du om datorn. Efter omstart av datorn, startar du om installationsprogrammet, och när du kommer till den **SQL-inställningar** klickar du på **kontrollen igen**.

5. I den **installationsinställningar**, ange en plats för installation av Microsoft Azure Backup serverfiler och klickar på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Den tillfälliga platsen krävs för att säkerhetskopiera till Azure. Se till att storleken på den virtuella platsen motsvarar minst 5% av de data som ska säkerhetskopieras till Azure. För skydd på disk måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns [konfigurera lagringspooler och disklagring](https://technet.microsoft.com/library/hh758075.aspx).

6. På den **säkerhetsinställningar** skärmen, ange ett starkt lösenord för begränsade lokala användarkonton och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. På den **Microsoft Update Opt-In** , väljer om du vill använda *Microsoft Update* att söka efter uppdateringar och klicka på **nästa**.

   > [!NOTE]
   > Vi rekommenderar att du har Windows Update omdirigera till Microsoft Update, vilket ger säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup-Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Granska de *inställningar för sammanfattning av* och på **installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    När Azure Backup Server har installerats startar installationsprogrammet för Microsoft Azure Recovery Services-agenten direkt i installationsprogrammet.

9. Microsoft Azure Recovery Services-agenten installer öppnas och söker efter anslutning till Internet. Om Internet-anslutning är tillgänglig kan du fortsätta med installationen. Om det finns ingen nätverksanslutning, anger du proxyinformationen att ansluta till Internet. När du har angett dina proxyinställningar, klickar du på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Installera Microsoft Azure Recovery Services-agenten, klicka på **installera**.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure Recovery Services-agenten, även kallat Azure Backup-agenten konfigurerar Azure Backup Server till Recovery Services-valvet. När du konfigurerat Azure Backup-Server alltid att säkerhetskopiera data till samma Recovery Services-valvet.

11. När Microsoft Azure Recovery Services-agenten har installerats klickar du på **nästa** att starta nästa fas: Azure Backup-servern registreras med Recovery Services-valvet.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Installationsprogrammet startar den **guiden registrera Server**.

12. Gå till din Azure-prenumeration och Recovery Services-valvet. I den **Förbered infrastrukturen** -menyn klickar du på **hämta** att ladda ned valvautentiseringsuppgifter. Om den **hämta** knappen i steg 2 är inte aktiv, Välj **redan hämtas eller använder den senaste Azure Backup Server installationen** aktivera knappen. Autentiseringsuppgifter för valv ladda ned till den plats där du lagrar hämtningar. Tänk på den här platsen eftersom du behöver för nästa steg.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. I den **valvet identifiering** -menyn klickar du på **Bläddra** att hitta autentiseringsuppgifter för Recovery Services-valv.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    I den **Välj Valvautentiseringsuppgifter** dialogrutan, gå till nedladdningsplatsen, Välj din valvautentiseringsuppgifter och klicka på **öppna**.

    Sökvägen till autentiseringsuppgifterna som visas i menyn valvet identifiering. Klicka på **nästa** att gå till inställningen för kryptering.

14. I den **krypteringsinställning** dialogrutan, ange en lösenfras för säkerhetskopiering kryptering och en plats för att lagra lösenfrasen och klickar på **nästa**.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Du kan ange egna lösenfras eller använda lösenfras generatorn för att skapa en åt dig. Lösenfrasen är ditt och Microsoft inte spara eller hantera lösenfrasen. Spara din lösenfras på en tillgänglig plats för att förbereda för en katastrofåterställning.

    När du klickar på **nästa**, Azure Backup-servern har registrerats med Recovery Services-valvet. Installationsprogrammet fortsätter att installera SQL Server och Azure Backup-Server.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. När installationsprogrammet är klart visar statusen att all programvara som har installerats.

    ![Azure Backup-Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    När installationen är klar skapas konsolen Azure Backup Server och Azure Backup Server PowerShell ikoner på serverskrivbordet.

## <a name="add-backup-storage"></a>Lägg till lagring för säkerhetskopiering

Den första säkerhetskopian sparas på lagringsutrymme som är anslutet till Azure Backup-Server-datorn. Läs mer om att lägga till diskar i [lägga till moderna Backup storage](https://docs.microsoft.com/en-us/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Du måste lägga till lagring för säkerhetskopiering, även om du planerar att skicka data till Azure. I Azure Backup-Server-arkitektur Recovery Services-valvet innehåller den *andra* kopia av data under den lokala lagringen innehåller säkerhetskopian första (och obligatoriska).
>
>

## <a name="network-connectivity"></a>Nätverksanslutning

Azure Backup-servern kräver anslutning till Azure Backup-tjänsten för att produkten ska fungera korrekt. Du kan kontrollera om datorn är ansluten till Azure genom att använda den ```Get-DPMCloudConnection``` cmdlet i Azure Backup Server PowerShell-konsolen. Om utdata från cmdleten är TRUE och annan anslutning finns det finns ingen nätverksanslutning.

Azure-prenumerationen måste vara i felfritt tillstånd på samma gång. Ta reda på status för din prenumeration och hantera den, logga in på den [prenumeration portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

När du vet att anslutningen Azure och Azure-prenumerationen kan använda du tabellen nedan ta reda på inverkan på funktionen säkerhetskopiering/återställning.

| Tillstånd för anslutning | Azure-prenumeration | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställa från Azure | Återställa från disken |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Active |Behörig |Behörig |Behörig |Behörig |
| Ansluten |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppad och Azure återställningspunkter tas bort |Stoppad |
| Förlorade anslutningen > 15 dagar |Active |Stoppad |Stoppad |Behörig |Behörig |
| Förlorade anslutningen > 15 dagar |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Förlorade anslutningen > 15 dagar |Avetableras |Stoppad |Stoppad |Stoppad och Azure återställningspunkter tas bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställa från anslutningsproblem

Om en brandvägg eller en proxy som hindrar åtkomst till Azure, godkända följande domän-adresser i brandväggen/proxy-profil:

- www.msftncsi.com
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

När anslutningen till Azure har återställts till Azure Backup-Server, anger de åtgärder som kan utföras tillståndet Azure-prenumeration. När servern är **ansluten**, använda tabellen i [nätverksanslutningar](backup-mabs-install-azure-stack.md#network-connectivity) att se de tillgängliga åtgärderna.

### <a name="handling-subscription-states"></a>Hantera prenumerationstillstånd

Det är möjligt att ändra en Azure-prenumeration från *utgångna* eller *Deprovisioned* till *Active* tillstånd. Prenumerationsstatusen är inte *Active*:

- När en prenumeration är *Deprovisioned*, det förlorar funktioner. Återställa prenumerationen *Active*, revives funktionen säkerhetskopiering/återställning. Om du säkerhetskopierade data på den lokala disken behölls med en tillräckligt lång Bevarandeperiod som säkerhetskopierade data kan hämtas. Säkerhetskopierade data i Azure är dock oåterkalleligen förlorade när prenumerationen försätts den *Deprovisioned* tillstånd.
- När en prenumeration är *utgångna*, det förlorar funktioner. Schemalagda säkerhetskopieringar körs inte när en prenumeration är *utgångna*.

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup-servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning), finns det [fel koder dokumentet](https://support.microsoft.com/kb/3041338).
Du kan också läsa [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

I artikeln [förbereda din miljö för DPM](https://docs.microsoft.com/en-us/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), innehåller information om Azure Backup Server-konfigurationer som stöds.

Du kan använda följande artiklar för att få en djupare förståelse för skydd av arbetsbelastning med hjälp av Microsoft Azure Backup-Server.

- [SQL Server-säkerhetskopiering](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sql-azure-stack)
- [SharePoint server-säkerhetskopiering](https://docs.microsoft.com/en-us/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativ server-säkerhetskopiering](backup-azure-alternate-dpm-server.md)
