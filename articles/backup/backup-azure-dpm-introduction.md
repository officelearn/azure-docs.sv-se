---
title: Använda DPM för att säkerhetskopiera arbetsbelastningar till Azure
description: En introduktion till säkerhetskopiering av DPM-data till ett Azure Recovery Services-valv.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, data protection manager, dpm-säkerhetskopiering
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 873e7066bcf51b32c3a7a54e845ffd5a744f407f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745443"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Förbereder för att säkerhetskopiera arbetsbelastningar till Azure med DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Den här artikeln förklarar hur du säkerhetskopierar data för System Center Data Protection Manager (DPM) till Azure; inklusive:

* Hur du säkerhetskopierar DPM server-data till Azure
* Kraven för att få en smidig upplevelse för säkerhetskopiering
* Vanliga fel som inträffar och hur du hanterar dem.
* Scenarier som stöds

> [!NOTE]
> Azure har två distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln innehåller information och procedurer för att återställa virtuella datorer som distribueras med hjälp av Resource Manager-modellen.
>
>

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) säkerhetskopierar filer och programdata. Mer information om arbetsbelastningar som stöds finns [här](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Data som säkerhetskopieras till DPM kan lagras på band, på disk, eller säkerhetskopieras till Azure med Microsoft Azure Backup. DPM interagerar med Azure Backup på följande sätt:

* **DPM distribueras som en fysisk server eller en lokal virtuell dator** – DPM distribueras som en fysisk server eller en lokal Hyper-V-dator säkerhetskopierar data till ett Recovery Services-valv utöver disk och band säkerhetskopiering.
* **DPM distribueras som virtuella Azure-datorer** – från System Center 2012 R2 med uppdatering 3 på, kan du distribuera DPM på en Azure-dator. Om DPM distribueras som virtuella Azure-datorer kan du säkerhetskopiera data till Azure diskar som är anslutna till den virtuella datorn eller avlasta datalagringen med säkerhetskopiering till ett Recovery Services-valv.

## <a name="why-back-up-dpm-to-azure"></a>Varför säkerhetskopiera DPM till Azure?
Företagets fördelar med att säkerhetskopiera DPM-servrar till Azure är:

* Använd Azure som ett alternativ till långsiktig distribution till band för den lokala DPM-distribution.
* Omfördela lagring från Azure-disken för att distribuera DPM på en virtuell dator i Azure. Lagra äldre data i Recovery Services-valvet kan du skala upp din verksamhet genom att lagra nya data till disk.

## <a name="prerequisites"></a>Förutsättningar
Förbereda Azure Backup för att säkerhetskopiera DPM-data på följande sätt:

1. **Skapa ett Recovery Services-valv** – skapa ett valv i Azure-portalen.
2. **Ladda ned valvautentiseringsuppgifter** – hämta de autentiseringsuppgifter du använder för att registrera DPM-servern med Recovery Services-valvet.
3. **Installera Azure Backup-agenten** – installera agenten på varje DPM-server.
4. **Registrera servern** – registrera DPM-servern med Recovery Services-valvet.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Viktiga definitioner
Här följer några viktiga definitioner för säkerhetskopiering till Azure för DPM:

1. **Valvautentisering** – Valvautentiseringsuppgifter behövs för att autentisera datorn för att skicka säkerhetskopierade data till ett identifierat valv i Azure Backup-tjänsten. Den kan hämtas från valvet och är giltiga i 48 timmar.
2. **Lösenfras** – lösenfras som används för att kryptera säkerhetskopiorna till molnet. Spara filen på en säker plats när den behövs under en återställningsåtgärd.
3. **Säkerhetskod** – om du har aktiverat den [säkerhetsinställningar](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) av valvet SÄKERHETSKOD krävs för att utföra viktiga säkerhetskopieringsåtgärder. Den här Multi-Factor authentication lägger till ytterligare en säkerhetsnivå. 
4. **Återställningsmappen** – det är frasen som säkerhetskopior från molnet hämtas tillfälligt till under återställningar för molnet. Storleken ungefär vara lika med storleken på de säkerhetskopieringsobjekt som du vill återställa parallellt.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Redigera Lagringsreplikering

Storage-replikering kan du välja mellan geo-redundant lagring och lokalt redundant lagring. Valvet använder geo-redundant lagring som standard. Lämna alternativet inställt på geo-redundant lagring om valvet är din primära säkerhetskopia. Om du vill använda ett billigare alternativ som inte är så hållbar, kan du använda följande procedur för att konfigurera lokalt redundant lagring. Läs mer om alternativen för [geo-redundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagring i [Översikt över Azure Storage-replikering](../storage/common/storage-redundancy.md).

Så här redigerar du inställningen för lagringsreplikering:

> [!NOTE] 
> Konfigurera lagringsreplikering innan du aktiverar den första säkerhetskopieringen. Om du vill ändra konfigurationen för storage-replikering när du säkerhetskopierar det skyddade objektet måste du stoppa skydd på valvet innan du byter lagringskonfiguration.
>  

1. Välj ditt valv och öppna instrumentpanelen för valvet.

2. I den **hantera** klickar du på **infrastruktur för säkerhetskopiering**.

3. På den **Säkerhetskopieringskonfigurationen** menyn, Välj alternativet för lagringsreplikering för ditt valv.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    När du har valt lagringsalternativet för valvet är det dags att associera den virtuella datorn med valvet. För att börja kopplingen identifierar du och registrerar de virtuella Azure-datorerna.

## <a name="download-vault-credentials"></a>Ladda ned autentiseringsuppgifter för valvet
Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv. Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS). Under registreringsarbetsflödet datorn görs certifikatets privata nyckel tillgänglig för användaren som autentiserar datorn. Azure Backup-tjänsten skickar data till identifierat valv baserat på vilken autentisering.

Valvautentiseringen används endast under registreringsarbetsflödet. Det är användarens ansvar att se till att valvautentiseringsfilen inte komprometteras. Om kontroll av autentiseringsuppgifter tappas bort, användas autentiseringsuppgifterna för valvet för att registrera andra datorer till valvet. Dock krypteras säkerhetskopierade data med en lösenfras som hör till kunden, så att befintliga säkerhetskopierade data komprometteras. För att lösa det här problemet, valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Ladda ned nya valvautentiseringsuppgifter så många gånger som behövs. Dock kan endast senaste valvautentiseringsfilen användas under registreringsarbetsflödet.

Valvautentiseringsfilen hämtas via en säker kanal från Azure-portalen. Azure Backup-tjänsten är inte medveten om certifikatets privata nyckel och den privata nyckeln är inte tillgänglig i portalen eller tjänsten. Använd följande steg för att hämta valvautentiseringsfilen till en lokal dator.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Öppna Recovery Services-valvet som du vill registrera en DPM-server.

3. Inställningsmenyn som öppnas som standard. Om den är stängd klickar du på **inställningar** på instrumentpanelen för valvet för att öppna menyn. I den **inställningar** -menyn klickar du på **egenskaper**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. På sidan Egenskaper under **säkerhetskopiering autentiseringsuppgifter** klickar du på **hämta**. Portalen genererar valvautentiseringsfilen som gjorts tillgänglig för hämtning.

    ![Hämta](./media/backup-azure-dpm-introduction/vault-credentials.png)

Portalen genererar en valvautentiseringen med hjälp av en kombination av valvnamnet och det aktuella datumet. Klicka på **spara** att hämta autentiseringsuppgifterna för valvet till det lokala kontot mapp eller välja Spara som från menyn Spara för att ange en plats för autentiseringsuppgifterna för valvet. Det kan ta upp till en minut för filen som ska genereras.

### <a name="note"></a>Observera
* Se till att valvautentiseringsfilen sparas på en plats som kan nås från din dator. Om den är lagrad i en resurs/SMB i filen, söka efter åtkomstbehörigheterna.
* Valvautentiseringsfilen används endast under registreringsarbetsflödet.
* Valvautentiseringsfilen upphör att gälla efter 48hrs och kan laddas ned från portalen.

## <a name="install-backup-agent"></a>Installera säkerhetskopieringsagenten
När du har skapat Azure Backup-valvet, bör en agent installeras på var och en av dina Windows-datorer (Windows Server, Windows-klienten, System Center Data Protection Manager-server eller Azure Backup Server-dator) som gör att säkerhetskopiera data och program till Azure .

1. Öppna Recovery Services-valvet som du vill registrera DPM-datorn.
2. Inställningsmenyn som öppnas som standard. Om den är stängd klickar du på **inställningar** att öppna inställningsmenyn. I Inställningar-menyn klickar du på **egenskaper**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. På sidan Inställningar **hämta** under **Azure Backup-agenten**.

    ![Hämta](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   När agenten har hämtats, kör du MARSAgentInstaller.exe för att starta installationen av Azure Backup-agenten. Välj installationsmappen och temporära mappen som krävs för agenten. Cachelagringsplatsen måste ha ledigt utrymme som är minst 5% av säkerhetskopierade data.

4. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger proxy-serverinformation. Om du använder en autentiserad proxyserver måste du ange användarinformation för namn och lösenord i den här skärmen.

5. Azure Backup-agenten installeras .NET Framework 4.5 och Windows PowerShell (om det inte finns redan) för att slutföra installationen.

6. När agenten har installerats, **Stäng** fönstret.

   ![Stäng](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Att **registrera DPM-servern** till valvet, i den **Management** fliken, klicka på **Online**. Välj **registrera**. Registrera guiden öppnas.

8. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger proxy-serverinformation. Om du använder en autentiserad proxyserver måste du ange användarinformation för namn och lösenord i den här skärmen.

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. På skärmen vault autentiseringsuppgifter Bläddra till och välj filen med valvautentiseringsuppgifter som hämtades tidigare.

    ![Autentiseringsuppgifterna för valvet](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    Valvautentiseringsfilen är endast giltig för 48 timmar (när den har hämtats från portalen). Om det uppstår några fel i den här skärmen (till exempel ”valvautentiseringsuppgifter tillhandahållna filen har upphört att gälla”), logga in på Azure-portalen och ladda ned valvautentiseringsuppgifter filen igen.

    Se till att valvautentiseringsfilen finns på en plats som kan användas av installationsprogrammet. Om du får åtkomst till felen, kopiera filen med valvautentiseringsuppgifter till en tillfällig plats på den här datorn och försök igen.

    Om du stöter på ett ogiltigt vault credential-fel (till exempel ”ogiltiga valvautentiseringsuppgifter angivna”) i filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten. Försök igen när du laddar ned en ny valvautentiseringsfil från portalen. Det här felet visas ofta om användaren klickar på den **Download valvautentiseringen** alternativ i Azure-portalen i snabb följd. I det här fallet gäller endast andra valvautentiseringsfilen.

10. Styra användningen av nätverksbandbredden under arbete och icke-arbetstid, i den **begränsning** skärmen, som du kan ange bandbreddsbegränsningen och definiera arbets- och fritid.

    ![Begränsningsinställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. I den **Recovery inställningen** skärmen, bläddra för att tillfälligt mellanlagra av den mapp där filerna hämtade från Azure.

    ![Inställningen för återställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. I den **krypteringsinställning** skärmen, du kan generera en lösenfras eller ange en lösenfras (minst 16 tecken). Kom ihåg att spara lösenfrasen på en säker plats.

    ![Kryptering](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Om lösenfrasen går förlorat; Microsoft kan hjälpa dig att återställa säkerhetskopierade data. Användaren äger krypteringslösenfrasen och Microsoft har inte insyn i den lösenfras som används av slutanvändaren. Spara filen på en säker plats när den behövs under en återställningsåtgärd.
    >
    >

13. När du klickar på den **registrera** knapp, datorn har registrerats för valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure.

14. När du använder Data Protection Manager kan du ändra de inställningar som anges under registreringsarbetsflödet genom att klicka på den **konfigurera** alternativet genom att välja **Online** under den **Management**  Fliken.

## <a name="requirements-and-limitations"></a>Krav (och begränsningar)
* DPM kan köras som en fysisk server eller en Hyper-V virtuell dator som är installerade på System Center 2012 SP1 eller System Center 2012 R2. Det kan också köras som en Azure-dator som körs på System Center 2012 R2 med minst DPM 2012 R2 Samlad uppdatering 3 eller en Windows-dator i VMWare som körs på System Center 2012 R2 med minst Samlad uppdatering 5.
* Om du kör DPM med System Center 2012 SP1 bör du installera Samlad uppdatering 2 för System Center Data Protection Manager SP1. Detta krävs innan du kan installera Azure Backup-agenten.
* DPM-servern bör ha Windows PowerShell och .net Framework 4.5 installerat.
* DPM kan säkerhetskopiera de flesta arbetsbelastningar till Azure Backup. En fullständig lista över vad har som stöds finns i stöd Azure Backup för objekten nedan.
* Data som lagras i Azure Backup kan inte återställas med alternativet ”Kopiera till band”.
* Du behöver ett Azure-konto med Azure Backup-funktionen aktiverad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs mer om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Med Azure Backup kräver Azure Backup-agenten installeras på servrar som du vill säkerhetskopiera. Alla servrar måste ha minst 5% av storleken på de data som säkerhetskopieras, tillgängligt som lokal ledigt lagringsutrymme. Till exempel kräver säkerhetskopiera 100 GB data minst 5 GB ledigt utrymme i den tillfälliga platsen.
* Data lagras i Azure vault-lagring. Det finns ingen gräns för mängden data som du kan säkerhetskopiera till ett Azure Backup-valv men storleken på en datakälla (till exempel en virtuell dator eller en databas) får inte överskrida 54400 GB.

De här filtyperna har stöd för säkerhetskopiering till Azure:

* Krypterade (endast fullständiga säkerhetskopior)
* Komprimerade (inkrementella säkerhetskopior stöds)
* Sparse-filer (inkrementella säkerhetskopior stöds)
* Komprimerade och sparse-filer (hanteras som sparse-filer)

Och dessa inte stöds:

* Servrar i skiftlägeskänsliga filsystem stöds inte.
* Hårda länkar (ignoreras)
* Referenspunkt (ignoreras)
* Krypterade och komprimerade (ignoreras)
* Krypterade och utspridda (ignoreras)
* Komprimerad dataström
* Utspridd ström

> [!NOTE]
> Från System Center 2012 DPM med SP1 och senare, kan du säkerhetskopiera skyddade arbetsbelastningar till Azure.
>
>
