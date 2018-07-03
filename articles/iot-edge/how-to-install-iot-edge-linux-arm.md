---
title: Så här installerar du Azure IoT Edge på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Linux på ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 320eef05faec6b3e399e5a1289e3ee076823c921
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346322"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)

Azure IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta IoT Edge-agenten. Den **IoT Edge-agenten** underlättar distribution och övervakning av moduler på Edge-enheter, inklusive IoT Edge hub. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på en Linux ARM32v7/armhf Edge-enhet (till exempel Raspberry Pi).

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] container-körning (t.ex. Docker). Om du redan har Docker CE/EE installerad på din Edge-enhet kan fortsätta du att använda den för utveckling och testning med Azure IoT Edge. 

I produktionsscenarier rekommenderas du använder den [Moby-baserade] [ lnk-moby] motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är helt kompatibla med Moby runtime.

Kommandona nedan installera både moby-motorn och kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>Installera Daemon för IoT Edge-säkerhet

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge Security Daemon


Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard måste du kanske förhöjd behörighet att redigera den.

```bash
sudo nano /etc/iotedge/config.yaml
```

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

Starta om daemon när du har angett etableringsinformationen i konfigurationen:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM-Edge-enhet på en Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrollera status för en IoT Edge-Daemon med hjälp av:

```cmd/sh
systemctl status iotedge
```

Kontrollera daemon loggar med:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Och listan körs moduler med:

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med Edge-körningen installeras korrekt, Kolla in den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md