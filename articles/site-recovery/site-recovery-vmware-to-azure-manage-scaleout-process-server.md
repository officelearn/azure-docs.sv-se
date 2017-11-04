---
title: " Hantera en skalbar Processerver i Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar och hanterar en skalbar Processerver i Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: e5c01de19917235c34c035415df86291b9152bf0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-scale-out-process-server"></a>Hantera en skalbar Processerver

Skalbar Processerver fungerar som en koordinator för att överföra data mellan Site Recovery services och lokal infrastruktur. Den här artikeln beskriver hur du kan skapa, konfigurera och hantera en skalbar Processerver.

## <a name="prerequisites"></a>Krav
Följande är den rekommenderade maskinvara, programvara och nätverkskonfiguration som krävs för att konfigurera en skalbar Processerver.

> [!NOTE]
> [Kapacitetsplanering](site-recovery-capacity-planner.md) är ett viktigt steg så att du distribuerar skalbar Processerver med en konfiguration som paket belastningen-krav. Läs mer om [skalning egenskaper för en skalbar Processerver](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Hämta programvaran skalbar Processerver
1. Logga in på Azure-portalen och bläddra till Recovery Services-ventilen.
2. Bläddra till **Site Recovery-infrastruktur** > **Konfigurationsservrar** (under för VMware och fysiska datorer).
3. Välj din konfigurationsservern och öka detaljnivån till server configuration informationssidan.
4. Klicka på den **+ Processervern** knappen.
5. I den **lägga till processervern** väljer **distribuera en skalbar Processerver lokala** alternativet från den **Välj där du vill distribuera din processerver för** listrutan.

  ![Lägg till servrar på sidan](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Klicka på den **ladda ned Microsoft Azure Site Recovery Unified installationsprogram** länk för att hämta den senaste versionen av skalbara processen Server-installationen.

  > [!WARNING]
  Versionen för din skalbar Processerver måste vara lika med eller lägre version än konfigurationsservern körs i din miljö. Ett enkelt sätt att kontrollera versionskompatibiliteten är att använda samma installer bitarna som du nyligen använt för att installera/uppdatera din konfigurationsservern.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Installera och registrera en skalbar Processerver från GUI
Om du har skala upp distributionen utöver 200 källdatorer eller en daglig totala omsättningen på mer än 2 TB, om du behöver ytterligare servrar att hantera trafikvolymen.

Kontrollera den [storlek rekommendationer för servrar](#size-recommendations-for-the-process-server), och följ sedan anvisningarna för att ställa in processervern. När du har installerat på servern, kan du migrera källdatorer för att använda den.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Installera och registrera en skalbar Processerver med hjälp av kommandoraden

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Exempel på användning
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Skalbar Processerver installer kommandoradsargument.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Skapa en konfigurationsfil för proxy-inställningar
Parametern ProxySettingsFilePath använder en fil som indata. Skapa fil med följande format och skickar den som indataparameter till ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Ändra proxyinställningar för skalbar Processerver
1. Logga in på servern för skalbar processen.
2. Öppna ett kommandofönster Admin PowerShell.
3. Kör följande kommando
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Bläddra sedan till katalogen **%PROGRAMDATA%\ASR\Agent** och kör följande kommando
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Registrera en skalbar Processerver
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Nästa gång du öppna en kommandotolk för administratören.
* Bläddra till mappen **%PROGRAMDATA%\ASR\Agent** och kör kommandot

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Uppgradera en skalbar Processerver
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Inaktivering av en skalbar Processerver
1. Se till att:
  - Status för Configuration Server-anslutningen visas som **ansluten** i Azure-portalen
  - Processen för servern är fortfarande kunna kommunicera med konfigurationsservern.
2. Logga in till processervern som administratör
3. Öppna Kontrollpanelen > Program > avinstallera program
4. Avinstallera program i den ordning som anges i följande:
  * Server-processen för Microsoft Azure Site Recovery konfigurationsservern
  * Microsoft Azure Site Recovery Configuration Server beroenden
  * Microsoft Azure Recovery Services-agent

Det kan ta upp till 15 minuter för att Processervern återspeglar i Azure-portalen.

  > [!NOTE]
  Om processervern kan inte kommunicera med konfigurationsservern (anslutningens status i portalen är frånkopplad), måste du följa anvisningarna nedan om du vill rensa från konfigurationsservern.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Avregistrerar en frånkopplad skalbar processerver från en Server Configuration

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Ändra storlek på kraven för en skalbar Processerver

| **Ytterligare processervern** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- |
|4 vCPUs (2 sockets * 2 kärnor @ 2,5 GHz), 8 GB minne |300 GB |250 GB eller mindre |Replikera 85 eller mindre datorer. |
|8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz), 12 GB minne |600 GB |250 GB till 1 TB |Replikera mellan 85 150 datorer. |
|12 vCPUs (2 sockets * 6 kärnor @ 2,5 GHz) 24 GB minne |1 TB |1 TB till 2 TB |Replikera mellan 150 225 datorer. |
