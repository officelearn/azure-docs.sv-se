---
title: Hur du installerar Azure IoT Edge på Windows med Linux-behållare | Microsoft Docs
description: Azure IoT Edge instruktioner för installation på Windows med Linux-behållare
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: kgremban
ms.openlocfilehash: ea576c0d434d4db7077fc41bc1f5bbbc89e7779e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576655"
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

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] runtime behållare (till exempel Docker). 

Du kan använda [Docker för Windows] [ lnk-docker-for-windows] för utveckling och testning. Konfigurera Docker för Windows [att använda Linux-behållare][lnk-docker-config]

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
   Install-SecurityDaemon -Manual -ContainerOs Linux
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
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. När du uppmanas, anger den **ScopeID** och **RegistrationID** för din etableringstjänst och en enhet.

## <a name="verify-successful-installation"></a>Verifiera installationen

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
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Och listan körs moduler med:

```powershell
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler][lnk-modules].

Om du har problem med Edge-körningen installeras korrekt kan du kolla den [felsökning] [ lnk-trouble] sidan.


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
[lnk-modules]: how-to-deploy-modules-portal.md
