---
title: Konfigurera en processerver i Azure för VMware VM och fysisk återställning efter fel med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en processerver i Azure för återställning efter fel virtuella datorer i Azure till VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: ade47c59a8db673869ce8c60a062a2a6a6656ca2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916317"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>Konfigurera ytterligare processervrar för skalbarhet

Som standard när du replikerar virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), en processerver är installerad på configuration server-datorn och används för att samordna data som överförs mellan Site Recovery och den lokala infrastrukturen. För att öka kapacitet och skala upp replikeringsdistributionen kan du lägga till ytterligare fristående processervrar. Den här artikeln beskriver hur du gör detta.

## <a name="before-you-start"></a>Innan du börjar

### <a name="capacity-planning"></a>Kapacitetsplanering

Kontrollera att du har utfört [kapacitetsplanering](site-recovery-plan-capacity-vmware.md) för VMware-replikering. Detta hjälper dig att identifiera hur och när du ska distribuera ytterligare processervrar.

### <a name="sizing-requirements"></a>Storlekskraven 

Kontrollera storlekskraven sammanfattas i tabellen. Om du behöver skala distributionen till fler än 200 källdatorer, eller så har du totalt dagligen dataomsättningsfrekvensen på mer än 2 TB, måste du i allmänhet ytterligare processervrar att hantera trafik.

| **Kompletterande processervern** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 virtuella processorer (2 platser * 2 kärnor @ 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera datorer 85 eller mindre. |
|8 virtuella processorer (2 platser * 4 kärnor @ 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85 150 datorer. |
|12 virtuella processorer (2 platser * @ 2,5 GHz-6 kärnor) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150 225 datorer. |

### <a name="prerequisites"></a>Förutsättningar

Krav för den kompletterande processervern sammanfattas i tabellen nedan.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Ladda ned installationsfilen

Hämta installationsfilen för processervern enligt följande:

1. Logga in på Azure portal och bläddra till ditt Recovery Services-valv.
2. Öppna **Site Recovery-infrastruktur** > **VMWare och fysiska datorer** > **Konfigurationsservrar** (under för VMware och fysiska Datorer).
3. Välj configuration server att granska nedåt i server-information. Klicka sedan på **+ Processervern**.
4. I **lägga till processerver** >  **Välj där du vill distribuera processervern**väljer **distribuera en skalbar Processerver lokalt**.

  ![Lägg till servrar sida](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klicka på **ladda ned Microsoft Azure Site Recovery ett enhetligt installationsprogram**. Då hämtas den senaste versionen av installationsfilen.

  > [!WARNING]
  Processerverversionen för installation måste vara samma som, eller tidigare än, configuration server-version du har som körs. Ett enkelt sätt att kontrollera versionskompatibiliteten är att använda samma installationsprogram som senast användes för att installera eller uppdatera konfigurationsservern.

## <a name="install-from-the-ui"></a>Installera från Användargränssnittet

Installera på följande sätt. När du skapat servern kan migrera du källdatorer för att använda den.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installera från kommandoraden

Installera genom att köra följande kommando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Där kommandoradsparametrar är följande:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Exempel:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Skapa en fil med proxyinställningar

Om du vill konfigurera en proxyserver använder parametern ProxySettingsFilePath en fil som indata. Du kan skapa filen på följande sätt och skicka dem som indataparameter till ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [hantera bearbeta serverinställningar](vmware-azure-manage-process-server.md)
