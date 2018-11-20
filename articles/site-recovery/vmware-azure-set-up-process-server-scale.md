---
title: Konfigurera en processerver i Azure för att växla tillbaka under haveriberedskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in en processerver i Azure, för att återställa från Azure till lokala under haveriberedskap för virtuella VMware-datorer och fysiska servrar.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: ramamill
ms.openlocfilehash: 50d1170f64ae199f4a1bf4b852c1ed3232df4d42
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51974812"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Skala för återställning efter fel med ytterligare processervrar

Som standard när du replikerar virtuella VMware-datorer eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md), en processerver är installerad på configuration server-datorn och används för att samordna data som överförs mellan Site Recovery och den lokala infrastrukturen. För att öka kapacitet och skala upp replikeringsdistributionen kan du lägga till ytterligare fristående processervrar. Den här artikeln beskriver hur du gör detta.

## <a name="before-you-start"></a>Innan du börjar

### <a name="capacity-planning"></a>Kapacitetsplanering

Kontrollera att du har utfört [kapacitetsplanering](site-recovery-plan-capacity-vmware.md) för VMware-replikering. Detta hjälper dig att identifiera hur och när du ska distribuera ytterligare processervrar.

### <a name="sizing-requirements"></a>Storlekskraven 

Kontrollera storlekskraven sammanfattas i tabellen. Om du behöver skala distributionen till fler än 200 källdatorer, eller så har du totalt dagligen dataomsättningsfrekvensen på mer än 2 TB, måste du i allmänhet ytterligare processervrar att hantera trafik.

| **Kompletterande processervern** | **Cachestorleken för disk** | **Dataändringshastigheten** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 virtuella processorer (2 platser * 2 kärnor \@ 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera datorer 85 eller mindre. |
|8 virtuella processorer (2 platser * 4 kärnor \@ 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85 150 datorer. |
|12 virtuella processorer (2 platser * 6 kärnor \@ 2,5 GHz) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150 225 datorer. |

Där varje skyddad källdatorn är konfigurerad med 3 diskar på 100 GB vardera.

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
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
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
