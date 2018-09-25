---
title: Hur du installerar Azure IoT Edge på Windows med Windows-behållare | Microsoft Docs
description: Azure IoT Edge instruktioner för installation på Windows med Windows-behållare
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 2eebc96b14ee0f06b3bd88ea565dfe9372aba1ff
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037822"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Installera Azure IoT Edge-körningen på Windows för användning med Windows-behållare

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller steg för att installera Azure IoT Edge-körningen med Windows-behållare på din Windows x64 (AMD/Intel) system. 

Windows support förhandsvisas just nu.

## <a name="supported-windows-versions"></a>Windows-versioner som stöds
Azure IoT Edge med Windows-behållare kan användas med:
  * Windows 10/IoT Enterprise/IoT Core med April 2018 uppdaterar (skapa 17134).
  * Windows Server 1803

Mer information om vilka operativsystem stöds för närvarande finns i [support för Azure IoT Edge](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>Installera runtime behållare 

>[!NOTE]
>Behållaren motorn installeras på Windows IoT Core, följer du stegen från [etablera en IoT Core enheten artikel] [ lnk-iot-core] och fortsätt sedan med anvisningarna nedan.

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] runtime behållare (till exempel Docker). Du kan använda [Docker för Windows] [ lnk-docker-for-windows] för utveckling och testning. 

Konfigurera Docker för Windows [du använder Windows-behållare][lnk-docker-config].

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Daemon för Azure IoT Edge-säkerhet

>[!NOTE]
>Azure IoT Edge-programvarupaket omfattas licensvillkoren finns i paket (i katalogen licens). Läs igenom licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

### <a name="install-and-manually-provision"></a>Installera och etablera manuellt

1. Följ stegen i [registrera en ny Azure IoT Edge-enhet] [ lnk-dcs] att registrera din enhet och hämta enhetens anslutningssträng. 

2. Kör PowerShell som administratör på din IoT Edge-enhet. 

3. Hämta och installera IoT Edge-körningen. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. När du tillfrågas om en **DeviceConnectionString**, ange anslutningssträngen som du hämtade från IoT Hub. Ta inte med citattecknen runt anslutningssträngen. 

### <a name="install-and-automatically-provision"></a>Installera och automatiskt etablera

1. Följ stegen i [skapa och etablera en simulerad TPM-Edge-enhet på Windows] [ lnk-dps] att konfigurera Device Provisioning-tjänsten och hämta dess **Scopeid**, simulera en TPM enhets- och hämta dess **registrerings-ID**, skapa en enskild registrering. När enheten är registrerad i IoT Hub, fortsätter du med installationen.  

   >[!TIP]
   >Lämna fönstret med TPM-simulatorn öppen under installationen och testning. 

2. Kör PowerShell som administratör på din IoT Edge-enhet. 

3. Hämta och installera IoT Edge-körningen. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. När du uppmanas, anger den **ScopeID** och **RegistrationID** för din etableringstjänst och en enhet. 

## <a name="verify-successful-installation"></a>Verifiera installationen

Du kan kontrollera status för IoT Edge-tjänsten genom att: 

```powershell
Get-Service iotedge
```

Kontrollera tjänstloggar för de senaste 5 minuterna med:

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Och listan körs moduler med:

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Om nätverket har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md) att installera och starta IoT Edge-körningen.

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler][lnk-modules].

Om du har problem med Edge-körningen installeras korrekt kan du kolla den [felsökning] [ lnk-trouble] sidan.


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
[lnk-modules]: how-to-deploy-modules-portal.md
