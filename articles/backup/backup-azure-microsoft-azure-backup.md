---
title: Säkerhetskopiera arbetsbelastningar till Azure med hjälp av Azure Backup Server
description: Använda Azure Backup Server för att skydda eller säkerhetskopiera arbetsbelastningar till Azure-portalen.
services: backup
author: PVRK
manager: shivamg
keywords: Azure backup server. skydda arbetsbelastningar; Säkerhetskopiera arbetsbelastningar
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: f6a6a1deb55bf16c65982c0d58cd6d92559596af
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52728284"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installera och uppgradera Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Den här artikeln beskriver hur du förbereder din miljö för att säkerhetskopiera arbetsbelastningar med Microsoft Azure Backup Server (MABS). Med Azure Backup Server kan du skydda arbetsbelastningar för program som Hyper-V-datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol.

> [!NOTE]
> Azure Backup Server kan nu skydda virtuella VMware-datorer och ger funktioner för förbättrad säkerhet. Installera produkten som beskrivs i avsnitten nedan och den senaste Azure Backup-agenten. Mer information om hur du säkerhetskopierar VMware-servrar med Azure Backup Server finns i artikeln [använda Azure Backup Server att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md). Mer information om funktioner för säkerhet, referera till [Azure backup säkerhetsfunktioner dokumentation](backup-azure-security-feature.md).
>
>

Du kan även skydda infrastruktur som en tjänst (IaaS)-arbetsbelastningar som till exempel virtuella datorer i Azure.

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med hjälp av Resource Manager-modellen.
>
>

Azure Backup Server ärver programmering av mycket av den arbetsbelastning säkerhetskopiering från Data Protection Manager (DPM). Den här innehåller artikellänkar till DPM-dokumentationen som förklarar några av de funktioner som delas. Även om Azure Backup Server delar stor del samma funktioner som DPM, Azure Backup Server inte säkerhetskopiera till band, och inte heller integreras den med System Center.

## <a name="choose-an-installation-platform"></a>Välj en installationsplattform för
Det första steget mot Azure Backup Server konfigurera och komma igång är att ställa in en Windows-Server. Servern kan vara i Azure eller lokalt.

### <a name="using-a-server-in-azure"></a>Med en server i Azure
När du väljer en server för att köra Azure Backup Server, bör du börja med en galleri-bild av Windows Server 2012 R2 Datacenter, Windows Server 2016 Datacenter eller Windows Server 2019 Datacenter. I artikeln [skapa din första Windows-dator i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), innehåller en vägledning för att komma igång med den rekommendera virtuella datorn i Azure, även om du aldrig har använt Azure förut. Rekommenderade minimikraven för den server-datorn (VM) ska vara: Standard A2 med två kärnor och 3,5 GB RAM-minne.

Skydda arbetsbelastningar med Azure Backup Server har många nyanser. I artikeln [Install DPM as Azure-datorer](https://technet.microsoft.com/library/jj852163.aspx), bidrar till att klargöra dessa olika delarna. Läs den här artikeln helt innan du distribuerar datorn.

### <a name="using-an-on-premises-server"></a>Med hjälp av en lokal server
Om du inte vill köra grundläggande server i Azure kan du köra servern på en Hyper-V-dator, en VMware VM eller en fysisk värd. Rekommenderade minimikraven för servermaskinvaran är två kärnor och 4 GB RAM-minne. Operativsystem som stöds finns i följande tabell:

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitars |Standard, Datacenter, Essentials (MABS V3 och senare) |
| Windows Server 2016 och senaste Service Pack |64-bitars |Standard, Datacenter, Essentials (MABS V2 och senare) |
| Windows Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Datacenter, Foundation |
| Windows Server 2012 och senaste Service Pack |64-bitars |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Workgroup |
| Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup |

Du kan deduplicera DPM-lagring med hjälp av Windows Server-Deduplicering. Läs mer om hur [DPM och deduplicering](https://technet.microsoft.com/library/dn891438.aspx) fungerar tillsammans när de distribueras i Hyper-V-datorer.

> [!NOTE]
> Azure Backup Server är avsedd att köras på en dedikerad server med enda syfte. Du kan inte installera Azure Backup Server på:
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som är en System Center Operations Manager-hanteringsserver
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Alltid ansluta till Azure Backup Server till en domän. Om du planerar att flytta servern till en annan domän, installera Azure Backup Server först och sedan ansluta servern till den nya domänen. Flytta en befintlig Azure Backup Server-dator till en ny domän när distributionen är *stöds inte*.

Om du skickar säkerhetskopierade data till Azure, eller behåll dem lokalt, måste Azure Backup Server vara registrerad med ett Recovery Services-valv.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder geo-redundant lagring i Recovery Services-valv. Lämna alternativet inställt på geo-redundant lagring om det här valvet är ditt primära valv. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. Välj ditt valv för att öppna instrumentpanelen för valvet och menyn Inställningar. Om den **inställningar** inte öppna menyn, klicka på **alla inställningar** i instrumentpanelen för valvet.
2. På den **inställningar** -menyn klickar du på **infrastruktur för säkerhetskopiering** > **Säkerhetskopieringskonfigurationen** att öppna den **Säkerhetskopieringskonfigurationen**bladet. På den **Säkerhetskopieringskonfigurationen** menyn, Välj alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="software-package"></a>Programpaket
### <a name="downloading-the-software-package"></a>Hämta programpaketet
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Om du redan har ett Recovery Services-valv är öppet, fortsätter du till steg 3. Om du inte har ett Recovery Services-valv är öppet, men finns i Azure-portalen på huvudmenyn klickar du på **Bläddra**.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Den **inställningar** bladet som öppnas som standard. Om den är stängd klickar du på **inställningar** att öppna inställningsbladet.

    ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klicka på **Backup** att öppna guiden komma igång.

    ![Backup komma igång](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    I den **Kom igång med säkerhetskopiering** bladet som öppnas, **säkerhetskopieringsmål** blir automatiskt markerade.

    ![Backup-mål-standard-öppnas](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. I den **säkerhetskopieringsmål** bladet från den **var körs din arbetsbelastning** menyn och välj **lokala**.

    ![lokala och arbetsbelastningar som mål](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Från den **vad vill du säkerhetskopiera?** nedrullningsbara menyn, Välj de arbetsbelastningar du vill skydda med hjälp av Azure Backup Server och klicka sedan på **OK**.

    Den **Kom igång med säkerhetskopiering** guiden växlar den **Förbered infrastruktur** alternativet att säkerhetskopiera arbetsbelastningar till Azure.

   > [!NOTE]
   > Om du bara vill säkerhetskopiera filer och mappar, rekommenderar vi med hjälp av Azure Backup-agenten och följa riktlinjerna i artikeln [första titt: säkerhetskopiera filer och mappar](backup-try-azure-backup-in-10-mins.md). Om du ska skydda fler än filer och mappar, eller om du planerar att expandera protection behoven i framtiden, väljer du dessa arbetsbelastningar.
   >
   >

    ![Komma igång-guiden Ändra](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. I den **Förbered infrastruktur** bladet som öppnas klickar du på den **hämta** länkar för att installera Azure Backup Server och ladda ned valvautentiseringsuppgifter. Du kan använda autentiseringsuppgifterna för valvet under registreringen av Azure Backup Server till recovery services-valvet. Länken tar dig till Download Center där programvarupaketet som kan hämtas.

    ![Förbereda infrastrukturen för Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Markera alla filer och klicka på **nästa**. Ladda ned alla filer som kommer från hämtningssidan för Microsoft Azure Backup och placera alla filer i samma mapp.

    ![Ladda ned center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Eftersom nedladdningen av alla filer tillsammans är > 3G, på en 10 Mbit/s nedladdningslänk det kan ta upp till 60 minuter för att slutföra nedladdningen.

### <a name="extracting-the-software-package"></a>Extrahera programpaketet
När du har laddat ned alla filer, klickar du på **MicrosoftAzureBackupInstaller.exe**. Detta startar den **installationsguiden för Microsoft Azure Backup** att extrahera installationsfiler till en plats som angetts av dig. Fortsätt genom guiden och klicka på den **extrahera** för att starta extraheringsprocessen.

> [!WARNING]
> Minst 4GB ledigt utrymme krävs för att extrahera installationsfilerna.
>
>

![Installationsguiden för Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract/03.png)

När extraheringsprocessen slutförts, markerar kryssrutan Starta nyligen extraherade *setup.exe* att börja installera Microsoft Azure Backup Server och klicka på den **Slutför** knappen.

### <a name="installing-the-software-package"></a>Installera programpaketet
1. Klicka på **Microsoft Azure Backup** att starta installationsguiden.

    ![Installationsguiden för Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. På välkomstskärmen klickar du på den **nästa** knappen. Då kommer du till den *Kravkontroller* avsnittet. På den här skärmen, klickar du på **Kontrollera** att avgöra om maskin- och krav för Azure Backup Server har uppfyllts. Om alla krav är uppfyllda har, visas ett meddelande om att datorn uppfyller kraven. Klicka på den **nästa** knappen.

    ![Kontrollera Azure Backup Server – Välkommen och förutsättningar](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server kräver SQL Server Enterprise. Installationspaketet för Azure Backup Server kommer dessutom tillsammans med de lämpliga binärfilerna för SQL Server krävs om du inte vill använda din egen SQL. När du börjar med en ny installation av Azure Backup Server ska du välja alternativet **installera ny instans av SQL Server med den här konfigurationen** och klicka på den **kontrollera och installera** knappen. När nödvändiga komponenter har installerats, klickar du på **nästa**.

    ![Azure Backup Server - kontroll av SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Om ett fel inträffar med en rekommendation att starta om datorn, göra det och klickar på **kontrollen igen**. Konfigurera om SQL enligt SQL-riktlinjerna vid eventuella konfigurationsproblem för SQL och försök igen om du vill installera/uppgradera MABS i den befintliga instansen av SQL.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärransluten SQL Server-instans. Den instans som används av Azure Backup Server måste vara lokal. Om du använder en befintlig SQLServer för MABS MABS-installationen endast stöder användning av *namngivna instanser* av SQLServer.

   **Manuell konfiguration**

   När du använder en egen instans av SQL, kontrollera att du lägger till builtin\Administrators till sysadmin-rollen till huvud-DB.

    **SSRS-konfiguration med SQL-2017**

    När du använder egna instans av SQL 2017, måste du manuellt konfigurera SSRS. Efter konfigurationen av SSRS, se till att *IsInitialized* av SSRS egenskapen till *SANT*. När detta är inställt på True, förutsätter MABS att SSRS har redan konfigurerats och hoppar över SSRS-konfigurationen.

    Använd följande värden för SSRS-konfiguration:

        - Service Account: ‘Use built-in account’ should be Network Service
        - Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>
        - Database: DatabaseName should be ReportServer$<SQLInstanceName>
        - Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    [Läs mer](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) om SSRS-konfiguration.

4. Ange en plats för installationen av Microsoft Azure Backup server-filer och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Den tillfälliga platsen är ett krav för säkerhetskopiering till Azure. Kontrollera att den tillfälliga platsen är minst 5% av de data som ska säkerhetskopieras till molnet. För skydd på disk måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [konfigurera lagringspooler och disklagring](https://technet.microsoft.com/library/hh758075.aspx).
5. Ange ett starkt lösenord för begränsade lokala användarkonton och klicka på **nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Välj om du vill använda *Microsoft Update* att söka efter uppdateringar och klicka på **nästa**.

   > [!NOTE]
   > Vi rekommenderar att du har Windows Update som omdirigerar till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Granska den *sammanfattning av inställningar* och klicka på **installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Installationen sker i faser. I den första fasen installerad Microsoft Azure Recovery Services-agenten på servern. Guiden söker också efter Internetanslutning. Om Internet-anslutning är tillgänglig du kan fortsätta med installationen, annars måste du ange proxyinformationen att ansluta till Internet.

    Nästa steg är att konfigurera Microsoft Azure Recovery Services-agenten. Som en del av konfigurationen kommer du behöva ange dina valvautentiseringsuppgifter för att registrera datorn till recovery services-valvet. Du kan också ange en lösenfras för att kryptera/dekryptera data som skickas mellan Azure och ditt lokala nätverk. Du kan automatiskt generera en lösenfras eller ange minst 16 tecken-lösenfrasen. Fortsätta med guiden förrän agenten har konfigurerats.

    ![Azure Backup Serer PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. När registreringen av Microsoft Azure Backup server har slutförts, fortsätter övergripande installationsguiden med installationen och konfigurationen av SQL Server och Azure Backup Server-komponenter. Azure Backup Server-komponenterna installeras när komponenten SQL Server-installationen har slutförts.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

När installationen steg har slutförts, kommer produktens skrivbordsikoner har skapats samt. Bara dubbelklicka på ikonen för att starta produkten.

### <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior
Den första säkerhetskopian sparas på lagring som är anslutna till Azure Backup Server-datorn. Läs mer om att lägga till diskar i [konfigurera lagringspooler och disklagring](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Du måste lägga till lagring av säkerhetskopior, även om du planerar att skicka data till Azure. I den aktuella Azure Backup Server-arkitekturen, Azure Backup-valvet har kvar den *andra* kopia av data medan den lokala lagringen innehåller säkerhetskopian första (och obligatoriska).
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installera och uppdatera Data Protection Manager protection-agenten

MABS använder System Center Data Protection Manager protection-agenten. [Här följer stegen](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) att installera Skyddsagenten på dina servrar för skydd.

I följande avsnitt beskrivs hur du uppdaterar skyddsagenter för klientdatorer.

1. I Backup Server-administratörskonsolen väljer **Management** > **agenter**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.

  > [!NOTE]
  > Den **Agentuppdateringar** kolumnen visar när en uppdatering till skyddsagenten finns tillgänglig för varje skyddad dator. I den **åtgärder** fönstret den **uppdatering** åtgärden är tillgänglig endast när en skyddad dator är markerad och uppdateringar är tillgängliga.
  >
  >

3. Installerar uppdaterade skyddsagenter på de valda datorerna i den **åtgärder** väljer **uppdatering**.

4. För en klientdator som inte är ansluten till nätverket, tills datorn är ansluten till nätverket, den **Agentstatus** kolumnen visar statusen **väntande uppdatering**.

  När en klientdator är ansluten till nätverket, den **Agentuppdateringar** kolumn för klientdatorn visar statusen **uppdaterar**.

## <a name="move-mabs-to-a-new-server"></a>Flytta MABS till en ny server

Här följer stegen om du behöver flytta MABS till en ny server, samtidigt som du behåller lagringen. Detta kan göras bara om alla data som finns på Modern Backup Storage.


  > [!IMPORTANT]
  > - Det nya servernamnet måste vara samma namn som den ursprungliga Azure Backup Server-instansen. Du kan inte ändra namnet på den nya Azure Backup Server-instansen om du vill använda tidigare lagringspoolen och Data Protection Manager-databasen för att behålla återställningspunkter.
  > - Du måste ha en säkerhetskopia av Data Protection Manager-databasen. Du måste återställa databasen.

1. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten.
2. Stäng av den ursprungliga Azure säkerhetskopiering server eller starta kabeln.
3. Återställa datorkontot i active directory.
4. Installera Server 2016 på den nya datorn och ge den namnet samma datornamn som den ursprungliga Azure Backup-servern.
5. Ansluta till domänen
6. Installera Azure Backup server V2 eller senare (flytta DPM-lagringspooldiskar från gamla servern och importera)
7. Återställ DPM-databasen som vidtagits i steg 1.
8. Bifoga lagringen från den ursprungliga backup-servern till den nya servern.
9. Återställ DPMDB från SQL
10. Från kommandoraden för administratör på den nya servern cd till Microsoft Azure Backup installerar du platsen och bin-mappen

Sökväg till exempel: C:\windows\system32 > cd ”c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
Säkerhetskopiera till Azure kör DPMSYNC-SYNC

10) Kör DPMSYNC-SYNC Obs Om du har lagt till nya diskar i DPM lagringspoolen i stället för att flytta gamla kör DPMSYNC - Reallocatereplica

## <a name="network-connectivity"></a>Nätverksanslutning
Azure Backup Server kräver anslutning till Azure Backup-tjänsten för produkten som ska fungera korrekt. Du kan kontrollera om datorn är ansluten till Azure genom att använda den ```Get-DPMCloudConnection``` cmdlet i Azure Backup Server PowerShell-konsolen. Om resultatet av cmdlet: en är SANT och sedan anslutning finns, annars det finns ingen nätverksanslutning.

Azure-prenumerationen måste vara i felfritt tillstånd på samma gång. Att ta reda på status för din prenumeration och hantera det, logga in på den [prenumerationsportalen](https://account.windowsazure.com/Subscriptions).

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
Om du har en brandvägg eller en proxy som hindrar åtkomst till Azure måste du godkänna följande domän-adresser i brandväggen/proxy-profil:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

När anslutningen till Azure har återställts till Azure Backup Server-datorn, bestäms vilka åtgärder som kan utföras efter status för Azure-prenumeration. Tabellen ovan innehåller information om de åtgärder som tillåts när datorn är ”ansluten”.

### <a name="handling-subscription-states"></a>Hantering av prenumerationstillstånd
Det är möjligt att ta en Azure-prenumeration från en *har upphört att gälla* eller *avetablerad* tillstånd till den *Active* tillstånd. Men detta har vissa effekter på produkt-beteende eftersom tillståndet inte *Active*:

* En *avetablerad* prenumeration förlorar funktionerna för den period som den datorn avetableras. På Aktivera *Active*, produkt-funktionen för säkerhetskopiering/återställning återställas. Säkerhetskopierade data på den lokala disken kan även hämtas om den behöll med en tillräckligt stor kvarhållningsperiod. Säkerhetskopieringsdata i Azure är dock oåterkalleligen förlorade när prenumerationen försätts den *avetablerad* tillstånd.
* En *har upphört att gälla* prenumeration endast förlorar funktionerna för förrän det har gjorts *Active* igen. Eventuella säkerhetskopior som är schemalagda för perioden som prenumerationen har *har upphört att gälla* körs inte.

## <a name="upgrade-mabs"></a>Uppgradera MABS
Använd följande procedurer för att uppgradera MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uppgradera från MABS V2 till V3

> [!NOTE]

> MABS V2 är inte ett krav för att installera MABS V3. Du kan dock uppgradera till MABS V3 endast från MABS V2.

Använd följande steg för att uppgradera MABS:

1. Om du vill uppgradera från MABS V2 till V3 MABS kan du uppgradera ditt operativsystem till Windows Server 2016 eller Windows Server 2019 om det behövs.

2.  Uppgradera din server. Stegen är liknande [installation](#install-and-upgrade-azure-backup-server). Men för SQL-inställningar får du ett alternativ för att uppgradera SQL-instansen till SQL 2017 eller använda en egen instans av SQLServer 2017.

  > [!NOTE]

  > Avsluta inte medan SQL-instansen håller på att uppgraderas, avslutar avinstalleras SQL reporting-instansen och därför försöker uppgradera igen MABS misslyckas.

  Viktiga saker att tänka på:

  > [!IMPORTANT]

  >  Som en del av SQL 2017 uppgraderingen kan vi säkerhetskopiera krypteringsnycklarna SQL och avinstallera reporting services. Efter uppgraderingen av SQL server reporting service(14.0.6827.4788) installeras & krypteringsnycklar har återställts.

 > När du konfigurerar SQL 2017 manuellt, referera till *SSRS-konfiguration med SQL 2017* avsnittet under installationsanvisningar.

3. Uppdatera skyddsagenter på de skyddade servrarna.
4. Säkerhetskopior bör fortsätta utan att behöva starta om produktionsservrarna.
5. Du kan börja skydda dina data nu. Om du uppgraderar till Modern Backup Storage, samtidigt som du skyddar, kan du också välja de volymer som du vill lagra säkerhetskopior i och söka efter under etablerade utrymme. [Läs mer](backup-mabs-add-storage.md).

> [!NOTE]

> Om du uppgraderar från MABS V1 till V2, kontrollera att ditt operativsystem är Windows Server 2016 eller Windows Server 2012 R2. Om du vill dra nytta av nya funktioner som System Center 2016 Data Protection Manager Modern Backup Storage, måste du installera Backup Server V2 på Windows Server 2016. Innan du uppgraderar till eller installera Backup Server V2, Läs om de [installationskrav](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) gäller för MABS.

## <a name="troubleshooting"></a>Felsökning
Om Microsoft Azure Backup server misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning), referera till denna [fel koder dokumentet](https://support.microsoft.com/kb/3041338) för mer information.
Du kan även gå till [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg
Du kan få detaljerad information [förbereda din miljö för DPM](https://technet.microsoft.com/library/hh758176.aspx) på webbplatsen Microsoft TechNet. Det innehåller även information om konfigurationer som stöds som Azure Backup Server kan distribueras och används. Du kan använda en serie [PowerShell-cmdleten](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) för att utföra olika åtgärder.

Du kan använda dessa artiklar för att få en djupare förståelse för skydd av arbetsbelastningar med hjälp av Microsoft Azure Backup server.

* [SQL Server-säkerhetskopiering](backup-azure-backup-sql.md)
* [SharePoint server-säkerhetskopiering](backup-azure-backup-sharepoint.md)
* [Alternativ server-säkerhetskopiering](backup-azure-alternate-dpm-server.md)
