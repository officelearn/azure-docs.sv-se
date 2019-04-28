---
title: Förbereda DPM-servern att säkerhetskopiera arbetsbelastningar till Azure
description: En introduktion till säkerhetskopiering av DPM-data till ett Azure Recovery Services-valv.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: f119d128b35b93d7e18d514c09d187689d8dffe9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111278"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Förbereda säkerhetskopiering av arbetsbelastningar till Azure med System Center DPM

Den här artikeln beskriver hur du förbereder för System Center Data Protection Manager (DPM) säkerhetskopieringar till Azure med Azure Backup-tjänsten.

Artikeln innehåller:

- En översikt över distribution av DPM med Azure Backup.
- Förutsättningar och begränsningar för användning av Azure Backup med DPM.
- Steg för att förbereda Azure, inklusive hur du konfigurerar ett Recovery Services-Backup-valv och du kan också ändra typen för Azure storage för valvet.
- Steg för att förbereda DPM-servern, inklusive hämtar valvet autentiseringsuppgifter, installera Azure Backup-agenten och registrera DPM-servern i valvet.
- Felsökningstips för vanliga fel.


## <a name="why-back-up-dpm-to-azure"></a>Varför säkerhetskopiera DPM till Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) säkerhetskopierar filer och programdata. DPM interagerar med Azure Backup på följande sätt:

* **DPM som körs på en fysisk server eller en lokal virtuell dator** – du kan säkerhetskopiera data till ett säkerhetskopieringsvalv i Azure, förutom till disk och band säkerhetskopiering.
* **DPM som körs på en Azure VM** – från System Center 2012 R2 med uppdatering 3 eller senare, kan du distribuera DPM på en Azure VM. Du kan säkerhetskopiera data till Azure diskar som är anslutna till den virtuella datorn eller använda Azure Backup för att säkerhetskopiera data till ett säkerhetskopieringsvalv.

Företagets fördelar med att säkerhetskopiera DPM-servrar till Azure är:

* Azure Backup är ett alternativ till långsiktig distribution till band för den lokala DPM.
* För DPM som körs på en virtuell Azure-dator, kan Azure Backup du omfördela lagring från Azure-disken. Lagra äldre data i ett Backup-valv kan du skala upp din verksamhet genom att lagra nya data till disk.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

**Inställning** | **Krav**
--- | ---
DPM på en Azure virtuell dator | System Center 2012 R2 med DPM 2012 R2 Samlad uppdatering 3 eller senare.
DPM på en fysisk server | System Center 2012 SP1 eller senare. System Center 2012 R2.
DPM på en Hyper-V-dator | System Center 2012 SP1 eller senare. System Center 2012 R2.
DPM på en virtuell VMware-dator | System Center 2012 R2 med Samlad uppdatering 5 eller senare.
Komponenter | DPM-servern bör ha Windows PowerShell och .NET Framework 4.5 är installerat.
Appar som stöds | [Läs mer om](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) vad som kan säkerhetskopieras av DPM.
Filtyper som stöds | Dessa filtyper kan säkerhetskopieras med Azure Backup: Krypterade (endast fullständiga säkerhetskopior); Komprimerade (inkrementella säkerhetskopior stöds). Sparse-filer (inkrementella säkerhetskopior stöds); Komprimerade och sparse-filer (hanteras som sparse).
Filtyper som inte stöds | Servrar i skiftlägeskänsliga filsystem; hårda länkar (ignoreras); referenspunkt (ignoreras); krypterade och komprimerade (ignoreras); krypterade och utspridda (ignoreras); Komprimerad dataström; parsa dataströmmen.
Lokal lagring | Varje dator som du vill säkerhetskopiera måste ha lokala ledigt lagringsutrymme som är minst 5% av storleken på de data som säkerhetskopieras. Till exempel kräver säkerhetskopiera 100 GB data minst 5 GB ledigt utrymme i den tillfälliga platsen.
Valvet lagring | Det finns ingen gräns för mängden data som du kan säkerhetskopiera till ett Azure Backup-valv, men storleken på en datakälla (till exempel en virtuell dator eller en databas) får inte överskrida 54400 GB.
Azure Backup-agent | Om DPM körs på System Center 2012 SP1 kan du installera Samlad 2 eller senare för DPM SP1. Detta krävs för installation av agent.<br/><br/> Den här artikeln beskriver hur du distribuerar den senaste versionen av Azure Backup-agenten, även känt som Microsoft Azure Recovery Service MARS-agenten. Om du har en tidigare version som har distribuerats kan du uppdatera till den senaste versionen för att säkerställa att säkerhetskopieringen fungerar som förväntat.

Innan du börjar bör behöver du ett Azure-konto med Azure Backup-funktionen aktiverad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs mer om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Ändra inställningar

Du kan välja mellan geo-redundant lagring och lokalt redundant lagring.

- Valvet använder geo-redundant lagring som standard.
- Lämna alternativet inställt på geo-redundant lagring om valvet är din primära säkerhetskopia. Om du vill använda ett billigare alternativ som inte är så hållbar, kan du använda följande procedur för att konfigurera lokalt redundant lagring.
- Lär dig mer om [Azure storage](../storage/common/storage-redundancy.md), och [georedundant](../storage/common/storage-redundancy-grs.md) och [lokalt redundant](../storage/common/storage-redundancy-lrs.md) lagringsalternativ.
- Ändra lagringsinställningarna innan den första säkerhetskopieringen. Om du redan har säkerhetskopierat ett objekt kan du stoppa säkerhetskopieras i valvet innan du ändrar inställningarna.

Så här redigerar du inställningen för lagringsreplikering:

1. Öppna instrumentpanelen för valvet.

2. I **hantera**, klickar du på **infrastruktur för säkerhetskopiering**.

3. I **Säkerhetskopieringskonfigurationen** menyn och välj en alternativ för valvet.

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Ladda ned autentiseringsuppgifter för valvet

Du kan använda autentiseringsuppgifterna för valvet när du registrerar DPM-servern i valvet.

- Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv.
- Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS).
- Under registreringsarbetsflödet datorn görs certifikatets privata nyckel tillgänglig för användaren som autentiserar datorn.
- Azure Backup-tjänsten skickar data till identifierat valv baserat på vilken autentisering.

### <a name="best-practices-for-vault-credentials"></a>Metodtips för valvautentiseringsuppgifter

Om du vill hämta autentiseringsuppgifterna hämta du valvautentiseringsfilen via en säker kanal från Azure portal:

- Autentiseringsuppgifterna för valvet används endast under registreringsarbetsflödet.
- Det är ditt ansvar att se till att valvautentiseringsfilen är säkert och inte komprometterad.
    - Om kontroll av autentiseringsuppgifter tappas bort, användas autentiseringsuppgifterna för valvet för att registrera andra datorer till valvet.
    - Dock krypteras säkerhetskopierade data med en lösenfras som hör till kunden, så att befintliga säkerhetskopierade data komprometteras.
- Kontrollera att filen sparas på en plats som kan nås från DPM-servern. Om den är lagrad i en resurs/SMB i filen, söka efter åtkomstbehörigheterna.
- Valvautentiseringsuppgifterna upphör att gälla efter 48 timmar. Du kan ladda ned nya valvautentiseringsuppgifter så många gånger som behövs. Dock kan endast senaste valvautentiseringsfilen användas under registreringsarbetsflödet.
- Azure Backup-tjänsten är inte medveten om certifikatets privata nyckel och den privata nyckeln är inte tillgänglig i portalen eller tjänsten.

Hämta valvautentiseringsfilen till en lokal dator på följande sätt:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Öppna det valv som du vill registrera DPM-servern.
3. I **inställningar**, klickar du på **egenskaper**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. I **egenskaper** > **säkerhetskopiering autentiseringsuppgifter**, klickar du på **hämta**. Portalen genererar valvautentiseringsfilen med hjälp av en kombination av valvnamnet och aktuellt datum och gör den tillgänglig för hämtning.

    ![Ladda ned](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Klicka på **spara** att hämta autentiseringsuppgifterna för valvet till mappen eller **Spara som** och ange en plats. Det kan ta upp till en minut för filen som ska genereras.


## <a name="install-the-backup-agent"></a>Installera säkerhetskopieringsagenten

Alla virtuella datorer som backas upp av Azure Backup måste ha Backup-agenten (kallas även Microsoft Azure Recovery Service (MARS)-agent) installerad på den. Installera agenten på DPM-servern enligt följande:

1. Öppna det valv som du vill registrera DPM-servern.
2. I **inställningar**, klickar du på **egenskaper**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. På den **egenskaper** sidan kan du ladda ner Azure Backup-agenten.

    ![Ladda ned](./media/backup-azure-dpm-introduction/azure-backup-agent.png)


4. När du hämtat, kör du MARSAgentInstaller.exe. installera agenten på DPM-datorn.
5. Välj en installationsmappen och cachemappen för agenten. Det lediga utrymmet för cache-platsen måste vara minst 5% av säkerhetskopierade data.
6. Om du använder en proxyserver för att ansluta till internet, i den **proxykonfiguration** anger proxy-serverinformation. Om du använder en autentiserad proxyserver måste du ange användarinformation för namn och lösenord i den här skärmen.
7. Azure Backup-agenten installeras .NET Framework 4.5 och Windows PowerShell (om de inte är installerat) att slutföra installationen.
8. När agenten har installerats, **Stäng** fönstret.

    ![Stäng](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrera DPM-servern i valvet

1. I DPM-administratörskonsolen > **Management**, klickar du på **Online**. Välj **Registrera**. Guiden för serverregistrering öppnas.
2. I **proxykonfiguration**, ange proxyinställningarna vid behov.

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. I **Säkerhetskopieringsvalv**, bläddra till och markera filen med valvautentiseringsuppgifter som du laddade ned.

    ![Autentiseringsuppgifterna för valvet](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

10. I **begränsning**, du kan välja att bandbreddsbegränsning för säkerhetskopior. Du kan ange begränsningarna hastighet för ange arbetstid och dagar.

    ![Begränsningsinställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. I **Recovery inställningen**, ange en plats som kan användas vid återställning av data.

    - Azure Backup använder den här platsen som en tillfällig hållplatsområde för återställda data.
    - Azure Backup kommer att rensa upp data i det här området efter slutförs dataåterställning.
    - Platsen måste ha tillräckligt mycket utrymme för objekt som du vill återställa parallellt.

    ![Inställningen för återställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. I **krypteringsinställning** Generera eller ange en lösenfras.

    - Lösenfrasen används för att kryptera säkerhetskopiorna till molnet.
    - Ange minst 16 tecken.
    - Spara filen på en säker plats krävs för återställning.

    ![Kryptering](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Du äger den krypterade lösenfrasen och Microsoft har inte insyn i den.
    > Om lösenfrasen går förlorat; Microsoft kan hjälpa dig att återställa säkerhetskopierade data.

13. Klicka på **registrera** att registrera DPM-servern i valvet.

När servern har registrerats till valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure.

## <a name="troubleshoot-vault-credentials"></a>Felsöka valvautentiseringsuppgifter

### <a name="expiration-error"></a>Upphör att gälla-fel

Valvautentiseringsfilen är endast giltig för 48 timmar (när den har hämtats från portalen). Om det uppstår några fel i den här skärmen (till exempel ”valvautentiseringsuppgifter tillhandahållna filen har upphört att gälla”), logga in på Azure-portalen och ladda ned valvautentiseringsuppgifter filen igen.

### <a name="access-error"></a>Fel vid åtkomst

Se till att valvautentiseringsfilen finns på en plats som kan användas av installationsprogrammet. Om du får åtkomst till felen, kopiera filen med valvautentiseringsuppgifter till en tillfällig plats på den här datorn och försök igen.

### <a name="invalid-credentials-error"></a>Ogiltiga autentiseringsuppgifter fel

Om du stöter på ett ogiltigt vault credential-fel (till exempel ”ogiltiga valvautentiseringsuppgifter angivna”) i filen är antingen skadad eller har inte har de senaste autentiseringsuppgifterna som är associerade med recovery-tjänsten.

- Försök igen när du laddar ned en ny valvautentiseringsfil från portalen.
- Det här felet visas vanligtvis när du klickar på den **Download valvautentiseringen** alternativ i Azure-portalen, två gånger i snabb följd. I det här fallet gäller endast andra valvautentiseringsfilen.
