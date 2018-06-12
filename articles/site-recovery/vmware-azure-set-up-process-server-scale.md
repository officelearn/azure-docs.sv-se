---
title: Ställ in en processerver i Azure för VM VMware och fysiska servrar återställning med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en processerver i Azure, återställa virtuella Azure-datorer för VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 3e53954341136a293052f9af755515a5552432fe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300855"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>Konfigurera ytterligare servrar för skalbarhet

Som standard när du replikera virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), en processerver är installerad på configuration server-datorn och används för att samordna dataöverföring mellan Site Recovery och lokal infrastruktur. Om du vill öka kapaciteten och skala upp replikeringsdistributionen, kan du lägga till ytterligare fristående servrar. Den här artikeln beskriver hur du gör detta.

## <a name="before-you-start"></a>Innan du börjar

### <a name="capacity-planning"></a>Kapacitetsplanering

Kontrollera att du har utfört [kapacitetsplanering](site-recovery-plan-capacity-vmware.md) för VMware-replikering. Detta hjälper dig att identifiera hur och när du ska distribuera ytterligare servrar.

### <a name="sizing-requirements"></a>Storlek för krav 

Kontrollera storlek kraven sammanfattas i tabellen. Om du behöver skala distributionen till fler än 200 källdatorer eller du har totalt dagligen omsättningsuppdateringar av mer än 2 TB, måste du i allmänhet ytterligare servrar att hantera trafikvolymen.

| **Ytterligare processervern** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 vCPUs (2 sockets * 2 kärnor @ 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera 85 eller mindre datorer. |
|8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85 150 datorer. |
|12 vCPUs (2 sockets * 6 kärnor @ 2,5 GHz) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150 225 datorer. |

### <a name="prerequisites"></a>Förutsättningar

Krav för ytterligare processervern sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Hämta installationsfilen

Hämta installationsfilen för processervern på följande sätt:

1. Logga in på Azure-portalen och bläddra till Recovery Services-ventilen.
2. Öppna **Site Recovery-infrastruktur** > **VMWare och fysiska datorer** > **Konfigurationsservrar** (under för VMware och fysiska Datorer).
3. Välj konfigurationsservern och öka detaljnivån till serverinformation. Klicka på **+ Processervern**.
4. I **lägga till processervern** >  **Välj där du vill distribuera processervern**väljer **distribuera en skalbar Processerver lokala**.

  ![Lägg till servrar på sidan](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klicka på **ladda ned Microsoft Azure Site Recovery Unified installationsprogrammet**. Här kan du hämta den senaste versionen av installationsfilen.

  > [!WARNING]
  Processerverversionen installation ska vara samma som, eller tidigare än har configuration server-version du kör. Ett enkelt sätt att kontrollera versionskompatibiliteten är att använda samma installationsprogram som senast användes för att installera eller uppdatera din konfigurationsservern.

## <a name="install-from-the-ui"></a>Installera från Användargränssnittet

Installera på följande sätt. När du har installerat på servern, kan du migrera källdatorer för att använda den.

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

Om du behöver konfigurera en proxyserver använder parametern ProxySettingsFilePath en fil som indata. Du kan skapa filen enligt följande och överför den som indataparameter till ProxySettingsFilePath.

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
