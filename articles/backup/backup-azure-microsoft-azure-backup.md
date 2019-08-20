---
title: Använda Azure Backup Server för att säkerhetskopiera arbets belastningar till Azure
description: Använd Azure Backup Server för att skydda eller säkerhetskopiera arbets belastningar till Azure Portal.
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 18eacc914a30dc6004bef71e9d3910e170c43d9b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575136"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installera och uppgradera Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Den här artikeln förklarar hur du förbereder din miljö för att säkerhetskopiera arbets belastningar med hjälp av Microsoft Azure Backup Server (MABS). Med Azure Backup Server kan du skydda program arbets belastningar, till exempel virtuella Hyper-V-datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol.

> [!NOTE]
> Azure Backup Server kan nu skydda virtuella VMware-datorer och ger förbättrade säkerhetsfunktioner. Installera produkten enligt beskrivningen i avsnitten nedan och den senaste Azure Backup agenten. Mer information om hur du säkerhetskopierar VMware-servrar med Azure Backup Server finns i artikeln [använda Azure Backup Server för att säkerhetskopiera en VMware-Server](backup-azure-backup-server-vmware.md). Mer information om säkerhetsfunktioner finns i [dokumentationen om säkerhets funktioner i Azure Backup](backup-azure-security-feature.md).
>
>

MABS som distribueras i en virtuell Azure-dator kan säkerhetskopiera virtuella datorer i Azure, men de bör vara i samma domän för att aktivera säkerhets kopiering. Processen för att återställa en virtuell Azure-dator förblir likadan som säkerhets kopiering av virtuella datorer lokalt, men distribution av MABS i Azure har vissa begränsningar. Mer information om begränsningar finns i [DPM som en virtuell Azure-dator](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure har två distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med Resource Manager-modellen.
>
>

Azure Backup Server ärver många av funktionerna för säkerhets kopiering av arbets belastning från Data Protection Manager (DPM). Den här artikeln länkar till DPM-dokumentationen för att förklara några av de delade funktionerna. Även om Azure Backup Server delar mycket av samma funktioner som DPM, kan Azure Backup Server inte säkerhetskopiera till band eller integrera den med System Center.

## <a name="choose-an-installation-platform"></a>Välj en installations plattform
Det första steget för att få Azure Backup Server igång är att konfigurera en Windows Server. Servern kan vara i Azure eller lokalt.

### <a name="using-a-server-in-azure"></a>Använda en server i Azure
När du väljer en server för att köra Azure Backup Server rekommenderar vi att du börjar med en Galleri avbildning av Windows Server 2012 R2 Data Center, Windows Server 2016 Data Center eller Windows Server 2019 Data Center. Artikeln [skapar din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)och innehåller en självstudie för att komma igång med den rekommenderade virtuella datorn i Azure, även om du aldrig har använt Azure tidigare. De rekommenderade minimi kraven för virtuell Server-dator (VM) bör vara: Standard_A4_v2 med fyra kärnor och 8 GB RAM-minne.

Att skydda arbets belastningar med Azure Backup Server har många olika delarna. Artikeln [Installera DPM som en virtuell Azure-dator, vilket](https://technet.microsoft.com/library/jj852163.aspx)hjälper dig att förklara dessa olika delarna. Innan du distribuerar datorn kan du läsa den här artikeln helt och hållet.

### <a name="using-an-on-premises-server"></a>Använda en lokal server
Om du inte vill köra bas servern i Azure kan du köra servern på en virtuell Hyper-V-dator, en virtuell VMware-dator eller en fysisk värd. De rekommenderade minimi kraven för server maskin varan är två kärnor och 8 GB RAM-minne. De operativ system som stöds visas i följande tabell:

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitars |Standard, data Center, Essentials (MABS v3 och senare) |
| Windows Server 2016 och senaste SPs |64-bitars |Standard, data Center, Essentials (MABS v2 och senare) |
| Windows Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Datacenter, Foundation |
| Windows Storage Server 2012 R2 och senaste Service Pack |64-bitars |Standard, Workgroup |
| Windows Storage Server 2012 och senaste Service Pack |64-bitars |Standard, Workgroup |

Du kan deduplicera DPM-lagringen med Windows Server-deduplicering. Lär dig mer om hur [DPM och deduplicering](https://technet.microsoft.com/library/dn891438.aspx) fungerar tillsammans när de distribueras i virtuella Hyper-V-datorer.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad server med ett enda syfte. Du kan inte installera Azure Backup Server på:
> - En dator som körs som en domänkontrollant
> - En dator där programserverrollen är installerad
> - En dator som är en System Center Operations Manager-hanteringsserver
> - En dator som kör Exchange Server
> - En dator som är en nod i ett kluster

Anslut alltid Azure Backup Server till en domän. Om du planerar att flytta servern till en annan domän installerar du Azure Backup Server först och ansluter sedan servern till den nya domänen. Det finns *inte stöd*för att flytta en befintlig Azure Backup Server-dator till en ny domän efter distribution.

Oavsett om du skickar säkerhets kopierings data till Azure eller om du behåller dem lokalt måste Azure Backup Server registreras med ett Recovery Services-valv.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering
Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv Geo-redundant lagring. Om valvet är ditt primära valv lämnar du lagrings alternativet inställt på Geo-redundant lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Under avsnittet **Inställningar** klickar du på **Egenskaper**.
2. I **Egenskaper**, under **säkerhets kopierings konfiguration**, klickar du på **Uppdatera**.

3. Välj typ av lagrings replikering och klicka på **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

 

## <a name="software-package"></a>Program varu paket
### <a name="downloading-the-software-package"></a>Laddar ned programpaketet
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Om du redan har ett Recovery Services-valv öppet går du vidare till steg 3. Om du inte har ett Recovery Services-valv öppet, men är i Azure Portal klickar du på **Bläddra**i huvud menyn.

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Bladet **Inställningar** öppnas som standard. Om den är stängd klickar du på **Inställningar** för att öppna bladet inställningar.

    ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klicka på **säkerhets kopiering** för att öppna guiden komma igång.

    ![Komma igång med säkerhets kopiering](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    På bladet **komma igång med säkerhets kopia** som öppnas, väljs **säkerhets kopierings mål** automatiskt.

    ![Säkerhets kopiering-mål – standard – öppnade](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. På bladet **säkerhets kopierings mål** väljer du **lokalt**på menyn **där är din arbets belastning som körs** .

    ![lokalt och arbets belastningar som mål](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    På list menyn **vad vill du säkerhetskopiera?** väljer du de arbets belastningar som du vill skydda med Azure Backup Server och klickar sedan på **OK**.

    Guiden **komma igång med säkerhets kopiering** växlar till alternativet **Förbered infrastruktur** för att säkerhetskopiera arbets belastningar till Azure.

   > [!NOTE]
   > Om du bara vill säkerhetskopiera filer och mappar rekommenderar vi att du använder Azure Backup-agenten och följer anvisningarna i artikeln, [först: säkerhetskopiera filer och mappar](backup-try-azure-backup-in-10-mins.md). Om du ska skydda fler än filer och mappar, eller om du planerar att utöka skydds behoven i framtiden, väljer du dessa arbets belastningar.
   >
   >

    ![Ändring av guiden Komma igång](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. På bladet **Förbered infrastruktur** som öppnas klickar du på länken **Hämta** Länkar för installation Azure Backup Server och laddar ned autentiseringsuppgifter för valvet. Du använder autentiseringsuppgifterna för valvet under registreringen av Azure Backup Server i Recovery Services-valvet. Länkarna tar dig till Download Center där du kan ladda ned program varu paketet.

    ![Förbered infrastruktur för Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Markera alla filer och klicka på **Nästa**. Hämta alla filer som kommer från sidan Microsoft Azure Backup hämtning och placera alla filer i samma mapp.

    ![Hämta Center 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Eftersom nedladdnings storleken för alla filer tillsammans är > 3G, kan det ta upp till 60 minuter för nedladdnings länken på 10 Mbit/s att slutföra nedladdningen.

### <a name="extracting-the-software-package"></a>Extrahera programpaketet
När du har hämtat alla filer klickar du på **MicrosoftAzureBackupInstaller. exe**. Då startas **guiden Microsoft Azure Backup installations guiden** för att extrahera installationsfilerna till en plats som du har angett. Fortsätt genom guiden och klicka på extraherings knappen för att starta extraherings processen.

> [!WARNING]
> Det krävs minst 4 GB ledigt utrymme för att extrahera installationsfilerna.
>
>

![Microsoft Azure Backup installations guiden](./media/backup-azure-microsoft-azure-backup/extract/03.png)

När extraherings processen har slutförts markerar du kryss rutan för att starta den nyextraherade filen *Setup. exe* för att börja installera Microsoft Azure Backup Server och klickar på knappen **Slutför** .

### <a name="installing-the-software-package"></a>Installera programpaketet
1. Klicka på **Microsoft Azure Backup** för att starta installations guiden.

    ![Microsoft Azure Backup installations guiden](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Klicka på knappen **Nästa** på Välkomst skärmen. Detta tar dig till avsnittet *krav kontroll* . På den här skärmen klickar du på **kontrol lera** för att avgöra om maskin-och program varu kraven för Azure Backup Server är uppfyllda. Om alla krav har uppfyllts visas ett meddelande som anger att datorn uppfyller kraven. Klicka på knappen **Nästa** .

    ![Azure Backup Server-Välkommen och krav kontroll](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup Server kräver SQL Server Enterprise. Dessutom levereras Azure Backup Server installations paket med lämpliga SQL Server binärfiler som behövs om du inte vill använda din egen SQL. När du börjar med en ny Azure Backup Server installation bör du välja alternativet **installera ny instans av SQL Server med den här installationen** och klicka på knappen **kontrol lera och installera** . När de nödvändiga komponenterna har installerats klickar du på **Nästa**.

    ![Azure Backup Server-SQL-kontroll](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Om ett fel inträffar med en rekommendation om att starta om datorn gör du det och klickar på **kontrol lera igen**. Om det finns några problem med SQL-konfigurationen kan du konfigurera om SQL enligt SQL-rikt linjerna och försöka installera/uppgradera MABS med hjälp av den befintliga SQL-instansen.

   > [!NOTE]
   > Azure Backup Server fungerar inte med en fjärran sluten SQL Server instans. Den instans som används av Azure Backup Server måste vara lokal. Om du använder en befintlig SQL Server för MABS stöder MABS-installationen endast användningen av *namngivna instanser* av SQL Server.

   **Manuell konfiguration**

   När du använder en egen SQL-instans kontrollerar du att du lägger till builtin\Administrators till sysadmin-rollen till Master-databasen.

    **SSRS-konfiguration med SQL 2017**

    När du använder din egen instans av SQL 2017 måste du konfigurera SSRS manuellt. Efter SSRS-konfigurationen kontrollerar du att egenskapen *IsInitialized* för SSRS har angetts till *True*. Om detta är inställt på Sant förutsätter MABS att SSRS redan har kon figurer ATS och kommer att hoppa över SSRS-konfigurationen.

    Använd följande värden för SSRS-konfiguration: 
    - Tjänst konto: Använd det inbyggda kontot måste vara nätverks tjänst
    - Webb tjänst-URL: ' Virtuell katalog ' ska vara ReportServer_<SQLInstanceName>
    - Databas DatabaseName ska vara ReportServer $<SQLInstanceName>
    - Webb portal-URL: ' Virtuell katalog ' ska vara Reports_<SQLInstanceName>

    [Läs mer](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) om SSRS-konfiguration.

4. Ange en plats för installationen av Microsoft Azure Backup Server-filerna och klicka på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Arbets platsen är ett krav för säkerhets kopiering till Azure. Se till att arbets platsen är minst 5% av de data som planeras att säkerhets kopie ras till molnet. För disk skydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i Konfigurera lagringspooler [och disk lagring](https://technet.microsoft.com/library/hh758075.aspx).
5. Ange ett starkt lösen ord för begränsade lokala användar konton och klicka på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Välj om du vill använda *Microsoft Update* för att söka efter uppdateringar och klicka på **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att du Windows Update omdirigera till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Granska *sammanfattningen av inställningarna* och klicka på **Installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Installationen sker i faser. I den första fasen installeras Microsoft Azure Recovery Services-agenten på-servern. Guiden kontrollerar också om det är en Internet anslutning. Om Internet anslutningen är tillgänglig kan du fortsätta med installationen, om inte behöver du ange information om proxyn för att ansluta till Internet.

    Nästa steg är att konfigurera Microsoft Azure Recovery Services agenten. Som en del av konfigurationen måste du ange dina autentiseringsuppgifter för valvet för att registrera datorn i Recovery Services-valvet. Du får också en lösen fras för att kryptera/dekryptera data som skickas mellan Azure och dina lokaler. Du kan generera en lösen fras automatiskt eller ange en egen lösen fras för lösen ord på 16. Fortsätt med guiden tills agenten har kon figurer ATS.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. När registreringen av Microsoft Azure Backup servern har slutförts fortsätter den övergripande installations guiden till installationen och konfigurationen av SQL Server och Azure Backup Server-komponenterna. När installationen av SQL Server-komponenten är klar installeras Azure Backup Server-komponenterna.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

När installations steget har slutförts kommer produktens Skriv bords ikoner också att skapas. Dubbelklicka bara på ikonen för att starta produkten.

### <a name="add-backup-storage"></a>Lägg till säkerhets kopierings lagring
Den första säkerhets kopian sparas på lagrings platsen som är ansluten till Azure Backup Server datorn. Mer information om hur du lägger till diskar finns i [Konfigurera lagringspooler och disk lagring](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Du måste lägga till säkerhets kopierings lagring även om du planerar att skicka data till Azure. I den aktuella arkitekturen för Azure Backup Server innehåller Azure Backup-valvet den *andra* kopian av data medan den lokala lagrings platsen innehåller den första (och obligatoriska) säkerhets kopian.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installera och uppdatera Data Protection Manager Protection Agent

MABS använder System Center Data Protection Manager Protection Agent. [Här följer stegen](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) för att installera skydds agenten på skydds servrarna.

I följande avsnitt beskrivs hur du uppdaterar skydds agenter för klient datorer.

1. I administratörskonsol säkerhets kopierings server väljer > du hanterings**agenter**.

2. I visningsfönstret väljer du de klient datorer som du vill uppdatera skydds agenten för.

   > [!NOTE]
   > Kolumnen **agent uppdateringar** visar när en uppdatering av skydds agenten är tillgänglig för varje skyddad dator. I fönstret **åtgärder** är uppdaterings åtgärden bara tillgänglig när en skyddad dator är vald och uppdateringar är tillgängliga.
   >
   >

3. Om du vill installera uppdaterade skydds agenter på de valda datorerna går du till fönstret **åtgärder** och väljer **Uppdatera**.

4. För en klient dator som inte är ansluten till nätverket, tills datorn är ansluten till nätverket, visar kolumnen **agent status** statusen **Väntande uppdatering väntar**.

   När en klient dator är ansluten till nätverket visar kolumnen **agent uppdateringar** för klient datorn statusen **uppdatering**.

## <a name="move-mabs-to-a-new-server"></a>Flytta MABS till en ny server

Här följer några steg om du behöver flytta MABS till en ny server, samtidigt som lagringen behålls. Detta kan bara göras om alla data finns på Modern Backup Storage.


  > [!IMPORTANT]
  > - Det nya Server namnet måste ha samma namn som den ursprungliga Azure Backup Server-instansen. Du kan inte ändra namnet på den nya Azure Backup Server-instansen om du vill använda den tidigare lagringspoolen och MABS-databasen (DPMDB) för att behålla återställnings punkter.
  > - Du måste ha en säkerhets kopia av MABS-databasen (DPMDB). Du måste återställa databasen.

1. I visningsfönstret väljer du de klient datorer som du vill uppdatera skydds agenten för.
2. Stäng av den ursprungliga Azure Backup-servern eller ta bort den från kabeln.
3. Återställ dator kontot i Active Directory.
4. Installera Server 2016 på den nya datorn och ge den namnet samma dator namn som den ursprungliga Azure Backup servern.
5. Anslut till domänen
6. Installera Azure Backup Server v2 eller senare (flytta MABS Storage pool disks från den gamla servern och importera)
7. Återställ DPMDB som tagits i steg 1.
8. Koppla lagringen från den ursprungliga säkerhets kopierings servern till den nya servern.
9. Från SQL återställa DPMDB
10. Från administratörs kommando rad på ny Server-CD till Microsoft Azure Backup installera plats och bin-mapp

    Exempel på sökväg: C:\Windows\System32 > CD "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Till Azure Backup kör du DPMSYNC-SYNC

    Om du har lagt till nya diskar i DPM-lagringspoolen i stället för att flytta de gamla, kör du DPMSYNC-reallocateReplica

## <a name="network-connectivity"></a>Nätverks anslutning
Azure Backup Server kräver anslutning till Azure Backups tjänsten för att produkten ska fungera korrekt. Du kan kontrol lera om datorn har anslutningen till Azure genom att använda ```Get-DPMCloudConnection``` cmdleten i Azure Backup Server PowerShell-konsolen. Om utdata från cmdleten är TRUE finns det en anslutning, annars finns det ingen anslutning.

På samma gången måste Azure-prenumerationen vara i felfritt tillstånd. Logga in på [prenumerations portalen](https://account.windowsazure.com/Subscriptions)för att ta reda på status för din prenumeration och hantera den.

När du känner till statusen för Azure-anslutningen och Azure-prenumerationen kan du använda tabellen nedan för att ta reda på hur de funktioner för säkerhets kopiering/återställning som erbjuds fungerar.

| Anslutnings tillstånd | Azure-prenumeration | Säkerhetskopiera till Azure | Säkerhetskopiera till disk | Återställa från Azure | Återställa från disk |
| --- | --- | --- | --- | --- | --- |
| Ansluten |Aktiv |Behörig |Behörig |Behörig |Behörig |
| Ansluten |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Ansluten |Avetableras |Stoppad |Stoppad |Stoppade och Azure-återställnings punkter har tagits bort |Stoppad |
| Förlorad anslutning > 15 dagar |Aktiv |Stoppad |Stoppad |Behörig |Behörig |
| Förlorad anslutning > 15 dagar |Har upphört att gälla |Stoppad |Stoppad |Behörig |Behörig |
| Förlorad anslutning > 15 dagar |Avetableras |Stoppad |Stoppad |Stoppade och Azure-återställnings punkter har tagits bort |Stoppad |

### <a name="recovering-from-loss-of-connectivity"></a>Återställning från förlust av anslutning
Om du har en brand vägg eller en proxy som förhindrar åtkomst till Azure måste du tillåta följande domän adresser i brand väggens/proxy-profilen:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

När anslutningen till Azure har återställts till den Azure Backup Server datorn, bestäms de åtgärder som kan utföras av Azures prenumerations tillstånd. Tabellen ovan innehåller information om de åtgärder som tillåts när datorn är ansluten.

### <a name="handling-subscription-states"></a>Hantera prenumerations tillstånd
Det går att ta en Azure-prenumeration från ett utgånget eller avetablerat tillstånd till det *aktiva* tillståndet. Detta har dock vissa konsekvenser för produktens beteende medan statusen inte är *aktiv*:

* En *avetablerad* prenumeration förlorar funktioner under den period som den avetableras. När den *aktive*ras är produkt funktionerna i Backup/Restore återupplivat. Du kan också hämta säkerhets kopierings data på den lokala disken om den behålls med en tillräckligt stor kvarhållningsperiod. Säkerhets kopierings data i Azure är dock irretrievably förlorade när prenumerationen övergår till *avetablerat* tillstånd.
* En utgången prenumeration förlorar bara funktioner för tills den har gjorts *aktiv* igen. Eventuella säkerhets kopieringar som är schemalagda för den period då prenumerationen gick *ut* kommer inte att köras.

## <a name="upgrade-mabs"></a>Uppgradera MABS
Använd följande procedurer för att uppgradera MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uppgradera från MABS v2 till v3

> [!NOTE]
>
> MABS v2 är inte ett krav för att installera MABS v3. Du kan dock endast uppgradera till MABS v3 från MABS v2.

Använd följande steg för att uppgradera MABS:

1. Uppgradera operativ systemet till Windows Server 2016 eller Windows Server 2019 om det behövs för att uppgradera från MABS v2 till MABS v3.

2. Uppgradera servern. Stegen liknar [installationen](#install-and-upgrade-azure-backup-server). Men för SQL-inställningar kommer du att få ett alternativ för att uppgradera SQL-instansen till SQL 2017 eller använda din egen instans av SQL Server 2017.

   > [!NOTE]
   >
   > Avsluta inte medan SQL-instansen håller på att uppgraderas, avinstallerar SQL repor ting-instansen och ett försök att uppgradera MABS kommer därför att Miss lyckas.

   Viktiga saker att tänka på:

   > [!IMPORTANT]
   >
   >  Som en del av SQL 2017-uppgraderingen, säkerhetskopierar vi SQL-krypterings nycklarna och avinstallerar repor ting Services. Efter SQL Server-uppgraderingen har repor ting service (14.0.6827.4788) installerats & krypterings nycklar återställs.
   >
   > När du konfigurerar SQL 2017 manuellt läser du avsnittet om *SSRS-konfiguration med SQL 2017* under installationsinstruktioner.

3. Uppdatera skydds agenterna på de skyddade servrarna.
4. Säkerhets kopieringarna bör fortsätta utan att du behöver starta om produktions servrarna.
5. Du kan börja skydda dina data nu. Om du uppgraderar till Modern Backup Storage, samtidigt som du skyddar, kan du även välja de volymer som du vill lagra säkerhets kopiorna i och kontrol lera under allokerat utrymme. [Läs mer](backup-mabs-add-storage.md).

> [!NOTE]
>
> Om du uppgraderar från MABS v1 till v2 kontrollerar du att operativ systemet är Windows Server 2016 eller Windows Server 2012 R2. Om du vill dra nytta av nya funktioner som System Center 2016 Data Protection Manager Modern Backup Storage måste du installera backup server v2 på Windows Server 2016. Innan du uppgraderar till eller installerar säkerhets kopierings Server v2 bör du läsa om de [installations krav](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) som gäller för Mabs.

## <a name="troubleshooting"></a>Felsökning
Om Microsoft Azure Backup-servern Miss lyckas med fel under installations fasen (eller säkerhets kopiering eller återställning) läser du det här [fel kods dokumentet](https://support.microsoft.com/kb/3041338) för mer information.
Du kan också läsa [Azure Backup relaterade vanliga frågor och svar](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg
Du kan få detaljerad information om hur du [förbereder din miljö för DPM](https://technet.microsoft.com/library/hh758176.aspx) på Microsoft TechNet-webbplatsen. Den innehåller också information om vilka konfigurationer som stöds där Azure Backup Server kan distribueras och användas. Du kan använda en serie [PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) för att utföra olika åtgärder.

Du kan använda de här artiklarna för att få en djupare förståelse för arbets belastnings skyddet med hjälp av Microsoft Azure Backup Server.

* [SQL Server säkerhets kopiering](backup-azure-backup-sql.md)
* [Säkerhets kopiering av SharePoint Server](backup-azure-backup-sharepoint.md)
* [Alternativ server säkerhets kopiering](backup-azure-alternate-dpm-server.md)
