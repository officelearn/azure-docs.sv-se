---
title: Så här installerar du Azure IoT Edge på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Linux
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: f515ddc58a9f9b434e40f44ca7cc50d738ed69f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993127"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installera Azure IoT Edge-körningen på Linux (x64)

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Linux x64 (Intel/AMD) Edge-enhet. Referera till [support för Azure IoT Edge](support.md#operating-systems) en lista över AMD64-operativsystem som stöds för närvarande. 

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft nyckeln och program lagringsplats feed

Beroende på ditt operativsystem, väljer du lämplig skripten för att förbereda din enhet för IoT Edge runtime-installation. 

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
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

Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen. 

Installationskommandot installerar också standardversionen av den **iothsmlib** om det inte redan finns.

Uppdatera apt-get.

```bash
sudo apt-get update
```

Installera daemonen säkerhet. Paketet har installerats på `/etc/iotedge/`.

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge Security Daemon

Konfigurera IoT Edge-körningen för att länka den fysiska enheten med en enhetsidentitet som finns i Azure IoT hub. 

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard måste du kanske förhöjd behörighet att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt, måste du ange den med en [enhetsanslutningssträngen] [ lnk-dcs] att du kan skapa genom att registrera en ny enhet i IoT hub.


Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen och ta bort den **manuell** Etableringsläge. Uppdatera värdet för **device_connection_string** med anslutningssträngen från din IoT Edge-enhet.

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

Spara och stäng filen. 

   `CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: Automatisk etablering

Att automatiskt etablera en enhet [konfigurera Device Provisioning-tjänsten och hämta din enhet registrerings-ID] [ lnk-dps] (DPS). Automatisk etablering fungerar bara med enheter som har ett chip för Trusted Platform Module (TPM). Till exempel levereras Raspberry Pi enheter inte med TPM som standard. 

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen och ta bort den **dps** Etableringsläge. Uppdaterar du värdet för **scope_id** och **registration_id** med värden från din IoT Hub Device Provisioning-tjänsten och IoT Edge-enhet med TPM. 

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

Spara och stäng filen. 

   `CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM-Edge-enhet på en Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrollera status för en IoT Edge-Daemon med hjälp av:

```bash
systemctl status iotedge
```

Kontrollera daemon loggar med:

```bash
journalctl -u iotedge --no-pager --no-full
```

Och listan körs moduler med:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat en runtime, logga ut från datorn och logga in för att uppdatera dina behörigheter automatiskt. Tills dess kan du använda **sudo** framför alla `iotedge` kommandona.

På resursen begränsad enheter, vi rekommenderar starkt att du ställer in den *OptimizeForPerformance* miljövariabeln till *FALSKT* enligt anvisningarna i den [felsökningsguide ][lnk-trouble].

Om ditt nätverk som har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>Nästa steg

Om du har problem med Edge-körningen installeras korrekt kan du kolla den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
