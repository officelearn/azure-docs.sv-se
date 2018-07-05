---
title: Hur du installerar Azure IoT Edge på Windows med Linux-behållare | Microsoft Docs
description: Azure IoT Edge instruktioner för installation på Windows med Linux-behållare
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 503dfc0c7606d44a1b9ab635aa0d479df61f3820
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435481"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Installera Azure IoT Edge-körningen på Windows för användning med Linux-behållare

Azure IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta IoT Edge-agenten. Den **IoT Edge-agenten** underlättar distribution och övervakning av moduler på Edge-enheter, inklusive IoT Edge hub. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Windows x64 (AMD/Intel) system. Windows support förhandsvisas just nu.

>[!NOTE]
Med Linux-behållare på Windows sytems är inte en rekommenderad eller stöds produktionskonfigurationen för Azure IoT Edge. Det kan dock användas för utveckling och testning.

## <a name="supported-windows-versions"></a>Windows-versioner som stöds
Azure IoT Edge kan vara används för utveckling och testning på följande versioner av Windows, när du använder Linux-behållare:
  * Windows 10 eller senare operativsystem.
  * Windows Server 2016 eller nya server-operativsystem.

## <a name="install-the-container-runtime"></a>Installera runtime behållare 

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] container-körning (t.ex. Docker). 

Du kan använda [Docker för Windows] [ lnk-docker-for-windows] för utveckling och testning. Kontrollera Docker för Windows är [konfigurerad för att använda Linux-behållare][lnk-docker-config]

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Daemon för Azure IoT Edge-säkerhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

### <a name="download-the-edge-daemon-package-and-install"></a>Ladda ned Edge-daemon och installera

Kör följande kommandon i ett administratören PowerShell-fönster:

```powershell
Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
rmdir C:\ProgramData\iotedge\iotedged-windows
$env:Path += ";C:\ProgramData\iotedge"
SETX /M PATH "$env:Path"
```

Installera vcruntime med:

```powershell
Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
.\vc_redist.exe /quiet /norestart
 ```

Skapa och starta *iotedge* service:

```powershell
New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
Start-Service iotedge
```

Lägg till brandväggsundantag för de portar som används av tjänsten:

```powershell
New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
```

Skapa en **iotedge.reg** filen med följande innehåll och importera i Windows-registret genom att dubbelklicka på den eller använda den `reg import iotedge.reg` kommando:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge Security Daemon

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `C:\ProgramData\iotedge\config.yaml`.

Gränsenheten kan konfigureras manuellt med hjälp av en [enhetsanslutningssträngen] [ lnk-dcs] eller [automatiskt via Device Provisioning-tjänsten] [ lnk-dps].

* Manuell konfiguration kan du ta bort kommentarerna i **manuell** Etableringsläge. Uppdatera värdet för **device_connection_string** med anslutningssträngen från din IoT Edge-enhet.

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

* För automatisk konfiguration, ta bort kommentarerna i **dps** Etableringsläge. Uppdaterar du värdet för **scope_id** och **registration_id** med värden från IoT Hub DPS-instansen och din IoT Edge-enhet med TPM. 

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

Hämta namnet på edge-enhet med `hostname` kommando i PowerShell och ställa in det som värdet för **värdnamn:** i konfigurationen yaml. Exempel:

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

Sedan ange IP-adress och port för **workload_uri** och **management_uri** i den **ansluta:** och **lyssna:** delar av den konfiguration.

Om du vill hämta din IP-adress, ange `ipconfig` i PowerShell-fönster. Kopiera IP-adressen för den **vEthernet (DockerNAT)**' gränssnitt som visas i följande exempel (ip-adressen på datorn kan vara annorlunda):

![DockerNat][img-docker-nat]

Uppdatera den **workload_uri** och **management_uri** i den **ansluta:** avsnittet i konfigurationsfilen. Ersätt **\<gateway-adress\>** med IP-adress som du kopierade. 

```yaml
connect:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

Ange samma adresser i den **lyssna:** avsnittet av konfigurationen med hjälp av din IP-adress som gatewayadress.

```yaml
listen:
  management_uri: "http://<GATEWAY_ADDRESS>:15580"
  workload_uri: "http://<GATEWAY_ADDRESS>:15581"
```

I PowerShell-fönstret skapar du en miljövariabel **IOTEDGE_HOST** med den **management_uri** adress.

```powershell
[Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<GATEWAY_ADDRESS>:15580")
```

Spara miljövariabeln mellan omstarter.

```powershell
SETX /M IOTEDGE_HOST "http://<GATEWAY_ADDRESS>:15580"
```

Kontrollera slutligen att den **nätverk:** inställningen **moby_runtime:** tagit bort kommentarstecken från och tilldelas **azure iot edge**

```yaml
moby_runtime:
  docker_uri: "npipe://./pipe/docker_engine"
  network: "azure-iot-edge"
```

Spara konfigurationsfilen och starta om tjänsten:

```powershell
Stop-Service iotedge -NoWait
sleep 5
Start-Service iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM-Edge-enhet på Windows](how-to-auto-provision-simulated-device-windows.md#create-a-tpm-environment-variable).


Du kan kontrollera status för IoT Edge-tjänsten genom att: 

```powershell
Get-Service iotedge
```

Undersök tjänstloggar från de senaste 5 minuterna med:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false}
```

Och listan körs moduler med:

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med Edge-körningen installeras korrekt, Kolla in den [felsökning] [ lnk-trouble] sidan.


<!-- Images -->
[img-docker-nat]: ./media/how-to-install-iot-edge-windows-with-linux/dockernat.png

<!-- Links -->
[lnk-docker-config]: https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-windows.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
[lnk-docker-for-windows]: https://www.docker.com/docker-windows

