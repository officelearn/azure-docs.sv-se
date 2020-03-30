---
title: Konfigurera en skalningsprocessserver vid haveriberedskap av virtuella datorer och fysiska servrar med Azure Site Recovery | Microsoft Dokument"
description: I den här artikeln beskrivs hur du konfigurerar utskalningsprocessserver vid haveriberedskap av virtuella datorer och fysiska servrar.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257152"
---
# <a name="scale-with-additional-process-servers"></a>Skala med ytterligare processservrar

När du replikerar virtuella datorer med VMware eller fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md)installeras som standard en processserver på konfigurationsserverdatorn och används för att samordna dataöverföring mellan platsåterställning och din lokala infrastruktur. Om du vill öka kapaciteten och skala ut replikeringsdistributionen kan du lägga till ytterligare fristående processservrar. I den här artikeln beskrivs hur du konfigurerar en utskalningsprocessserver.

## <a name="before-you-start"></a>Innan du börjar

### <a name="capacity-planning"></a>Kapacitetsplanering

Kontrollera att du har utfört [kapacitetsplanering](site-recovery-plan-capacity-vmware.md) för VMware-replikering. Detta hjälper dig att identifiera hur och när du bör distribuera ytterligare processservrar.

Från 9.24-versionen läggs vägledning till vid val av processserver för nya replikeringar. Processservern markeras felfri, varning och kritisk baserat på vissa kriterier. Om du vill förstå olika scenarier som kan påverka processserverns tillstånd läser du [processserveraviseringarna](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Användning av en klonad Process Server-komponent stöds inte. Följ stegen i den här artikeln för varje PS-utskalning.

### <a name="sizing-requirements"></a>Storlekskrav 

Kontrollera storlekskraven som sammanfattas i tabellen. Om du måste skala distributionen till mer än 200 källdatorer, eller om du har en total daglig omsättningshastighet på mer än 2 TB, behöver du i allmänhet ytterligare processservrar för att hantera trafikvolymen.

| **Ytterligare processserver** | **Cachediskstorlek** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 vCPUs (2 uttag * \@ 2 kärnor 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera 85 eller färre maskiner. |
|8 virtuella processorer (2 uttag * \@ 4 kärnor 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85-150 maskiner. |
|12 virtuella processorer (2 uttag * \@ 6 kärnor 2,5 GHz) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150-225 maskiner. |

Där varje skyddad källdator är konfigurerad med 3 diskar på 100 GB vardera.

### <a name="prerequisites"></a>Krav

Förutsättningarna för ytterligare processserver sammanfattas i följande tabell.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Hämta installationsfil

Hämta installationsfilen för processservern enligt följande:

1. Logga in på Azure-portalen och bläddra till ditt Recovery Services Vault.
2. Öppna > VMWare-infrastruktur för **platsåterställningsinfrastruktur****VMWare och konfigurationsservrar för fysiska** > **datorer** för fysiska datorer för fysiska datorer för VM &ware.
3. Välj den konfigurationsserver som du vill öka detaljnivån i serverinformationen. Klicka sedan på **+ Process Server**.
4. I **Lägg till processserver** >  **Välj var du vill distribuera processservern**väljer du **Distribuera en utskalningsprocessserver lokalt**.

   ![Sidan Lägg till servrar](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klicka på **Hämta installationsprogrammet för Enhetlig Microsoft Azure-webbplatsåterställning**. Detta hämtar den senaste versionen av installationsfilen.

   > [!WARNING]
   > Installationsversionen av processservern ska vara samma som eller tidigare än den konfigurationsserverversion som du har på körning. Ett enkelt sätt att säkerställa versionskompatibilitet är att använda samma installationsprogram, som du senast använde för att installera eller uppdatera konfigurationsservern.

## <a name="install-from-the-ui"></a>Installera från användargränssnittet

Installera enligt följande. När du har konfigurerat servern migrerar du källdatorer för att använda den.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Installera från kommandoraden

Installera genom att köra följande kommando:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Om kommandoradsparametrarna är följande:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Ett exempel:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Skapa en proxyinställningsfil

Om du behöver konfigurera en proxy tar parametern ProxySettingsFilePath en fil som indata. Du kan skapa filen på följande sätt och skicka den som parameter ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [hanterar inställningar för processserver](vmware-azure-manage-process-server.md)
