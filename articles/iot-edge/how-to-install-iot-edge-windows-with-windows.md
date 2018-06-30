---
title: Hur du installerar Azure IoT kanten på Windows med Windows behållare | Microsoft Docs
description: Azure IoT kant instruktioner för installation i Windows med Windows-behållare
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 1ae51d948fdaa5654c59549d384b784f0e87dcc3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112627"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Installera Azure IoT kant körning i Windows för användning med Windows-behållare

Azure IoT kant runtime distribueras på alla kant för IoT-enheter. Den har tre komponenter. Den **IoT Edge security daemon** ger och underhåller säkerhetskrav på gränsenheten. Daemon startas på varje start och startar enheten genom att starta IoT kant-agenten. Den **IoT kant agent** underlättar distribution och övervakning av moduler på Edge-enheten, inklusive kant för IoT-hubben. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln innehåller steg för att installera Azure IoT kant-körningsmiljön på Windows x64 (AMD/Intel) system. 

Stöd för Windows är för närvarande under förhandsgranskning.

## <a name="supported-windows-versions"></a>Windows-versioner som stöds
Azure IoT kanten med Windows-behållare kan användas med:
  * Windows 10/IoT Enterprise/IoT kärnor med April 2018 update (skapa 17134).
  * Windows Server 1803

## <a name="install-the-container-runtime"></a>Installera behållaren runtime 

>[!NOTE]
>För behållaren motorn installeras på Windows IoT Core, följer du stegen från [etablera en IoT-Core enheten artikel] [ lnk-iot-core] och fortsätt sedan med instruktionerna nedan.

Azure IoT-Edge förlitar sig på en [OCI-kompatibel] [ lnk-oci] behållare runtime (t.ex. Docker). Du kan använda [Docker för Windows] [ lnk-docker-for-windows] för utveckling och testning. 

**Kontrollera Docker för Windows är [konfigurerad för att använda Windows-behållare][lnk-docker-config]**

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Azure IoT-Edge Security Daemon

>[!NOTE]
>Azure IoT kant programvarupaket regleras licensvillkoren finns i paket (i katalogen licens). Läs licensvillkoren innan du använder paketet. Din installation och användning av paketet som utgör ditt godkännande av villkoren. Om du inte godkänner licensvillkoren, Använd inte paketet.

### <a name="download-the-edge-daemon-package-and-install"></a>Hämta daemon kant och installera

Kör följande kommandon i en administratör PowerShell-fönstret:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Installera vcruntime med (du kan hoppa över det här steget på en insticksenhet för IoT-core):

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Skapa och starta **iotedge** tjänsten:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Lägg till brandväggsundantag för portar som används av tjänsten:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Skapa en **iotedge.reg** med följande innehåll, och importera i Windows-registret genom att dubbelklicka på den eller använda den `reg import iotedge.reg` kommando:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT-Edge Security-Daemon

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `C:\ProgramData\iotedge\config.yaml`.

Gränsenheten kan konfigureras manuellt med hjälp av en [enheten anslutningssträngen] [ lnk-dcs] eller [automatiskt via enhet Etableringstjänsten] [ lnk-dps].

* Manuell konfiguration kan du ta bort kommentarerna i **manuell** Etableringsläge. Uppdatera värdet i **device_connection_string** med anslutningssträngen från IoT-Edge-enhet.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* Automatisk konfiguration, Avkommentera den **dp** Etableringsläge. Uppdatera värdena för **scope_id** och **registration_id** med värden från din IoT-hubb DP-instans och enheten IoT kanten med TPM. 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Hämta namnet på edge enheter med hjälp av `hostname` kommando i PowerShell och anges som värde för **värdnamn:** i konfigurationen yaml. Exempel:

```yaml
  ###############################################################################
  # Edge device hostname
  ###############################################################################
  #
  # Configures the environment variable 'IOTEDGE_GATEWAYHOSTNAME' injected into
  # modules.
  #
  ###############################################################################

  hostname: "edgedevice-1"
```

Därefter måste du ange ip-adress och port för **workload_uri** och **management_uri** i den **ansluta:** avsnitt i konfigurationen.

Ip-adress, ange `ipconfig` i PowerShell-fönstret och välj ip-adressen för den **vEthernet (nat)** gränssnitt som visas i exemplet nedan (ip-adressen på datorn kan vara olika):  

![nat][img-nat]

```yaml
connect:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

Ange samma adresserna i den **lyssna:** avsnitt i konfigurationen. Exempel:

```yaml
listen:
  management_uri: "http://172.29.240.1:15580"
  workload_uri: "http://172.29.240.1:15581"
```

Skapa en miljövariabel i PowerShell-fönstret **IOTEDGE_HOST** med den **management_uri** adress, exempel:

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://172.29.240.1:15580")
```

Kontrollera slutligen den **nätverk:** inställningen **moby_runtime:** har tagit bort kommentarstecken från och ange **nat**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "nat"
```

Spara konfigurationsfilen och starta om tjänsten:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Kontrollera att installationen lyckades

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt IoT kant-körningen ska vara har etablerad och körs på enheten. Om du har använt den **automatisk konfiguration** steg, måste du slutföra några ytterligare steg så att körningen kan registrera din enhet med din IoT-hubb för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM Edge-enhet i Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).

Du kan kontrollera status för tjänsten IoT kanten av: 

```powershell
Get-Service iotedge
```

Undersök tjänstloggar för de senaste 5 minuterna med:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Och lista med moduler med:

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med kant-körningsmiljön installeras korrekt, checka ut den [felsökning] [ lnk-trouble] sidan.


<!-- Images -->
[img-nat]: ./media/how-to-install-iot-edge-windows-with-windows/nat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows
[lnk-iot-core]: how-to-install-iot-core.md

