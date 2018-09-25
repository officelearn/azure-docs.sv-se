---
title: Installera Azure IoT Edge på Linux ARM32 | Microsoft Docs
description: Azure IoT Edge instruktioner för installation på Linux på ARM32 enheter, till exempel en Raspberry PI
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 9007ee95dc25854a55006284ce6fc574fd8e7968
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948527"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på en Linux ARM32v7/armhf Edge-enhet. De här stegen skulle till exempel fungerar för Raspberry Pi-enheter. Referera till [support för Azure IoT Edge](support.md#operating-systems) en lista över ARM32 operativsystem som stöds för närvarande. 

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibla] [ lnk-oci] behållare runtime. I produktionsscenarier rekommenderas du använder den [Moby-baserade] [ lnk-moby] motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby-baserade-runtime.

Kommandona nedan installera både Moby-baserad motor och kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

```bash

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

Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="connect-your-device-to-an-iot-hub"></a>Anslut enheten till en IoT-hubb 

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

Att automatiskt etablera en enhet [konfigurera Device Provisioning-tjänsten och hämta din enhet registrerings-ID][lnk-dps]. Automatisk etablering fungerar bara med enheter som har ett chip för Trusted Platform Module (TPM). Till exempel levereras Raspberry Pi enheter inte med TPM som standard. 

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen och ta bort den **dps** Etableringsläge. Uppdaterar du värdet för **scope_id** och **registration_id** med värden från IoT Hub Device Provisioning-tjänsten och IoT Edge-enhet med TPM. 

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

Om du har problem med Edge-körningen installeras korrekt, ser du den [felsökning] [ lnk-trouble] sidan.

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
