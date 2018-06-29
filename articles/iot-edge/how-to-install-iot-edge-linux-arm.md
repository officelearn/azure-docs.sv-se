---
title: Så här installerar du Azure IoT kanten på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT kanten på Linux på ARM32
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 2f25faa6957f9a8a42e64829e62506ae2add13ae
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096166"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installera Azure IoT kant runtime på Linux (ARM32v7/armhf)

Azure IoT kant runtime distribueras på alla kant för IoT-enheter. Den har tre komponenter. Den **IoT Edge security daemon** ger och underhåller säkerhetskrav på gränsenheten. Daemon startas på varje start och startar enheten genom att starta IoT kant-agenten. Den **IoT kant agent** underlättar distribution och övervakning av moduler på Edge-enheten, inklusive kant för IoT-hubben. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln innehåller steg för att installera Azure IoT kant körning på en Linux ARM32v7/armhf Edge-enhet (till exempel hallon Pi).

>[!NOTE]
>Paket i databaser för Linux-programvaran regleras av licensvillkoren finns i varje paket (/ usr/dela/doc/*paketnamn*). Läs igenom licensvillkoren innan du använder paketet. Din installation och användning av paketet som utgör ditt godkännande av villkoren. Om du inte godkänner licensvillkoren, Använd inte paketet.

## <a name="install-the-container-runtime"></a>Installera behållaren runtime

Azure IoT-Edge förlitar sig på en [OCI-kompatibel] [ lnk-oci] behållare runtime (t.ex. Docker). Om du redan har Docker CE/EE installerat på enheten kant kan du fortsätta att använda den för utveckling och testning med Azure IoT kant. 

Scenarier för produktion, rekommenderas du använder den [Moby-baserade] [ lnk-moby] motorn nedan. Det är bara behållare motorn officiellt stöds med Azure IoT kant. Docker CE/EE behållaren bilder är helt kompatibel med Moby körningsmiljön.

Kommandona nedan installera både moby motor och kommandoradsgränssnittet (CLI). CLI är användbart för utveckling, men är valfritt för Produktionsdistribution.

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

## <a name="install-the-iot-edge-security-daemon"></a>Installera Daemon för IoT kant-säkerhet

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

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT-Edge Security-Daemon

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml` gränsenheten kan konfigureras <!--[automatically via Device Provisioning Service][lnk-dps] or--> manuellt med hjälp av en [enheten anslutningssträngen][lnk-dcs].

För manuell konfiguration, anger du enheten anslutningssträngen i **etablering** avsnitt i **config.yaml**

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*Filen är skrivskyddad som standard kan du behöva använda `sudo` redigera den. Till exempel `sudo nano /etc/iotedge/config.yaml`*

Starta om daemon när du har angett etableringsinformationen i konfigurationen:

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Kontrollera att installationen lyckades

Du kan kontrollera status för IoT kant Daemon med:

```cmd/sh
systemctl status iotedge
```

Kontrollera daemon loggar med:

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Och lista med moduler med:

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med kant-körningsmiljön installeras korrekt, checka ut den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md