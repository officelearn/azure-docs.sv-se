---
title: Så här installerar du Azure IoT Edge på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: kgremban
ms.openlocfilehash: 5cd12d4fab97f295cad1e0ea06112fc53e376b12
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055415"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installera Azure IoT Edge-körningen på Linux (x64)

Azure IoT Edge-körningen distribueras på alla IoT Edge-enheter. Den har tre komponenter. Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta IoT Edge-agenten. Den **IoT Edge-agenten** underlättar distribution och övervakning av moduler på Edge-enheter, inklusive IoT Edge hub. **IoT Edge-hubben** hanterar kommunikationen mellan moduler på IoT Edge-enheten, samt mellan enheten och IoT Hub.

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Linux x64 (Intel/AMD) Edge-enhet.

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft nyckeln och program lagringsplats feed

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

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>Installera runtime behållare 

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] behållare runtime. I produktionsscenarier rekommenderas du använder den [Moby-baserade] [ lnk-moby] motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby runtime.

Uppdatera apt-get.

```bash
sudo apt-get update
```

Installera Moby-motorn. 

```bash
sudo apt-get install moby-engine
```

Installera Moby kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Daemon för Azure IoT Edge-säkerhet

Kommandona nedan installeras även standardversionen av den **iothsmlib** om det inte redan finns.

```bash
sudo apt-get update
sudo apt-get install iotedge
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

>[!TIP]
>Du behöver ha förhöjd behörighet att köra `iotedge` kommandon. När du logga ut från datorn och logga in första gången när du har installerat IoT Edge-körningen kan uppdateras automatiskt dina behörigheter. Tills dess kan du använda **sudo** framför kommandona. 

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

Om du har problem med Edge-körningen installeras korrekt kan du kolla den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
