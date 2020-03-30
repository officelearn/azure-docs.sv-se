---
title: Installera Azure Backup Server på Azure Stack
description: I den här artikeln kan du läsa om hur du använder Azure Backup Server för att skydda eller säkerhetskopiera arbetsbelastningar i Azure Stack.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583443"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Installera Azure Backup Server på Azure Stack

I den här artikeln beskrivs hur du installerar Azure Backup Server på Azure Stack. Med Azure Backup Server kan du skydda IaaS-arbetsbelastningar (Infrastructure as a Service), till exempel virtuella datorer som körs i Azure Stack. En fördel med att använda Azure Backup Server för att skydda dina arbetsbelastningar är att du kan hantera alla arbetsbelastningsskydd från en enda konsol.

> [!NOTE]
> Mer information om säkerhetsfunktioner finns i [dokumentationen till säkerhetsfunktioner för Azure Backup](backup-azure-security-feature.md).
>

## <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server

Azure Backup Server skyddar följande Azure Stack virtuella datorarbetsbelastningar.

| Skyddad datakälla | Skydd och återställning |
| --------------------- | ----------------------- |
| Windows Server Halvårskanal - Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2016 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2012 – Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volymer, filer, mappar |
| SQL Server 2016 | Databas |
| SQL Server 2014 | Databas |
| SQL Server 2012 SP1 | Databas |
| SharePoint 2016 | Servergrupp, databas, frontend, webbserver |
| SharePoint 2013 | Servergrupp, databas, frontend, webbserver |
| SharePoint 2010 | Servergrupp, databas, frontend, webbserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Förutsättningar för Azure Backup Server-miljön

Tänk på rekommendationerna i det här avsnittet när du installerar Azure Backup Server i din Azure Stack-miljö. Azure Backup Server-installationsprogrammet kontrollerar att din miljö har de nödvändiga förutsättningarna, men du sparar tid genom att förbereda innan du installerar.

### <a name="determining-size-of-virtual-machine"></a>Bestämma storleken på den virtuella datorn

Om du vill köra Azure Backup Server på en virtuell Azure Stack-dator använder du storlek A2 eller större. Om du vill ha hjälp med att välja en storlek på den virtuella datorn kan du hämta [storlekskalkylatorn för Azure Stack VM](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuella nätverk på virtuella Azure Stack-datorer

Alla virtuella datorer som används i en Azure Stack-arbetsbelastning måste tillhöra samma virtuella Azure-nätverk och Azure-prenumeration.

### <a name="azure-backup-server-vm-performance"></a>Datorprestanda för Azure Backup Server

Om den delas med andra virtuella datorer påverkar lagringskontostorleken och IOPS-gränserna Azure Backup Server VM-prestanda. Därför bör du använda ett separat lagringskonto för den virtuella azure backup server-datorn. Azure Backup-agenten som körs på Azure Backup Server behöver tillfällig lagring för:

- sitt eget bruk (en cacheplats),
- data som återställts från molnet (lokalt mellanlagringsområde)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurera tillfällig disklagring för Azure Backup

Varje virtuell Azure Stack-dator levereras med tillfällig disklagring, som är tillgänglig för användaren som volym `D:\`. Det lokala mellanlagringsområdet som behövs av Azure `D:\`Backup kan konfigureras för `C:\`att finnas i och cacheplatsen kan placeras på . På så sätt behöver ingen lagring ristas bort från de datadiskar som är anslutna till den virtuella azure backup server-datorn.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhetskopierade data på lokal disk och i Azure

Azure Backup Server lagrar säkerhetskopieringsdata på Azure-diskar som är anslutna till den virtuella datorn, för återställning av drift. När diskarna och lagringsutrymmet är anslutna till den virtuella datorn hanterar Azure Backup Server lagring åt dig. Mängden lagring av säkerhetskopieringsdata beror på antalet diskar som är anslutna till varje [virtuell Azure Stack-dator](/azure-stack/user/azure-stack-storage-overview). Varje storlek på Azure Stack VM har ett maximalt antal diskar som kan kopplas till den virtuella datorn. A2 är till exempel fyra diskar. A3 är åtta diskar. A4 är 16 diskar. Återigen avgör storleken och antalet diskar den totala poolen för säkerhetskopieringslagring.

> [!IMPORTANT]
> Du bör **inte** behålla data för återställning av drift (säkerhetskopiering) på Azure Backup Server-anslutna diskar i mer än fem dagar.
>

Om du lagrar säkerhetskopieringsdata i Azure minskar säkerhetskopieringsinfrastrukturen på Azure Stack. Om data är äldre än fem dagar bör de lagras i Azure.

Om du vill lagra säkerhetskopierade data i Azure skapar eller använder du ett Recovery Services-valv. När du förbereder säkerhetskopiering av Azure Backup Server-arbetsbelastningen konfigurerar du [valvet för Återställningstjänster](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). När det har konfigurerats skapas en återställningspunkt i valvet varje gång ett säkerhetskopieringsjobb körs. Varje Recovery Services-valv rymmer upp till 9999 återställningspunkter. Beroende på antalet återställningspunkter som skapats och hur länge de behålls kan du behålla säkerhetskopierade data i många år. Du kan till exempel skapa månatliga återställningspunkter och behålla dem i fem år.

### <a name="scaling-deployment"></a>Skala distribution

Om du vill skala distributionen har du följande alternativ:

- Skala upp - Öka storleken på den virtuella azure backup server-datorn från A-serien till D-serien och öka det lokala lagringsutrymmet [enligt azure stack-instruktioner för den virtuella datorn](/azure-stack/user/azure-stack-manage-vm-disks).
- Avlasta data – skicka äldre data till Azure och behåll endast de senaste data på lagringen som är kopplad till Azure Backup Server.
- Skala ut - Lägg till fler Azure Backup-servrar för att skydda arbetsbelastningarna.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 eller högre måste installeras på den virtuella datorn.

### <a name="joining-a-domain"></a>Ansluta till en domän

Den virtuella datorn för Azure Backup Server måste anslutas till en domän. En domänanvändare med administratörsbehörighet måste installera Azure Backup Server på den virtuella datorn.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Använda en virtuell IaaS-dator i Azure Stack

När du väljer en server för Azure Backup Server börjar du med en Windows Server 2012 R2 Datacenter- eller Windows Server 2016 Datacenter-galleriavbildning. Artikeln, [Skapa din första virtuella Windows-dator i Azure-portalen,](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)innehåller en självstudiekurs för att komma igång med den rekommenderade virtuella datorn. De rekommenderade minimikraven för serverns virtuella dator (VM) bör vara: A2 Standard med två kärnor och 3,5 GB RAM.

Att skydda arbetsbelastningar med Azure Backup Server har många nyanser. Artikeln, [Installera DPM som en virtuell Azure-dator,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))hjälper till att förklara dessa nyanser. Läs den här artikeln helt innan du distribuerar datorn.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad virtuell dator med ett enda syfte. Det går inte att installera Azure Backup Server på:
>
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Gå alltid med i Azure Backup Server till en domän. Om du behöver flytta Azure Backup Server till en annan domän installerar du först Azure Backup Server och ansluter den sedan till den nya domänen. När du har distribuerat Azure Backup Server kan du inte flytta den till en ny domän.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Med alternativet Återställningstjänster för arkivlagring kan du välja mellan geouppsagbar lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv geoupptundssagt lagringsutrymme. Om det här valvet är ditt primära valv lämnar du lagringsalternativet inställt på geouppsagd lagring. Välj lokalt redundant lagring om du vill ha ett billigare alternativ som är mindre hållbart. Läs mer om [geo-redundanta](../storage/common/storage-redundancy-grs.md) och [lokalt redundanta](../storage/common/storage-redundancy-lrs.md) lagringsalternativ i [Azure Storage replication översikt](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och menyn Inställningar. Om menyn **Inställningar** inte öppnas klickar du på **Alla inställningar** på instrumentpanelen för valvet.
2. Klicka på **Säkerhetskopieringsinfrastrukturkonfiguration** > **Backup Configuration** på **menyn Inställningar** för att öppna menyn Konfiguration **för säkerhetskopiering.** På menyn **Konfiguration för säkerhetskopiering** väljer du alternativet lagringsreplikering för valvet.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Hämta installationsprogrammet för Azure Backup Server

Det finns två sätt att hämta installationsprogrammet för Azure Backup Server. Du kan hämta installationsprogrammet för Azure Backup Server från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55269). Du kan också hämta Installationsprogrammet för Azure Backup Server när du konfigurerar ett Recovery Services-valv. Följande steg går igenom att hämta installationsprogrammet från Azure-portalen medan du konfigurerar ett Recovery Services-valv.

1. Logga in på din Azure Stack-dator [i Azure-portalen](https://portal.azure.com/).
2. Välj **Alla tjänster**på menyn till vänster .

    ![Välj alternativet Alla tjänster i huvudmenyn](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Skriv **Återställningstjänster** i *Recovery Services*dialogrutan Alla tjänster . När du börjar skriva filtrerar indata listan med resurser. När du ser det väljer du **Recovery Services-valv**.

    ![Skriv återställningstjänster i dialogrutan Alla tjänster](./media/backup-mabs-install-azure-stack/all-services.png)

    Listan över Recovery Services-valv i prenumerationen visas.

4. I listan över Recovery Services-valv väljer du valvet för att öppna instrumentpanelen.

    ![Skriv återställningstjänster i dialogrutan Alla tjänster](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. **Öppna** guiden Komma igång på menyn Komma igång på menyn Komma igång.

    ![Säkerhetskopieringen kommer igång](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Menyn för säkerhetskopiering öppnas.

    ![Säkerhetskopiering-mål-standard-öppnas](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Välj **Lokalt**på menyn för säkerhetskopiering på menyn för säkerhetskopieringskörning på menyn För **säkerhetskopiering.** Välj de arbetsbelastningar som du vill skydda med Azure Backup Server på den nedrullningsbara menyn Vad vill du **säkerhetskopiera?** Om du inte är säker på vilka arbetsbelastningar du ska välja väljer du **Virtuella hyper-V-datorer** och klickar sedan på **Förbered infrastruktur**.

    ![lokala och arbetsbelastningar som mål](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Menyn **Förbered infrastruktur** öppnas.

7. Klicka på **Hämta** på menyn **Förbered infrastruktur** för att öppna en webbsida för att hämta installationsfiler för Azure Backup Server.

    ![Ändra guiden Komma igång](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Microsofts webbsida som är värd för de nedladdningsbara filerna för Azure Backup Server öppnas.

8. Välj ett språk på hämtningssidan för Microsoft Azure Backup Server och klicka på **Hämta**.

    ![Download center öppnar](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Azure Backup Server-installationsprogrammet består av åtta filer – en installationsprogram och sju BIN-filer. Markera **Filnamn** om du vill markera alla nödvändiga filer och klicka på **Nästa**. Ladda ned alla filer till samma mapp.

    ![Ladda ner centrum 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Nedladdningsstorleken för alla installationsfiler är större än 3 GB. På en 10-Mbps nedladdningslänk kan det ta upp till 60 minuter att hämta alla installationsfiler. Filerna hämtas till den angivna hämtningsplatsen.

## <a name="extract-azure-backup-server-install-files"></a>Extrahera installationsfiler för Azure Backup Server

När du har hämtat alla filer till den virtuella Azure Stack-datorn går du till hämtningsplatsen. Den första fasen av installationen av Azure Backup Server är att extrahera filerna.

![Ladda ner centrum 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Klicka på **MicrosoftAzureBackupserverInstaller.exe**från listan över nedladdade filer .

    > [!WARNING]
    > Minst 4 GB ledigt utrymme krävs för att extrahera installationsfilerna.
    >

2. Klicka på **Nästa** i guiden Azure Backup Server för att fortsätta.

    ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Välj sökväg för Azure Backup Server-filerna och klicka på **Nästa**.

   ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Kontrollera extraheringsplatsen och klicka på **Extrahera**.

   ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Guiden extraherar filerna och läser installationsprocessen.

   ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. När extraheringsprocessen är klar klickar du på **Slutför**. Som standard är **Kör setup.exe** markerat. När du klickar på **Slutför**installerar Setup.exe Microsoft Azure Backup Server till den angivna platsen.

   ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Installera programvarupaketet

I föregående steg klickade du på **Slutför** för att avsluta extraheringsfasen och starta installationsguiden för Azure Backup Server.

![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server delar kod med Data Protection Manager. Du kommer att se referenser till Data Protection Manager och DPM i Azure Backup Server-installationsprogrammet. Även om Azure Backup Server och Data Protection Manager är separata produkter är dessa produkter nära besläktade.

1. Om du vill starta installationsguiden klickar du på **Microsoft Azure Backup Server**.

   ![Guiden Konfigurera Microsoft Azure](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Klicka på **Nästa** på **välkomstskärmen**.

    ![Azure Backup Server - Välkommen och förutsättningar kontrollera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Klicka på **Kontrollera** på skärmen **Nödvändiga kontroller** för att ta reda på om maskinvaru- och programvaruförutsättningarna för Azure Backup Server har uppfyllts.

    ![Azure Backup Server - Välkommen och förutsättningar kontrollera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Om din miljö har de nödvändiga förutsättningarna visas ett meddelande som anger att datorn uppfyller kraven. Klicka på **Nästa**.  

    ![Azure Backup Server - Nödvändiga kontroll passerade](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Om din miljö inte uppfyller de nödvändiga förutsättningarna anges problemen. De förutsättningar som inte uppfylldes visas också i DpmSetup.log. Lös nödvändiga fel och kör sedan **Kontrollera igen**. Installationen kan inte fortsätta förrän alla förutsättningar är uppfyllda.

    ![Azure Backup Server - installationsförutsättningarna har inte uppfyllts](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server kräver SQL Server. Installationspaketet för Azure Backup Server levereras med lämpliga SQL Server-binärfiler. Om du vill använda din egen SQL-installation kan du. Det rekommenderade valet är dock att låta installationsprogrammet lägga till en ny instans av SQL Server. Om du vill vara säkra på att ditt val fungerar med din miljö klickar du på **Kontrollera och installera**.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en sql server-instans på fjärr. Instansen som används av Azure Backup Server måste vara lokal.
   >

    ![Azure Backup Server - Välkommen och förutsättningar kontrollera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Efter kontroll, om den virtuella datorn har de nödvändiga förutsättningarna för att installera Azure Backup Server, klickar du på **Nästa**.

    ![Azure Backup Server - Välkommen och förutsättningar kontrollera](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Om ett fel inträffar med en rekommendation att starta om datorn, starta om maskinen. När du har startat om datorn startar du om installationsprogrammet och klickar på **Kontrollera igen**när du kommer till skärmen **SQL Settings** .

5. I **installationsinställningarna**anger du en plats för installation av Microsoft Azure Backup-serverfiler och klickar på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Scratch-platsen krävs för att säkerhetskopiera till Azure. Se till att storleken på scratch-platsen motsvarar minst 5 % av de data som planeras att säkerhetskopieras till Azure. För diskskydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [Konfigurera lagringspooler och disklagring](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

6. Ange ett starkt lösenord för begränsade lokala användarkonton på skärmen **Säkerhetsinställningar** och klicka på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. På skärmen **Opt-In i Microsoft Update** väljer du om du vill använda *Microsoft Update* för att söka efter uppdateringar och klicka på **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att Windows Update omdirigeras till Microsoft Update, som erbjuder säkerhetsuppdateringar och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Granska *sammanfattningen av inställningar* och klicka på **Installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    När Azure Backup Server har installerats klart startar installationsprogrammet omedelbart microsoft Azure Recovery Services-agentinstallationsprogrammet.

9. Installationsprogrammet för Microsoft Azure Recovery Services Agent öppnas och söker efter Internet-anslutning. Om Internet-anslutning är tillgänglig fortsätter du med installationen. Om det inte finns någon anslutning anger du proxyinformation för att ansluta till Internet. När du har angett proxyinställningarna klickar du på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Om du vill installera Microsoft Azure Recovery Services Agent klickar du på **Installera**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure Recovery Services-agenten, även kallad Azure Backup-agenten, konfigurerar Azure Backup Server till Recovery Services-valvet. När Azure Backup Server har konfigurerats säkerhetskopieringsdata till samma Recovery Services-valv.

11. När Microsoft Azure Recovery Services-agenten har installerats klickar du på **Nästa** för att starta nästa fas: registrera Azure Backup Server med Recovery Services-valvet.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Installationsprogrammet startar **guiden Registrera server**.

12. Växla till din Azure-prenumeration och ditt Recovery Services-valv. Klicka på **Hämta** på menyn **Förbered infrastruktur** för att hämta autentiseringsuppgifter för valvet. Om **knappen Hämta** i steg 2 inte är aktiv väljer du **Redan nedladdad eller använder den senaste Azure Backup Server-installationen** för att aktivera knappen. Autentiseringsuppgifterna för valvet hämtas till den plats där du lagrar nedladdningar. Var medveten om den här platsen eftersom du behöver den för nästa steg.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Klicka på **Bläddra** på autentiseringsuppgifterna för Återställningstjänster på **Arkividentifieringsmenyn.**

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Gå till hämtningsplatsen i dialogrutan **Välj autentiseringsuppgifter,** välj autentiseringsuppgifter för valvet och klicka på **Öppna**.

    Sökvägen till autentiseringsuppgifterna visas på arkividentifieringsmenyn. Klicka på **Nästa** om du vill gå vidare till krypteringsinställningen.

14. I dialogrutan **Krypteringsinställning** tillhandahåller du en lösenfras för säkerhetskopieringskrypteringen och en plats där lösenfrasen ska lagras och klickar på **Nästa**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Du kan ange dina egna lösenfraser eller använda lösenfrasgeneratorn för att skapa en åt dig. Lösenfrasen är din och Microsoft sparar eller hanterar inte den här lösenfrasen. Om du vill förbereda dig för en katastrof sparar du lösenfrasen på en tillgänglig plats.

    När du klickar på **Nästa**registreras Azure Backup Server i valvet för Återställningstjänster. Installationsprogrammet fortsätter att installera SQL Server och Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. När installationsprogrammet är klart visar statusen att all programvara har installerats.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    När installationen är klar skapas Azure Backup Server-konsolen och Azure Backup Server PowerShell-ikonerna på serverskrivbordet.

## <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior

Den första säkerhetskopian sparas på lagring som är ansluten till Azure Backup Server-datorn. Mer information om hur du lägger till diskar finns i [Lägga till lagring av moderna säkerhetskopior](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> Du måste lägga till lagring för säkerhetskopiering även om du planerar att skicka data till Azure. I Azure Backup Server-arkitekturen innehåller Recovery Services-valvet den *andra* kopian av data medan den lokala lagringen innehåller den första (och obligatoriska) säkerhetskopian.
>
>

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup Server kräver anslutning till Azure Backup-tjänsten för att produkten ska fungera. Om du vill verifiera om datorn har ```Get-DPMCloudConnection``` anslutningen till Azure använder du cmdleten i Azure Backup Server PowerShell-konsolen. Om cmdletens utgång är SANN finns det någon anslutning, annars finns det ingen anslutning.

Samtidigt måste Azure-prenumerationen vara i felfritt tillstånd. Om du vill ta reda på tillståndet för din prenumeration och hantera den loggar du in på [prenumerationsportalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

När du vet tillståndet för Azure-anslutningen och Azure-prenumerationen kan du använda tabellen nedan för att ta reda på effekten på säkerhetskopierings-/återställningsfunktionen som erbjuds.

| Anslutningstillstånd | Azure-prenumeration | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställ från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Active |Tillåtet |Tillåtet |Tillåtet |Tillåtet |
| Ansluten |Upphörd |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Ansluten |Avetablerade |Stoppad |Stoppad |Stoppad och Azure-återställningspunkter har tagits bort |Stoppad |
| Förlorad anslutning > 15 dagar |Active |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Förlorad anslutning > 15 dagar |Upphörd |Stoppad |Stoppad |Tillåtet |Tillåtet |
| Förlorad anslutning > 15 dagar |Avetablerade |Stoppad |Stoppad |Stoppad och Azure-återställningspunkter har tagits bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställa från förlust av anslutning

Om en brandvägg eller en proxy förhindrar åtkomst till Azure lägger du till följande domänadresser i listan över tillåta brandväggs-/proxyprofil:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

När anslutningen till Azure har återställts till Azure Backup Server avgör Azure-prenumerationstillståndet vilka åtgärder som kan utföras. När servern är **ansluten**använder du tabellen i [Nätverksanslutning](backup-mabs-install-azure-stack.md#network-connectivity) för att se tillgängliga åtgärder.

### <a name="handling-subscription-states"></a>Hantera prenumerationstillstånd

Det är möjligt att ändra en Azure-prenumeration från *utgånget* eller *avetablerat* tillstånd till *aktivt* tillstånd. Medan prenumerationstillståndet inte är *aktivt:*

- Medan en prenumeration *avetableras*förlorar den funktionaliteten. Återställa prenumerationen till *Active*, återupplivar säkerhetskopiering / återställning funktionalitet. Om säkerhetskopierade data på den lokala disken behålls med en tillräckligt stor kvarhållningsperiod kan säkerhetskopierade data hämtas. Säkerhetskopierade data i Azure går dock oåterkalleligen förlorade när prenumerationen går in i tillståndet *Avetablerade.*
- När en prenumeration har *upphört att gälla*förlorar den funktionaliteten. Schemalagda säkerhetskopior körs inte när en prenumeration har *upphört att gälla*.

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup-servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning) läser du [felkodsdokumentet](https://support.microsoft.com/kb/3041338).
Du kan också referera till [Azure Backup relaterade vanliga frågor](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

Artikeln, [Förbereda din miljö för DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801), innehåller information om azure backup server-konfigurationer som stöds.

Du kan använda följande artiklar för att få en djupare förståelse för arbetsbelastningsskydd med Hjälp av Microsoft Azure Backup Server.

- [Säkerhetskopiering av SQL Server](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Säkerhetskopiering av SharePoint-server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternativ säkerhetskopiering av servern](backup-azure-alternate-dpm-server.md)
