---
title: Konfigurera Microsoft Azure Backup Server för Azure VMware-lösning (AVS)
description: Konfigurera din Azure VMware-lösning (AVS) för att säkerhetskopiera virtuella datorer som använder Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 23c29f453587ac7a232c21e43fa6fb45193881bc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613112"
---
# <a name="set-up-microsoft-azure-backup-server-for-avs"></a>Konfigurera Microsoft Azure Backup Server för AVS

Microsoft Azure Backup Server är ett robust säkerhets kopierings-och återställnings system för företag som bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR). Under AVS-förhands granskningen kan du bara konfigurera säkerhets kopiering på virtuell dator med hjälp av Azure Backup Server. 

Azure Backup Server kan lagra säkerhetskopierade data till:

- **Disk**: Azure Backup Server säkerhetskopierar data till lagringspooler för kortsiktig lagring.

- **Azure**: för både kortsiktiga och långsiktiga lagrings platser kan Azure Backup Server data som lagras i diskkonfigurationer säkerhets kopie ras till Microsoft Azure molnet med hjälp av Azure Backup tjänsten.

När avbrott uppstår och källdata inte är tillgängliga kan du enkelt använda Azure Backup Server för att återställa data till källan eller en alternativ plats. På så sätt kan du enkelt återställa data till en annan plats om de ursprungliga data inte är tillgängliga på grund av planerade eller oväntade problem.

I den här artikeln hjälper vi dig att förbereda din AVS-miljö för säkerhets kopiering av virtuella datorer (VM) med Azure Backup Server.  Vi vägleder dig genom stegen för att: 

> [!div class="checklist"]
> * Bestäm vilken typ av disk och storlek för virtuell dator som ska användas
> * Skapa ett Recovery Services-valv som lagrar återställnings punkterna
> * Ange Storage-replikering för ett Recovery Services-valv
> * Lägga till lagringsutrymme på Azure Backup Server

## <a name="supported-vmware-features"></a>VMware-funktioner som stöds

- **Återställning utan agent:** Azure Backup Server kräver inte att en agent installeras på vCenter-eller ESXi-servern för att säkerhetskopiera den virtuella datorn. Ange i stället IP-adressen eller det fullständigt kvalificerade domän namnet (FQDN) och inloggnings uppgifter som används för att autentisera VMware-servern med Azure Backup Server.

- **Cloud-integrerad säkerhets kopiering:** Azure Backup Server skyddar arbets belastningar till disk och moln. Arbets flödet för säkerhets kopiering och återställning av Azure Backup Server hjälper dig att hantera långsiktig kvarhållning och säkerhets kopiering på annan plats.

- **Identifiera och skydda virtuella datorer som hanteras av vCenter:** Azure Backup Server identifierar och skyddar virtuella datorer som distribueras på en vCenter-eller ESXi-Server. Azure Backup Server identifierar även virtuella datorer som hanteras av vCenter, så att du kan skydda stora distributioner.

- **Automatiskt skydd på mappnivå:** vCenter gör det möjligt att organisera dina virtuella datorer i VM-mappar. Azure Backup Server identifierar dessa mappar och gör att du kan skydda virtuella datorer på mappnivå och inkluderar alla undermappar. När du skyddar mappar, Azure Backup Server inte bara skyddar de virtuella datorerna i mappen, utan också skyddar virtuella datorer senare. Azure Backup Server identifierar nya virtuella datorer dagligen och skyddar dem automatiskt. När du ordnar de virtuella datorerna i rekursiva mappar, Azure Backup Server automatiskt identifiera och skydda de nya virtuella datorerna som distribueras i de rekursiva mapparna.

- **Azure Backup Server fortsätter att skydda virtuella vMotioned-datorer i klustret:** Eftersom virtuella datorer är vMotioned för belastnings utjämning i klustret, identifierar Azure Backup Server automatiskt och fortsätter med VM-skyddet.

- **Återställ nödvändiga filer snabbare:** Azure Backup Server kan återställa filer/mappar från en virtuell Windows-dator utan att återställa hela den virtuella datorn.

## <a name="limitations"></a>Begränsningar

- Samlad uppdatering 1 för Azure Backup Server v3 måste vara installerad.

- Du kan inte säkerhetskopiera ögonblicks bilder av användaren före den första Azure Backup Server säkerhets kopieringen. När Azure Backup Server har slutfört den första säkerhets kopieringen kan du säkerhetskopiera användar ögonblicks bilder.

- Azure Backup Server kan inte skydda virtuella VMware-datorer med direkt lagrings diskar och fysiska mappningar av RAW-enheter (pRDM).

- Azure Backup Server kan inte identifiera eller skydda VMware-vApps.

**Om du vill konfigurera Microsoft Azure Backup Server för Azure VMware-lösning (AVS) måste du utföra följande steg:**

- Konfigurera förutsättningarna och miljön

- Skapa Azure Recovery Services-valv

- Hämta och installera Azure Backup Server 

- Lägga till lagringsutrymme på Azure Backup Server 

**Distributions arkitektur**  
Microsoft Azure Backup Server distribueras som virtuell Azure IaaS-dator för att skydda virtuella AVS-datorer.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Arkitektur för AVS-distribution" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Krav för Azure Backup Servers miljön

Överväg rekommendationerna i det här avsnittet när du installerar Azure Backup Server i din Azure-miljö.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Se till att du [konfigurerar nätverk för ditt privata VMware-moln i Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Bestäm storleken på den virtuella datorn

Du måste skapa en virtuell Windows-dator i det virtuella nätverk som du skapade i steget ovan. När du väljer en server för att köra Azure Backup Server rekommenderar vi att du börjar med en galleri bild av Windows Server 2019 Data Center. Guiden [skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/windows/quick-create-portal.md) själv studie kursen hjälper dig att komma igång med den rekommenderade virtuella datorn i Azure, även om du aldrig har använt Azure.

I tabellen nedan sammanfattas det högsta antalet skyddade arbets belastningar för varje Azure Backup Server virtuell dator storlek. Informationen baseras på interna prestanda och skalningstester med kanoniska värden för arbetsbelastningsstorlek och omsättning. Den faktiska arbets belastnings storleken kan vara större men bör hanteras av diskarna som är anslutna till den Azure Backup Server virtuella datorn.

| Max antal skyddade arbetsbelastningar | Genomsnittlig arbetsbelastningsstorlek | Genomsnittlig omsättning av arbetsbelastning (dagligen) | Minsta lagrings-IOPS | Rekommenderad disktyp/storlek      | Rekommenderad storlek på virtuell dator |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Netto 5 % omsättning                   | 2000             | Standard HDD (8 TB eller över storlek per disk)  | A4V2       |
| 40                      | 150 GB                | Netto 10 % omsättning                  | 4500             | Premium SSD * (1 TB eller över storlek per disk) | DS3_V2     |
| 60                      | 200 GB                | Netto 10 % omsättning                  | 10500            | Premium SSD * (8 TB eller över storlek per disk) | DS3_V2     |

* Om du vill hämta nödvändiga IOPs använder du minst disk utrymme som rekommenderas eller högre. Disk med mindre storlek ger lägre IOPs.

> [!NOTE]
> Azure Backup Server är utformad för att köras på en dedikerad server med ett enda syfte. Det går inte att installera Azure Backup Server på en dator:
> * Köra som en domänkontrollant
> * Har program Server rollen installerad
> * Det är en System Center Operations Manager hanterings Server
> * Köra Exchange Server
> * Det är en nod i ett kluster

### <a name="disks-and-storage"></a>Diskar och lagring

Azure Backup Server kräver diskar för installation, inklusive systemfiler, installationsfiler, program vara som krävs, databasfiler och dedikerade diskar för lagringspoolen.

| Krav                      | Rekommenderad storlek  |
|----------------------------------|-------------------------|
| Azure Backup Server installation                | Installations plats: 3 GB<br />Databasfilenhet: 900 MB<br />Systemen het: 1 GB för SQL-installation<br /><br />Dessutom behöver du utrymme för Azure Backup Server för att kopiera fil katalogen till en tillfällig installations plats vid arkivering.      |
| Disk för lagringspoolen<br />(Använder enkla volymer, får inte finnas på en dynamisk disk.) | 2 till 3 gånger storleken på skyddade data<br />Detaljerad lagrings beräkning finns i [DPM-Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

[Koppla en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md) artiklarna visar hur du kopplar en ny hanterad datadisk till en befintlig virtuell Azure-dator.

> [!NOTE]
> En enda Azure Backup Server har en mjuk gräns på 120 TB för lagringspoolen.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Lagra säkerhets kopierings data på lokal disk och i Azure

Lagring av säkerhetskopierade data i Azure minskar säkerhets kopierings infrastrukturen på Azure Backup Server VM. Vid drifts återställning (säkerhets kopiering) lagrar Azure Backup Server säkerhets kopierings data på Azure-diskar som är anslutna till den virtuella datorn. När diskarna och lagrings utrymmet är anslutna till den virtuella datorn kan Azure Backup Server hantera lagringen åt dig. Mängden säkerhets kopierings data lagring beror på antalet och storleken på diskar som är anslutna till varje virtuell Azure-dator, och varje storlek på den virtuella Azure-datorn har ett maximalt antal diskar som kan anslutas. Till exempel är a2 fyra diskar. A3 är åtta diskar. A4 är 16 diskar. På nytt bestämmer storlek och antal diskar den totala kapaciteten för säkerhets kopiering av lagringspool.

> [!IMPORTANT]
> Du bör **inte** behålla drift återställnings data på Azure Backup Server anslutna diskar i mer än fem dagar. Om data är fler än fem dagar gamla lagrar du dem i Azure Recovery Services Vault.

Skapa eller Använd ett Recovery Services valv för att lagra säkerhetskopierade data i Azure. När du förbereder att säkerhetskopiera Azure Backup Server arbets belastningen [konfigurerar du Recovery Services-valvet](#create-a-recovery-services-vault). När en säkerhets kopiering online körs när ett jobb har kon figurer ATS skapas en återställnings punkt i valvet. Varje Recovery Services valv innehåller upp till 9999 återställnings punkter. Beroende på hur många återställnings punkter som har skapats och hur länge de behålls, kan du behålla säkerhetskopierade data i många år. Du kan till exempel skapa månads Visa återställnings punkter och behålla dem i fem år.

> [!IMPORTANT]
> Oavsett om du skickar säkerhets kopierings data till Azure eller om du behåller dem lokalt måste du registrera Azure Backup Server med ett Recovery Services-valv.

### <a name="scale-deployment"></a>Skala distribution

Om du vill skala distributionen har du följande alternativ:

- **Skala upp** – öka storleken på den virtuella datorn Azure Backup Server från en serie till DS3-serien och öka den lokala lagringen.

- **Avlasta data** – skicka äldre data till Azure och Behåll bara de senaste data på lagrings platsen som är kopplad till Azure Backup Server.

- **Skala ut** – Lägg till fler Azure Backup-servrar för att skydda arbets belastningarna.

### <a name="net-framework"></a>.NET Framework

Den virtuella datorn måste ha .NET Framework 3,5 SP1 eller senare installerat.

### <a name="join-a-domain"></a>Ansluta till en domän

Den Azure Backup Server virtuella datorn måste vara ansluten till en domän och en domän användare med administratörs behörighet på den virtuella datorn måste installera Azure Backup Server.

Även om detta inte stöds vid tidpunkten för för hands versionen kan Azure Backup Server som distribueras i en virtuell Azure-dator säkerhetskopiera arbets belastningar på de virtuella datorerna i AVS, men de bör vara i samma domän för att aktivera säkerhets kopiering.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en lagrings enhet som lagrar återställnings punkterna som skapats med tiden. Den innehåller också säkerhets kopierings principer som är associerade med skyddade objekt.

1. Logga in på din prenumeration i [Azure-portalen](https://portal.azure.com/).

1. På menyn till vänster väljer du **Alla tjänster**.

   ![Välj Alla tjänster](../backup/media/backup-create-rs-vault/click-all-services.png)

1. I dialog rutan **alla tjänster** anger *Recovery Services* och väljer **Recovery Services valv** i listan.

   ![Ange och välja Recovery Services-valv](../backup/media/backup-create-rs-vault/all-services.png)

   Listan över Recovery Services-valv i prenumerationen visas.

1. På instrument panelen för **Recovery Services valv** väljer du **Lägg till**.

   ![Lägg till ett Recovery Services-valv](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Dialog rutan **Recovery Services valv** öppnas.

1. Ange värden för **namn**, **prenumeration**, **resurs grupp**och **plats**.

   ![Konfigurera Recovery Services-valvet](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Namn**: Ange ett eget namn som identifierar valvet. Namnet måste vara unikt för Azure-prenumerationen. Ange ett namn som innehåller minst två men högst 50 tecken. Namnet måste börja med en bokstav och får bara bestå av bokstäver, siffror och bindestreck.

   - **Prenumeration**: Välj den prenumeration som ska användas. Om du är medlem i endast en prenumeration ser du det namnet. Om du inte är säker på vilken prenumeration du ska använda använder du standard prenumerationen (rekommenderas). Det finns flera alternativ bara om ditt arbets-eller skol konto är associerat med fler än en Azure-prenumeration.

   - **Resurs grupp**: Använd en befintlig resurs grupp eller skapa en ny. Om du vill se en lista över tillgängliga resurs grupper i din prenumeration väljer du **Använd befintlig**och väljer sedan en resurs i list rutan. Om du vill skapa en ny resurs grupp väljer du **Skapa ny** och anger namnet.

   - **Plats**: Välj det geografiska området för valvet. För att skapa ett valv för att skydda virtuella datorer i AVS-form *måste* valvet vara i samma region som det privata molnet i AVS.

1. När du är redo att skapa Recovery Services-valvet väljer du **skapa**.

   ![Skapa Recovery Services-valvet](../backup/media/backup-create-rs-vault/click-create-button.png)

   Det kan ta en stund att skapa Recovery Services-valvet. Övervaka status meddelanden i **meddelande** fältet i det övre högra hörnet i portalen. När valvet har skapats visas det i listan över Recovery Services-valv. Om du inte ser ditt valv väljer du **Uppdatera**.

   ![Uppdatera listan över säkerhets kopierings valv](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Konfigurera lagringsreplikering

Med alternativet lagrings replikering kan du välja mellan Geo-redundant lagring (standard) och lokalt redundant lagring. Geo-redundant lagring kopierar data i ditt lagrings konto till en sekundär region, vilket gör dina data varaktiga. Lokalt redundant lagring är ett billigare alternativ som inte är lika beständigt. Läs mer om alternativen för Geo-redundant och lokalt redundant lagring i [Azure Storage redundans](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Att ändra **typ av lagrings replikering** (lokalt redundant/Geo-redundant) för ett Recovery Services-valv måste göras innan du konfigurerar säkerhets kopieringar i valvet. När du har konfigurerat säkerhets kopiering inaktive ras alternativet för att ändra det och du kan inte ändra **typen av lagrings replik**.

1. Klicka på det nya valvet från **Recovery Services-valv**. 

1. Under **Inställningar**väljer du **Egenskaper**och under **säkerhets kopierings konfiguration**klickar du på **Uppdatera**.

1. Välj typ av lagrings replikering och klicka på **Spara**.

   ![Ange lagringskonfigurationen för det nya valvet](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-software-package"></a>Hämta och installera program varu paket

### <a name="downloading-the-software-package"></a>Laddar ned programpaketet

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Om du redan har ett Recovery Services-valv öppet kan du fortsätta till nästa steg. Om du inte har ett Recovery Services-valv öppet, men är i Azure Portal klickar du på **Bläddra**i huvud menyn.

   1. I listan över resurser skriver du **Recovery Services**.

   1. När du börjar skriva filtreras listan baserat på det du skriver. När du ser **Recovery Services-valv** klickar du på det.

   ![Skapa Recovery Services-valv (steg 1)](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Välj ett valv i listan över Recovery Services-valv.

   Instrumentpanelen för det valda valvet öppnas.

   ![Öppna bladet för valvet](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   **Inställningarna** öppnas som standard. Om det stängs väljer du **Inställningar** för att öppna den.

   ![Öppna bladet för valvet](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Klicka på **säkerhets kopiering** för att öppna guiden komma igång.

   ![Komma igång med säkerhets kopiering](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Gör följande i fönstret som öppnas:

   1. Från menyn **där är din arbets belastning som körs** väljer du **lokalt**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Var körs din arbets belastning?":::

   1. På menyn **vad vill du säkerhetskopiera?** väljer du de arbets belastningar som du vill skydda med hjälp av Azure Backup Server.

   1. Klicka på **Förbered infrastruktur** för att ladda ned och installera Azure Backup Server och autentiseringsuppgifterna för valvet.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Förbered infrastruktur":::

1. I fönstret **Förbered infrastruktur** som öppnas gör du följande:

   1. Klicka på länken **Hämta** för att installera Azure Backup Server.

   1. Hämta autentiseringsuppgifterna för valvet genom att markera kryss rutan som **redan har laddats ned eller Använd den senaste Azure Backup Server installationen** och klicka sedan på **Ladda ned**. Du använder autentiseringsuppgifterna för valvet under registreringen av Azure Backup Server i Recovery Services-valvet. Länkarna tar dig till Download Center där du laddar ned program varu paketet.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Förbered infrastruktur – Azure Backup Server":::

1. På sidan Hämta väljer du alla filer och klickar på **Nästa**.

   > [!NOTE]
   > Du måste ladda ned alla filer till samma mapp.  Eftersom nedladdnings storleken för filerna är > 3 GB, kan det ta upp till 60 minuter innan nedladdningen är klar. 

   ![Hämta Center 1](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extracting-the-software-package"></a>Extrahera programpaketet

Om du har laddat ned program varu paketet till en annan server kopierar du filerna till den virtuella datorn som du skapade för att distribuera Azure Backup Server.

> [!WARNING]
> Det krävs minst 4 GB ledigt utrymme för att extrahera installationsfilerna.

1. När du har laddat ned alla filer, dubbelklickar du på **MicrosoftAzureBackupInstaller. exe** för att öppna **installations guiden för Microsoft Azure Backup** och klickar sedan på **Nästa**.

1. Välj platsen där filerna ska extraheras och klicka på **Nästa**.

1. Starta extraherings processen genom att klicka på **extrahera** .

   ![Microsoft Azure Backup installations guiden](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. När du har extraherat väljer du alternativet för att **köra Setup. exe** och klickar sedan på **Slutför**.

> [!TIP]
> Du kan också leta upp filen Setup. exe från den mapp där du extraherade program varu paketet.

### <a name="installing-the-software-package"></a>Installera programpaketet

1. I installations fönstret under installera klickar du på **Microsoft Azure Backup** för att öppna installations guiden.

   ![Microsoft Azure Backup installations guiden](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. På Välkomst skärmen klickar du på **Nästa** för att fortsätta till de nödvändiga kontrollerna.

1. Klicka på **kontrol lera** för att avgöra om maskin-och program varu kraven för Azure Backup Server är uppfyllda. Klicka på **Nästa**om det är uppfyllt.

   ![Azure Backup Server-Välkommen och krav kontroll](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Installations paketet för Azure Backup Server levereras med lämpliga SQL Server binärfiler som behövs. När du startar en ny Azure Backup Server-installation väljer du alternativet **installera ny instans av SQL Server med detta installations** alternativ och klickar på **kontrol lera och installera**.

   ![Azure Backup Server-SQL-kontroll](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Om du vill använda din egen SQL Server är de SQL Server-versioner som stöds SQL Server 2014 SP1 eller högre, 2016 och 2017. Alla SQL Server-versioner ska vara standard eller Enterprise 64-bitars. Azure Backup Server fungerar inte med en fjärran sluten SQL Server instans. Den instans som används av Azure Backup Server måste vara lokal. Om du använder en befintlig SQL Server för Azure Backup Server stöder installations programmet endast användningen av *namngivna instanser* av SQL Server.

   Om ett fel inträffar med en rekommendation om att starta om datorn gör du det och klickar på **kontrol lera igen**. Om det finns några problem med SQL-konfigurationen konfigurerar du om SQL enligt rikt linjerna i SQL och försöker installera/uppgradera Azure Backup Server med hjälp av den befintliga SQL-instansen.

   **Manuell konfiguration**

   När du använder en egen SQL-instans kontrollerar du att du lägger till builtin\Administrators i sysadmin-rollen till Master-databasen.

   **SSRS-konfiguration med SQL 2017**

   När du använder din egen instans av SQL 2017 måste du konfigurera SSRS manuellt. Efter SSRS-konfigurationen kontrollerar du att egenskapen *IsInitialized* för SSRS har angetts till *True*. Om detta är inställt på Sant förutsätter MABS att SSRS redan har kon figurer ATS och kommer att hoppa över SSRS-konfigurationen.

   Kontrol lera status för SSRS-konfigurationen genom att köra följande kommando:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Använd följande värden för SSRS-konfiguration:
   * Tjänst konto: Använd det inbyggda kontot ska vara nätverks tjänst
   * Webb tjänstens URL: virtuell katalog ska vara ReportServer_\<SQLInstanceName>
   * Databas: DatabaseName ska vara ReportServer $\<SQLInstanceName>
   * Webb portalens URL: ' virtuell katalog ' ska vara Reports_\<SQLInstanceName>

   [Läs mer](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) om SSRS-konfiguration.

   > [!NOTE]
   > [Microsoft Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products) (ost) styr licensieringen för SQL Server som används som databas för Azure Backup Server. Enligt OST kan SQL Server paketerat med Azure Backup Server bara användas som databas för Azure Backup Server.

1. När installationen är klar klickar du på **Nästa**.

1. Ange en plats för installationen av Microsoft Azure Backup Server-filerna och klicka på **Nästa**.

   > [!NOTE]
   > Det krävs en arbets plats för att säkerhetskopiera till Azure. Se till att arbets platsen är minst 5% av de data som planeras att säkerhets kopie ras till molnet. För disk skydd måste separata diskar konfigureras när installationen är klar. Mer information om lagringspooler finns i Konfigurera lagringspooler [och disk lagring](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Ange ett starkt lösen ord för begränsade lokala användar konton och klicka på **Nästa**.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Välj om du vill använda Microsoft Update för att söka efter uppdateringar och klicka på **Nästa**.

   > [!NOTE]
   > Vi rekommenderar att du Windows Update omdirigera till Microsoft Update, som erbjuder säkerhet och viktiga uppdateringar för Windows och andra produkter som Microsoft Azure Backup Server.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Granska *sammanfattningen av inställningarna* och klicka på **Installera**.

   Installationen sker i faser. Den första fasen installerar Microsoft Azure Recovery Services agenten och den andra fasen kontrollerar Internet anslutningen. Om Internet anslutningen är tillgänglig kan du fortsätta med installationen. om inte, måste du ange information om proxyn för att ansluta till Internet. Den sista fasen kontrollerar den nödvändiga program varan och om den inte har installerats installeras alla program som saknas, tillsammans med Microsoft Azure Recovery Services agenten.

1. Klicka på **Bläddra** för att hitta dina autentiseringsuppgifter för valvet för att registrera datorn på Recovery Services-valvet och klicka sedan på **Nästa**.

1. Välj en lösen fras för att kryptera/dekryptera data som skickas mellan Azure och dina lokaler.

   > [!TIP]
   > Du kan generera en lösen fras automatiskt eller ange en egen lösen fras för lösen ord på 16.

1. Ange platsen där du vill spara lösen frasen och klicka sedan på **Nästa** för att registrera servern.

   > [!IMPORTANT]
   > Det är viktigt att även spara lösen frasen på en annan säker plats än den lokala servern. Microsoft rekommenderar starkt att du använder en Azure Key Vault för att lagra lösen frasen.

   När installationen av Microsoft Azure Recovery Services-agenten är klar flyttas den till installationen och konfigurationen av SQL Server och Azure Backup Server-komponenterna.

   ![Azure Backup Server](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. När installations steget har slutförts klickar du på **Stäng**.

### <a name="install-update-rollup-1"></a>Installera Samlad uppdatering 1

Installationen av Samlad uppdatering 1 för Microsoft Azure Backup Server v3 är obligatorisk innan arbets belastningarna skyddas. Om du vill visa en lista över fel korrigeringar och Installationsinstruktioner för Microsoft Azure Backup Server v3 UR1, se KB-artikel [4534062](https://support.microsoft.com/en-us/help/4534062/).

## <a name="add-storage-to-azure-backup-server"></a>Lägga till lagringsutrymme på Azure Backup Server

Azure Backup Server v3 stöder Modern Backup Storage som erbjuder:

-  Lagrings besparingar på 50%

-  Säkerhets kopieringar som är tre gånger snabbare

-  Effektivare lagring

-  Arbets belastnings medveten lagring

### <a name="volumes-in-backup-server"></a>Volymer i säkerhets kopierings Server

Lägg till data diskarna med den lagrings kapacitet som krävs för den virtuella datorn Azure Backup servern om den inte redan har lagts till.

Backup server v3 accepterar bara lagrings volymer. När du lägger till en volym formaterar backup-servern volymen till elastiskt fil system (ReFS) som Modern Backup Storage kräver.

### <a name="add-volumes-to-backup-server-disk-storage"></a>Lägg till volymer på säkerhets kopierings serverns disk lagring

1. I **hanterings** fönstret, Genomsök lagringen på nytt och välj sedan **Lägg till**. 

1. Välj bland de tillgängliga volymer som ska läggas till i lagringspoolen. 

1. När du har lagt till de tillgängliga volymerna ger du dem ett eget namn som hjälper dig att hantera dem. 

1. Klicka på **OK** för att formatera volymerna till ReFS så att backup server kan använda fördelarna med modern backup Storage.

![Lägg till tillgängliga volymer](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Efterföljande moment

Fortsätt till nästa självstudie och lär dig hur du konfigurerar säkerhets kopiering av virtuella VMware-datorer som körs på Azure VMware-lösning (AVS) med Azure Backup Server.

> [!div class="nextstepaction"]
> [Konfigurera säkerhets kopiering av virtuella AVS-datorer](backup-avs-vms-with-mabs.md)

