---
title: Så här installerar du Azure IoT kanten på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT kanten på Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036740"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installera Azure IoT kant runtime på Linux (x64)

Azure IoT kant runtime distribueras på alla kant för IoT-enheter. Den har tre komponenter. Den **IoT Edge security daemon** ger och underhåller säkerhetskrav på gränsenheten. Daemon startas på varje start och startar enheten genom att starta IoT kant-agenten. Den **IoT kant agent** underlättar distribution och övervakning av moduler på Edge-enheten, inklusive kant för IoT-hubben. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln innehåller steg för att installera Azure IoT kant-körningsmiljön på din Linux x64 (Intel/AMD) Edge-enhet.

>[!NOTE]
>Paket i databaser för Linux-programvaran regleras av licensvillkoren finns i varje paket (/ usr/dela/doc/*paketnamn*). Läs igenom licensvillkoren innan du använder paketet. Din installation och användning av paketet som utgör ditt godkännande av villkoren. Om du inte godkänner licensvillkoren, Använd inte paketet.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft nyckeln och program databasen feed

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>Installera behållaren runtime 

Azure IoT-Edge förlitar sig på en [OCI-kompatibel] [ lnk-oci] behållare runtime (t.ex. Docker). Om du redan har Docker CE/EE installerat på enheten kant kan du fortsätta att använda den för utveckling och testning med Azure IoT kant. 

Scenarier för produktion, rekommenderas du använder den [Moby-baserade] [ lnk-moby] motorn nedan. Det är bara behållare motorn officiellt stöds med Azure IoT kant. Docker CE/EE behållaren bilder är helt kompatibel med Moby körningsmiljön.

*Anvisningarna nedan installera både moby motor och kommandoradsgränssnittet (CLI). CLI är användbart för utveckling, men är valfritt för Produktionsdistribution.*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Azure IoT-Edge Security Daemon

Kommandona nedan installerar standardversionen av den **iothsmlib** om den inte redan finns.

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
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
iotedge list
```

## <a name="next-steps"></a>Nästa steg

Om du har problem med kant-körningsmiljön installeras korrekt, checka ut den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
