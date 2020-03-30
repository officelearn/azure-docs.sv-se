---
title: Förbereda DPM-servern för säkerhetskopiering av arbetsbelastningar
description: I den här artikeln kan du lära dig hur du förbereder för DPM-säkerhetskopior (System Center Data Protection Manager) till Azure med hjälp av Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 2119d46ca6102286ca879777058a49938b501ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273467"
---
# <a name="prepare-to-back-up-workloads-to-azure-with-system-center-dpm"></a>Förbered säkerhetskopiering av arbetsbelastningar till Azure med System Center DPM

I den här artikeln beskrivs hur du förbereder för DPM-säkerhetskopior (System Center Data Protection Manager) till Azure med hjälp av Azure Backup-tjänsten.

Artikeln innehåller:

- En översikt över distribution av DPM med Azure Backup.
- Förutsättningar och begränsningar för att använda Azure Backup med DPM.
- Steg för att förbereda Azure, inklusive att konfigurera ett Säkerhetskopieringsvalv för återställningstjänster, och eventuellt ändra typen av Azure-lagring för valvet.
- Steg för att förbereda DPM-servern, inklusive hämtning av autentiseringsuppgifter för valvet, installera Azure Backup-agenten och registrera DPM-servern i valvet.
- Felsökningstips för vanliga fel.

## <a name="why-back-up-dpm-to-azure"></a>Varför säkerhetskopiera DPM till Azure?

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview) säkerhetskopierar fil- och programdata. DPM interagerar med Azure Backup enligt följande:

- **DPM som körs på en fysisk server eller lokal virtuell dator** – Du kan säkerhetskopiera data till ett säkerhetskopieringsvalv i Azure, förutom säkerhetskopiering av disk och band.
- **DPM körs på en Virtuell Azure** – Från System Center 2012 R2 med uppdatering 3 eller senare kan du distribuera DPM på en Virtuell Azure-dator. Du kan säkerhetskopiera data till Azure-diskar som är anslutna till den virtuella datorn, eller använda Azure Backup för att säkerhetskopiera data till ett säkerhetskopieringsvalv.

Affärsfördelarna med säkerhetskopiering av DPM-servrar till Azure är:

- För lokal DPM är Azure Backup ett alternativ till långsiktig distribution till band.
- För DPM som körs på en Virtuell Azure kan du med Azure Backup avlasta lagring från Azure-disken. Om du lagrar äldre data i ett säkerhetskopieringsvalv kan du skala upp ditt företag genom att lagra nya data på disk.

## <a name="prerequisites-and-limitations"></a>Krav och begränsningar

**Inställning** | **Krav**
--- | ---
DPM på en virtuell Azure-dator | System Center 2012 R2 med DPM 2012 R2 Samlad uppdatering 3 eller senare.
DPM på en fysisk server | System Center 2012 SP1 eller senare; System Center 2012 R2.
DPM på en virtuell hyper-V-dator | System Center 2012 SP1 eller senare; System Center 2012 R2.
DPM på en virtuell virtuell VMware-dator | System Center 2012 R2 med samlad uppdatering 5 eller senare.
Komponenter | DPM-servern bör ha Windows PowerShell och .NET Framework 4.5 installerat.
Appar som stöds | [Läs mer om](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix) vad som kan säkerhetskopieras av DPM.
Filtyper som stöds | Dessa filtyper kan säkerhetskopieras med Azure Backup: Krypterad (endast fullständiga säkerhetskopior); Komprimerad (inkrementella säkerhetskopior stöds); Sparse (inkrementella säkerhetskopior stöds); Komprimerad och gles (behandlas som gles).
Filtyper som inte stöds | Servrar på skiftlägeskänsliga filsystem. hårda länkar (hoppas över); reparse punkter (hoppas över); krypterad och komprimerad (hoppas över); krypterad och gles (hoppas över); Komprimerad ström; tolka ström.
Lokal lagring | Varje dator som du vill säkerhetskopiera måste ha lokal kostnadsfri lagring som är minst 5 % av storleken på de data som säkerhetskopieras. Säkerhetskopiering av 100 GB data kräver till exempel minst 5 GB ledigt utrymme på scratch-platsen.
Arkivlagring | Det finns ingen gräns för hur mycket data du kan säkerhetskopiera till ett Azure Backup-valv, men storleken på en datakälla (till exempel en virtuell dator eller databas) bör inte överstiga 54 400 GB.
Azure ExpressRoute | Om Azure ExpressRoute är konfigurerat med Privat eller Microsoft-peering kan det inte användas för att säkerhetskopiera data till Azure.<br/><br/> Om Azure ExpressRoute är konfigurerat med offentlig peering kan den användas för att säkerhetskopiera data till Azure.<br/><br/> **Anm.:** Offentlig peering är föråldrad för nya kretsar.
Azure Backup-agent | Om DPM körs på System Center 2012 SP1 installerar du Samlad 2 eller senare för DPM SP1. Detta krävs för agentinstallation.<br/><br/> I den här artikeln beskrivs hur du distribuerar den senaste versionen av Azure Backup-agenten, även känd som MARS-agenten (Microsoft Azure Recovery Service). Om du har distribuerat en tidigare version uppdaterar du till den senaste versionen för att säkerställa att säkerhetskopieringen fungerar som förväntat.

Innan du börjar behöver du ett Azure-konto med Azure Backup-funktionen aktiverad. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Läs om [prissättning för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-settings"></a>Ändra lagringsinställningar

Du kan välja mellan geoupptundret lagring och lokalt redundant lagring.

- Valvet använder geo-redundant lagring som standard.
- Om valvet är din primära säkerhetskopia lämnar du alternativet inställt på geoupptundit lagringsutrymme. Om du vill ha ett billigare alternativ som inte är riktigt lika hållbart använder du följande procedur för att konfigurera lokalt redundant lagring.
- Lär dig mer om [Azure-lagring](../storage/common/storage-redundancy.md)och [de geouppsagbara](../storage/common/storage-redundancy-grs.md) och [lokalt redundanta](../storage/common/storage-redundancy-lrs.md) lagringsalternativen.
- Ändra lagringsinställningarna innan den första säkerhetskopieringen. Om du redan har säkerhetskopierat ett objekt slutar du säkerhetskopiera det i valvet innan du ändrar lagringsinställningarna.

Så här redigerar du inställningen för lagringsreplikering:

1. Öppna instrumentpanelen för valvet.

2. Klicka på **Infrastruktur för säkerhetskopiering**i **Hantera**.

3. Välj ett lagringsalternativ för valvet på menyn Konfiguration för **säkerhetskopiering.**

    ![Lista över säkerhetskopieringsvalv](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

## <a name="download-vault-credentials"></a>Ladda ned autentiseringsuppgifter för valvet

Du använder valvautentiseringsuppgifter när du registrerar DPM-servern i valvet.

- Valvautentiseringsfilen är ett certifikat som genereras av portalen för varje säkerhetskopieringsvalv.
- Portalen överför sedan den offentliga nyckeln till Access Control Service (ACS).
- Under arbetsflödet för maskinregistrering görs certifikatets privata nyckel tillgänglig för användaren, vilket autentiserar datorn.
- Baserat på autentiseringen skickar Azure Backup-tjänsten data till det identifierade valvet.

### <a name="best-practices-for-vault-credentials"></a>Metodtips för autentiseringsuppgifter för arkiv

Hämta autentiseringsuppgifterna genom att hämta valvautentiseringsfilen via en säker kanal från Azure-portalen:

- Autentiseringsuppgifterna för valvet används endast under registreringsarbetsflödet.
- Det är ditt ansvar att se till att valvautentiseringsuppgifterna är säker och inte komprometterade.
  - Om kontrollen över autentiseringsuppgifterna går förlorad kan valvautentiseringsuppgifterna användas för att registrera andra datorer i valvet.
  - Säkerhetskopierade data krypteras dock med hjälp av en lösenfras som tillhör kunden, så att befintliga säkerhetskopierade data inte kan komprometteras.
- Kontrollera att filen sparas på en plats som kan nås från DPM-servern. Om den lagras i en filresurs/SMB kontrollerar du om det finns åtkomstbehörigheter.
- Autentiseringsuppgifter för valvet upphör att gälla efter 48 timmar. Du kan hämta nya valvreferenser så många gånger som behövs. Endast den senaste arkivautentiseringsfilen kan dock användas under registreringsarbetsflödet.
- Azure Backup-tjänsten är inte medveten om certifikatets privata nyckel och den privata nyckeln är inte tillgänglig i portalen eller tjänsten.

Hämta autentiseringsfilen för valvet till en lokal dator enligt följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna valvet där du vill registrera DPM-servern.
3. Klicka på **Egenskaper**i **Inställningar**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. Klicka på Hämta i**Autentiseringsuppgifter för** **Egenskaper** > för **säkerhetskopiering.** Portalen genererar arkivautentiseringsfilen med en kombination av valvets namn och aktuellt datum, och gör den tillgänglig för hämtning.

    ![Ladda ned](./media/backup-azure-dpm-introduction/vault-credentials.png)

5. Klicka på **Spara** om du vill hämta autentiseringsuppgifterna för valvet till mappen eller **Spara som** och ange en plats. Det tar upp till en minut innan filen genereras.

## <a name="install-the-backup-agent"></a>Installera säkerhetskopieringsagenten

Varje dator som backas upp av Azure Backup måste ha backup-agenten (även känd som Microsoft Azure Recovery Service (MARS)-agenten installerad på den. Installera agenten på DPM-servern enligt följande:

1. Öppna valvet som du vill registrera DPM-servern till.
2. Klicka på **Egenskaper**i **Inställningar**.

    ![Öppna menyn för valvet](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. Ladda ned Azure Backup Agent på sidan **Egenskaper.**

    ![Ladda ned](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

4. När du har hämtat körningen AV MARSAgentInstaller.exe. för att installera agenten på DPM-maskinen.
5. Välj en installationsmapp och cachemapp för agenten. Ledigt utrymme för cacheplatsen måste vara minst 5 % av säkerhetskopieringsdata.
6. Om du använder en proxyserver för att ansluta till Internet anger du information om proxyservern på **konfigurationsskärmen för proxyserver.** Om du använder en autentiserat proxy anger du användarnamn och lösenordsinformation på den här skärmen.
7. Azure Backup-agenten installerar .NET Framework 4.5 och Windows PowerShell (om de inte är installerade) för att slutföra installationen.
8. Stäng fönstret **när** agenten har installerats.

    ![Stäng](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

## <a name="register-the-dpm-server-in-the-vault"></a>Registrera DPM-servern i valvet

1. Klicka på **Online**i DPM-administratörskonsolen > **Management**. Välj **Registrera**. Guiden Registrera server öppnas.
2. Ange proxyinställningarna efter behov i **Proxykonfiguration.**

    ![Proxykonfiguration](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
3. Bläddra **Backup Vault**till och välj autentiseringsfilen för valv som du hämtade i Säkerhetskopieringsvalvet.

    ![Autentiseringsuppgifter för arkiv](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

4. I **Begränsningsinställning**kan du aktivera bandbreddsbegränsning för säkerhetskopior. Du kan ange hastighetsgränser för ange arbetstider och dagar.

    ![Begränsningsinställning](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

5. Ange en plats som kan användas vid dataåterställning i **återställningsmappinställningen.**

    - Azure Backup använder den här platsen som ett tillfälligt anläggningsområde för återställda data.
    - När du har avslutat dataåterställning rensar Azure Backup data i det här området.
    - Platsen måste ha tillräckligt med utrymme för att rymma objekt som du räknar med att återställa parallellt.

    ![Inställning av återställningsmapp](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

6. I **krypteringsinställningen**genererar eller tillhandahåller du en lösenfras.

    - Lösenfrasen används för att kryptera säkerhetskopiorna till molnet.
    - Ange minst 16 tecken.
    - Spara filen på en säker plats, det behövs för återställning.

    ![Kryptering](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Du äger krypteringslösenfrasen och Microsoft har inte insyn i den.
    > Om lösenfrasen är förlorad eller glömd; Microsoft kan inte hjälpa till att återställa säkerhetskopieringsdata.

7. Klicka på **Registrera** om du vill registrera DPM-servern i valvet.

När servern har registrerats i valvet och du är nu redo att börja säkerhetskopiera till Microsoft Azure. Du måste konfigurera skyddsgruppen i DPM-konsolen för att säkerhetskopiera arbetsbelastningar till Azure. [Lär dig hur du](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) distribuerar skyddsgrupper.

## <a name="troubleshoot-vault-credentials"></a>Felsöka autentiseringsuppgifter för arkiv

### <a name="expiration-error"></a>Fel vid förfallodatum

Autentiseringsfilen för valvet är endast giltig i 48 timmar (när den har hämtats från portalen). Om du stöter på något fel på den här skärmen (till exempel "Arkiv-autentiseringsfilen har upphört att gälla" loggar du in på Azure-portalen och hämtar autentiseringsfilen för valvet igen.

### <a name="access-error"></a>Åtkomstfel

Kontrollera att autentiseringsfilen för valvet är tillgänglig på en plats som kan nås av inställningsprogrammet. Om du stöter på åtkomstrelaterade fel kopierar du autentiseringsfilen för valvet till en tillfällig plats på den här datorn och försöker utföra åtgärden igen.

### <a name="invalid-credentials-error"></a>Ogiltigt autentiseringsfel

Om du stöter på ett ogiltigt autentiseringsfel för valvet (till exempel "Ogiltiga autentiseringsuppgifter för valvet") är filen antingen skadad eller inte de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten.

- Försök igen när du har hämtat en ny arkivautentiseringsfil från portalen.
- Det här felet visas vanligtvis när du klickar på alternativet **Hämta valvautentiseringsuppgifter** i Azure-portalen, två gånger i snabb följd. I det här fallet är endast den andra valvautentiseringsfilen giltig.
