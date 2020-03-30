---
title: Använda Azure Backup Server för att säkerhetskopiera arbetsbelastningar
description: I den här artikeln kan du lära dig hur du förbereder din miljö för att skydda och säkerhetskopiera arbetsbelastningar med Hjälp av Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273415"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installera och uppgradera Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Gäller: MABS v3. (MABS v2 stöds inte längre. Om du använder en version tidigare än MABS v3, uppgradera till den senaste versionen.)

I den här artikeln beskrivs hur du förbereder din miljö för att säkerhetskopiera arbetsbelastningar med Hjälp av Microsoft Azure Backup Server (MABS). Med Azure Backup Server kan du skydda programarbetsbelastningar som virtuella hyper-virtuella datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol.

> [!NOTE]
> Azure Backup Server kan nu skydda virtuella virtuella datorer med VMware och ger förbättrade säkerhetsfunktioner. Installera produkten enligt beskrivningen i avsnitten nedan och den senaste Azure Backup Agent. Mer information om hur du säkerhetskopierar VMware-servrar med Azure Backup Server finns i artikeln [Använd Azure Backup Server för att säkerhetskopiera en VMware-server](backup-azure-backup-server-vmware.md). Mer information om säkerhetsfunktioner finns i [dokumentationen till Azure backup security features](backup-azure-security-feature.md).
>
>

MABS som distribueras i en virtuell Azure-dator kan säkerhetskopiera virtuella datorer i Azure, men de bör finnas i samma domän för att aktivera säkerhetskopiering. Processen för att stödja en Azure VM förblir samma som att säkerhetskopiera virtuella datorer lokalt, men distribuera MABS i Azure har vissa begränsningar. Mer information om begränsning finns i [DPM som en virtuell Azure-dator](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassiskt](../azure-resource-manager/management/deployment-models.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med hjälp av Resource Manager-modellen.
>
>

Azure Backup Server ärver mycket av arbetsbelastningen backup funktionalitet från Data Protection Manager (DPM). Den här artikeln länkar till DPM-dokumentation för att förklara några av de delade funktionerna. Även om Azure Backup Server delar mycket av samma funktioner som DPM, säkerhetskopierar inte Azure Backup Server till band, och den integreras inte heller med System Center.

## <a name="choose-an-installation-platform"></a>Välj en installationsplattform

Det första steget mot att få igång Azure Backup Server är att konfigurera en Windows Server. Din server kan vara i Azure eller lokalt.

### <a name="using-a-server-in-azure"></a>Använda en server i Azure

När du väljer en server för att köra Azure Backup Server rekommenderar vi att du börjar med en galleriavbildning av Windows Server 2016 Datacenter eller Windows Server 2019 Datacenter. Artikeln, [Skapa din första virtuella Windows-dator i Azure-portalen](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), innehåller en självstudiekurs för att komma igång med den rekommenderade virtuella datorn i Azure, även om du aldrig har använt Azure tidigare. De rekommenderade minimikraven för serverns virtuella dator (VM) bör vara: Standard_A4_v2 med fyra kärnor och 8 GB RAM.

Att skydda arbetsbelastningar med Azure Backup Server har många nyanser. Artikeln, [Installera DPM som en virtuell Azure-dator,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))hjälper till att förklara dessa nyanser. Läs den här artikeln helt innan du distribuerar datorn.

### <a name="using-an-on-premises-server"></a>Använda en lokal server

Om du inte vill köra basservern i Azure kan du köra servern på en Virtuell Hyper-V, en virtuell VMware-dator eller en fysisk värd. The recommended minimum requirements for the server hardware are two cores and 8-GB RAM. Operativsystem som stöds visas i följande tabell:

| Operativsystem | Plattform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64-bitars |Standard, Datacenter, Essentials |
| Windows Server 2016 och de senaste SPs-skivorna |64-bitars |Standard, Datacenter, Essentials  |

Du kan deduplicera DPM-lagringen med hjälp av Windows Server Deduplication. Läs mer om hur [DPM och deduplicering](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) fungerar tillsammans när de distribueras i virtuella hyper-virtuella datorer.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad server för en enda syfte. Det går inte att installera Azure Backup Server på:
>
> * En dator som körs som en domänkontrollant
> * En dator där programserverrollen är installerad
> * En dator som är en System Center Operations Manager-hanteringsserver
> * En dator som kör Exchange Server
> * En dator som är en nod i ett kluster
>
> Installation av Azure Backup Server stöds inte på Windows Server Core eller Microsoft Hyper-V Server.

Gå alltid med i Azure Backup Server till en domän. Om du planerar att flytta servern till en annan domän installerar du Azure Backup Server först och ansluter sedan servern till den nya domänen. Det *går inte*att flytta en befintlig Azure Backup Server-dator till en ny domän efter distributionen .

Oavsett om du skickar säkerhetskopierade data till Azure eller behåller dem lokalt måste Azure Backup Server registreras med ett Recovery Services-valv.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Med alternativet för lagringsreplikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Som standard använder Recovery Services-valv geoupptundssagt lagringsutrymme. Om det här valvet är ditt primära valv lämnar du lagringsalternativet inställt på geouppsagd lagring. Välj lokalt redundant lagring om du vill använda ett billigare alternativ som inte är lika beständigt. Läs mer om [geo-redundanta](../storage/common/storage-redundancy-grs.md) och [lokalt redundanta](../storage/common/storage-redundancy-lrs.md) lagringsalternativ i [Azure Storage replication översikt](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

1. På bladet **Recovery Services-valv** klickar du på det nya valvet. Klicka på **Egenskaper**under avsnittet **Inställningar** .
2. Klicka på **Uppdatera**under **Säkerhetskopieringskonfiguration**i **Egenskaper**.

3. Markera lagringsreplikeringstypen och klicka på **Spara**.

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Programvarupaket

### <a name="downloading-the-software-package"></a>Ladda ner mjukvarupaketet

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Om du redan har ett Recovery Services-valv öppet fortsätter du till steg 3. Om du inte har ett Recovery Services-valv öppet, men finns i Azure-portalen, klickar du på **Bläddra**på huvudmenyn .

   * I listan över resurser skriver du **Recovery Services**.
   * När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

     ![Skapa Recovery Services-valv (steg 1)](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Listan över Recovery Services-valv visas.
   * Välj ett valv i listan över Recovery Services-valv.

     Instrumentpanelen för det valda valvet öppnas.

     ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Bladet **Inställningar** öppnas som standard. Om den är stängd klickar du på **Inställningar** för att öppna inställningsbladet.

    ![Öppna bladet för valvet](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Öppna guiden Komma igång genom att klicka på **Säkerhetskopiera.**

    ![Säkerhetskopieringen kommer igång](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    I **bladet Komma igång med säkerhetskopiering** som öppnas väljs **säkerhetskopieringsmål** automatiskt.

    ![Säkerhetskopiering-mål-standard-öppnas](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Välj Lokalt på menyn **Säkerhetskopieringsmål** i bladet **Säkerhetskopieringsmål.** **On-premises**

    ![lokala och arbetsbelastningar som mål](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Välj de arbetsbelastningar som du vill skydda med Azure Backup Server på den nedrullningsbara menyn **Vad vill du säkerhetskopiera?** **OK**

    Guiden **Komma igång med säkerhetskopiering** växlar alternativet Förbered **infrastruktur** för att säkerhetskopiera arbetsbelastningar till Azure.

   > [!NOTE]
   > Om du bara vill säkerhetskopiera filer och mappar rekommenderar vi att du använder Azure Backup-agenten och följer anvisningarna i artikeln First [look: säkerhetskopiera filer och mappar](backup-try-azure-backup-in-10-mins.md). Om du ska skydda mer än filer och mappar, eller om du planerar att utöka skyddsbehoven i framtiden, väljer du dessa arbetsbelastningar.
   >
   >

    ![Ändra guiden Komma igång](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. I bladet **Förbered infrastruktur** som öppnas klickar du på **autentiseringsuppgifterna** för Hämta filer för Installera Azure Backup Server och Hämta valv. Du använder autentiseringsuppgifterna för valvet under registreringen av Azure Backup Server i återställningstjänstvalvet. Länkarna tar dig till Download Center där mjukvarupaketet kan laddas ner.

    ![Förbereda infrastruktur för Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Markera alla filer och klicka på **Nästa**. Hämta alla filer som kommer in från nedladdningssidan för Microsoft Azure Backup och placera alla filer i samma mapp.

    ![Ladda ner centrum 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Eftersom nedladdningsstorleken för alla filer tillsammans är > 3G, på en 10-Mbps nedladdningslänk kan det ta upp till 60 minuter för nedladdningen att slutföra.

### <a name="extracting-the-software-package"></a>Extrahera mjukvarupaketet

När du har hämtat alla filer klickar du på **MicrosoftAzureBackupInstaller.exe**. Detta startar **installationsguiden** för Microsoft Azure Backup för att extrahera installationsfilerna till en plats som du har angett. Fortsätt genom guiden och klicka på **knappen Extrahera** för att påbörja extraheringsprocessen.

> [!WARNING]
> Minst 4 GB ledigt utrymme krävs för att extrahera installationsfilerna.
>
>

![Guiden Konfigurera Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

När extraheringsprocessen är klar markerar du kryssrutan för att starta den nyligen extraherade *setup.exe* för att börja installera Microsoft Azure Backup Server och klicka på knappen **Slutför.**

### <a name="installing-the-software-package"></a>Installera programvarupaketet

1. Klicka på **Microsoft Azure Backup** om du vill starta installationsguiden.

    ![Guiden Konfigurera Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Klicka på knappen **Nästa** på välkomstskärmen. Detta tar dig till avsnittet *Nödvändiga kontroller.* Klicka på **Kontrollera** på den här skärmen för att ta reda på om maskinvaru- och programvaruförutsättningarna för Azure Backup Server har uppfyllts. Om alla förutsättningar uppfylls visas ett meddelande om att datorn uppfyller kraven. Klicka på knappen **Nästa.**

    ![Azure Backup Server - Välkommen och förutsättningar kontrollera](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Installationspaketet för Azure Backup Server levereras med lämpliga SQL Server-binärfiler som behövs. När du startar en ny Azure Backup Server-installation väljer du alternativet **Installera ny instans av SQL Server med den här installationen** och klickar på knappen Kontrollera och **installera.** När förutsättningarna har installerats klickar du på **Nästa**.

    >[!NOTE]
    >Om du vill använda din egen SQL-server är sql server-versionerna SQL Server 2014 SP1 eller senare, 2016 och 2017 som stöds.  Alla SQL Server-versioner ska vara Standard eller Enterprise 64-bitars.
    >Azure Backup Server fungerar inte med en sql server-instans på fjärr. Instansen som används av Azure Backup Server måste vara lokal. Om du använder en befintlig SQL-server för MABS stöder MABS-installationen endast användningen av *namngivna instanser* av SQL-servern.

    ![Azure Backup Server - SQL-kontroll](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Om ett fel inträffar med en rekommendation om att starta om datorn gör du det och klickar på **Kontrollera igen**. Om det finns några SQL-konfigurationsproblem konfigurerar du om SQL enligt SQL-riktlinjerna och försöker installera/uppgradera MABS igen med den befintliga instansen av SQL.

   **Manuell konfiguration**

   När du använder din egen instans av SQL kontrollerar du att du lägger till inbyggd\Administratörer till sysadmin-rollen i huvud-DB.

    **SSRS-konfiguration med SQL 2017**

    När du använder din egen instans av SQL 2017 måste du konfigurera SSRS manuellt. Efter SSRS-konfiguration, se till att *IsInitialized* egenskap för SSRS är inställd på *True*. När detta är inställt på Sant förutsätter MABS att SSRS redan är konfigurerat och kommer att hoppa över SSRS-konfigurationen.

    Använd följande värden för SSRS-konfiguration:
    * Tjänstkonto: "Använd inbyggt konto" ska vara Network Service
    * Webbtjänst-URL: "Virtual Directory" bör\<vara ReportServer_ SQLInstanceName>
    * Databas: Databasnamn ska vara\<ReportServer$ SQLInstanceName>
    * Url:en för webbportal: "Virtual Directory" ska vara Reports_\<SQLInstanceName>

    [Läs mer](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) om SSRS-konfiguration.

    > [!NOTE]
    > Licensiering för SQL Server som används som databas för MABS regleras av [Microsoft Online Services Terms](https://www.microsoft.com/licensing/product-licensing/products) (OST). Enligt OST kan SQL Server som levereras med MABS endast användas som databas för MABS.

4. Ange en plats för installation av Serverfiler för Microsoft Azure Backup och klicka på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Scratch-platsen är ett krav för säkerhetskopiering till Azure. Se till att scratch-platsen är minst 5 % av de data som planeras att säkerhetskopieras till molnet. För diskskydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i [Konfigurera lagringspooler och disklagring](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Ange ett starkt lösenord för begränsade lokala användarkonton och klicka på **Nästa**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Välj om du vill använda *Microsoft Update* för att söka efter uppdateringar och klicka på **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att Windows Update omdirigeras till Microsoft Update, som erbjuder säkerhetsuppdateringar och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Granska *sammanfattningen av inställningar* och klicka på **Installera**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Installationen sker i etapper. I den första fasen installeras Microsoft Azure Recovery Services Agent på servern. Guiden söker också efter Internet-anslutning. Om internetanslutning är tillgänglig kan du fortsätta med installationen, om inte, måste du ange proxyinformation för att ansluta till Internet.

    Nästa steg är att konfigurera Microsoft Azure Recovery Services Agent. Som en del av konfigurationen måste du ange dina valvautentiseringsuppgifter för att registrera datorn till återställningstjänstvalvet. Du kommer också att tillhandahålla en lösenfras för att kryptera/dekryptera data som skickas mellan Azure och dina lokaler. Du kan automatiskt generera en lösenfras eller ange din egen lösenfras på minst 16 tecken. Fortsätt med guiden tills agenten har konfigurerats.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. När registreringen av Microsoft Azure Backup-servern har slutförts fortsätter den övergripande installationsguiden till installation och konfiguration av SQL Server och Azure Backup Server-komponenterna. När installationen av SQL Server-komponenten är klar installeras Azure Backup Server-komponenterna.

    ![Azure Backup Server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

När installationssteget är klart har även produktens skrivbordsikoner skapats. Dubbelklicka bara på ikonen för att starta produkten.

### <a name="add-backup-storage"></a>Lägga till lagring av säkerhetskopior

Den första säkerhetskopian sparas på lagring som är ansluten till Azure Backup Server-datorn. Mer information om hur du lägger till diskar finns i [Konfigurera lagringspooler och disklagring](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Du måste lägga till lagring för säkerhetskopiering även om du planerar att skicka data till Azure. I den aktuella arkitekturen för Azure Backup Server innehåller Azure Backup-valvet den *andra* kopian av data medan den lokala lagringen innehåller den första (och obligatoriska) säkerhetskopian.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installera och uppdatera dataskyddshanterarens skyddsagent

MABS använder systemcenterdataskyddshanterarens skyddsagent. [Här är stegen](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) för att installera skyddsagenten på dina skyddsservrar.

I följande avsnitt beskrivs hur du uppdaterar skyddsagenter för klientdatorer.

1. Välj Hanteringsagenter i administratörskonsolen för säkerhetskopieringsserver **Management** > **Agents**.

2. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten för.

   > [!NOTE]
   > Kolumnen **Agentuppdateringar** anger när en uppdatering av skyddsagenten är tillgänglig för varje skyddad dator. I **åtgärdsfönstret** är åtgärden **Uppdatera** endast tillgänglig när en skyddad dator är markerad och uppdateringar är tillgängliga.
   >
   >

3. Om du vill installera uppdaterade skyddsagenter på de valda datorerna väljer du **Uppdatera**i fönstret **Åtgärder** .

4. För en klientdator som inte är ansluten till nätverket, tills datorn är ansluten till nätverket, visar kolumnen **Agentstatus** status **för Väntande uppdatering**.

   När en klientdator är ansluten till nätverket visar kolumnen **Agentuppdateringar** för klientdatorn **statusen Uppdatera**.

## <a name="move-mabs-to-a-new-server"></a>Flytta MABS till en ny server

Här är stegen om du behöver flytta MABS till en ny server, samtidigt som du behåller lagringen. Detta kan bara göras om alla data finns på modern säkerhetskopieringslagring.

  > [!IMPORTANT]
  >
  > * Det nya servernamnet måste vara samma namn som den ursprungliga Azure Backup Server-instansen. Du kan inte ändra namnet på den nya Azure Backup Server-instansen om du vill använda den tidigare lagringspoolen och MABS-databasen (DPMDB) för att behålla återställningspunkter.
  > * Du måste ha en säkerhetskopia av MABS-databasen (DPMDB). Du måste återställa databasen.

1. I visningsfönstret väljer du de klientdatorer som du vill uppdatera skyddsagenten för.
2. Stäng av den ursprungliga Azure-säkerhetskopieringsservern eller ta bort den från kabeln.
3. Återställ datorkontot i Active Directory.
4. Installera Server 2016 på ny dator och ge den samma datornamn som den ursprungliga Azure Backup-servern.
5. Gå med i domänen
6. Installera Azure Backup server V3 eller senare (flytta MABS Storage pool diskar från gamla servern och import)
7. Återställ DPMDB som tas i steg 1.
8. Koppla lagringen från den ursprungliga säkerhetskopieringsservern till den nya servern.
9. Från SQL Restore DPMDB
10. Från admin kommandorad på ny server cd till Microsoft Azure Backup installera plats och bin mapp

    Exempel på sökväg: C:\windows\system32>cd "c:\Program\Microsoft Azure Backup\DPM\DPM\bin\"

11. Kör DPMSYNC -SYNC för Azure-säkerhetskopiering

    Om du har lagt till NYA diskar i DPM Storage-poolen i stället för att flytta de gamla, kör du DPMSYNC -Omfördelareplica

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup Server kräver anslutning till Azure Backup-tjänsten för att produkten ska fungera. Om du vill verifiera om datorn har ```Get-DPMCloudConnection``` anslutningen till Azure använder du cmdleten i Azure Backup Server PowerShell-konsolen. Om cmdletens utgång är SANN finns det någon anslutning, annars finns det ingen anslutning.

Samtidigt måste Azure-prenumerationen vara i felfritt tillstånd. Om du vill ta reda på tillståndet för din prenumeration och hantera den loggar du in på [prenumerationsportalen](https://account.windowsazure.com/Subscriptions).

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

Om du har en brandvägg eller en proxy som förhindrar åtkomst till Azure måste du tillåta följande domänadresser i brandväggs-/proxyprofilen:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Om du använder ExpressRoute Microsoft-peering väljer du följande tjänster/regioner:

* Azure Active Directory (12076:5060)
* Microsoft Azure-region (enligt platsen för ditt Recovery Services-valv)
* Azure Storage (enligt platsen för ditt Recovery Services-valv)

Mer information finns i [ExpressRoute-routningskrav](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

När anslutningen till Azure har återställts till Azure Backup Server-datorn bestäms de åtgärder som kan utföras av Azure-prenumerationstillståndet. Tabellen ovan innehåller information om de åtgärder som tillåts när maskinen är "Ansluten".

### <a name="handling-subscription-states"></a>Hantera prenumerationstillstånd

Det är möjligt att ta en Azure-prenumeration från ett *utgånget* eller *avetablerat* tillstånd till tillståndet *Aktiv.* Detta har dock vissa konsekvenser för produktens beteende medan tillståndet inte är *aktivt:*

* En *avetablerade* prenumeration förlorar funktionalitet för den period som den avetableras. Vid svarvning *av Active*återupplivas produktfunktionerna för säkerhetskopiering/återställning. Säkerhetskopieringsdata på den lokala disken kan också hämtas om de har hållits med en tillräckligt stor kvarhållningsperiod. Säkerhetskopieringsdata i Azure förloras dock oåterkalleligen när prenumerationen går in i tillståndet *Avetablerade.*
* En *utgången* prenumeration förlorar bara funktioner tills den har gjorts *aktiv* igen. Alla säkerhetskopior som schemalagts för den period som prenumerationen *har upphört att gälla* kommer inte att köras.

## <a name="upgrade-mabs"></a>Uppgradera MABS

Använd följande procedurer för att uppgradera MABS.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Uppgradera från MABS V2 till V3

> [!NOTE]
>
> MABS V2 är inte en förutsättning för installation av MABS V3. Du kan dock bara uppgradera till MABS V3 från MABS V2.

Gör så här för att uppgradera MABS:

1. Om du vill uppgradera från MABS V2 till MABS V3 uppgraderar du operativsystemet till Windows Server 2016 eller Windows Server 2019 om det behövs.

2. Uppgradera servern. Stegen liknar [installationen](#install-and-upgrade-azure-backup-server). För SQL-inställningar får du dock ett alternativ för att uppgradera SQL-instansen till SQL 2017 eller använda din egen instans av SQL Server 2017.

   > [!NOTE]
   >
   > Avsluta inte medan SQL-instansen uppgraderas, avsluta kommer att avinstallera SQL-rapporteringsinstansen och därmed ett försök att uppgradera MABS kommer att misslyckas.

   > [!IMPORTANT]
   >
   >  Som en del av SQL 2017-uppgraderingen säkerhetskopierar vi SQL-krypteringsnycklarna och avinstallerar rapporteringstjänsterna. Efter uppgradering av SQL-servern installeras rapporteringstjänsten(14.0.6827.4788) & krypteringsnycklar återställs.
   >
   > När du konfigurerar SQL 2017 manuellt läser du *SSRS-konfigurationen med avsnittet SQL 2017* under Installationsinstruktioner.

3. Uppdatera skyddsagenterna på de skyddade servrarna.
4. Säkerhetskopior bör fortsätta utan att behöva starta om dina produktionsservrar.
5. Du kan börja skydda dina data nu. Om du uppgraderar till Modern Backup Storage, samtidigt som du skyddar, kan du också välja vilka volymer du vill lagra säkerhetskopiorna i och söka efter under etablerat utrymme. [Läs mer](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Felsökning

Om Microsoft Azure Backup-servern misslyckas med fel under installationsfasen (eller säkerhetskopiering eller återställning) läser du det här [felkodsdokumentet](https://support.microsoft.com/kb/3041338) för mer information.
Du kan också referera till [Azure Backup relaterade vanliga frågor](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

Du kan få detaljerad information här om [hur du förbereder din miljö för DPM](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019). Den innehåller också information om konfigurationer som stöds där Azure Backup Server kan distribueras och användas. Du kan använda en serie [PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) för att utföra olika åtgärder.

Du kan använda dessa artiklar för att få en djupare förståelse för arbetsbelastningsskydd med hjälp av Microsoft Azure Backup server.

* [Säkerhetskopiering av SQL Server](backup-azure-backup-sql.md)
* [Säkerhetskopiering av SharePoint-server](backup-azure-backup-sharepoint.md)
* [Alternativ säkerhetskopiering av servern](backup-azure-alternate-dpm-server.md)
