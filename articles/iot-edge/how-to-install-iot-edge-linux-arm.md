---
title: Installera Azure IoT Edge på Linux-ARM32 | Microsoft Docs
description: Azure IoT Edge installations anvisningar för Linux på ARM32-enheter, t. ex. en Raspberry PI
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224695"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Installera Azure IoT Edge runtime på Linux (ARM32v7/armhf)

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. 

Läs mer om hur IoT Edge-körningen fungerar och vilka komponenter som ingår i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

I den här artikeln beskrivs stegen för att installera Azure IoT Edge runtime på en Linux-ARM32v7/armhf IoT Edge-enhet. Dessa steg kan till exempel användas för Raspberry Pi-enheter. En lista över ARM32 operativ system som stöds finns i [Azure IoT Edge system som stöds](support.md#operating-systems). 

>[!NOTE]
>Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-latest-version"></a>Installera den senaste versionen

Använd följande avsnitt för att installera den senaste versionen av tjänsten Azure IoT Edge på dina Linux ARM-enheter. 

### <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container Runtime. I produktionsscenarier rekommenderas du använder den [Moby-baserade](https://mobyproject.org/) motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållar avbildningar är kompatibla med den Moby-baserade körningen.

Följande kommandon installerar både den Moby-baserade motorn och kommando rads gränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

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

### <a name="install-the-iot-edge-security-daemon"></a>Installera IoT Edge Security daemon

**IoT Edge Security daemon** tillhandahåller och upprätthåller säkerhets standarder på den IoT Edge enheten. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen. 


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

När IoT Edge har installerats uppmanas du att uppdatera konfigurations filen. Följ stegen i avsnittet [Konfigurera säkerhets demon för Azure IoT Edge](#configure-the-azure-iot-edge-security-daemon) för att slutföra etableringen av enheten. 

## <a name="install-a-specific-version"></a>Installera en enskild version

Om du vill installera en speciell version av Azure IoT Edge kan du rikta in komponentfilerna direkt från IoT Edge GitHub-lagringsplatsen. Använd samma `curl` kommandon som listas i föregående avsnitt för att hämta alla IoT Edge-komponenter till din enhet: Moby-motorn och CLI, libiothsm och slutligen IoT Edge Security daemon. Den enda skillnaden är att du ersätter **aka.MS** -URL: er med länkar som pekar direkt till versionen av varje komponent som du vill använda.

Gå till [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases)och leta upp den version som du vill använda som mål. Expandera avsnittet **till gångar** för versionen och välj de filer som matchar IoT Edge enhetens arkitektur. Alla IoT Edge-versioner innehåller **iotedge** -och **libiothsm** -filer. Alla versioner omfattar inte **Moby-Engine** eller **Moby-CLI**. Om du inte redan har installerat Moby container Engine kan du titta igenom de äldre versionerna tills du hittar en som omfattar Moby-komponenterna. 

När IoT Edge har installerats uppmanas du att uppdatera konfigurations filen. Följ stegen i nästa avsnitt för att slutföra etableringen av enheten. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge Security daemon

Konfigurera IoT Edge-körningen för att länka den fysiska enheten med en enhetsidentitet som finns i Azure IoT hub. 

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard, så du kan behöva utökade behörigheter för att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet. 

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt måste du tillhandahålla en enhets [anslutnings sträng](how-to-register-device-portal.md) som du kan skapa genom att registrera en ny IoT Edge enhet i IoT-hubben.

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till det **manuella etablerings konfigurations** avsnittet. Uppdatera värdet för **device_connection_string** med anslutningssträngen från din IoT Edge-enhet. Se till att alla andra etablerings avsnitt är kommenterade.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Spara och stäng filen. 

`CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: Automatisk etablering

Att automatiskt etablera en enhet [konfigurera Device Provisioning-tjänsten och hämta din enhet registrerings-ID](how-to-auto-provision-simulated-device-linux.md). Det finns ett antal attesterings metoder som stöds av IoT Edge när du använder automatisk etablering men maskin varu kraven påverkar också dina val. Till exempel levereras inte Raspberry Pi-enheter med ett Trusted Platform Module-chip (TPM) som standard.

Öppna konfigurationsfilen. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till det avsnitt som är lämpligt för din mekanism för attestering. När du använder TPM-attestering kan du till exempel uppdatera värdena för **scope_id** och **registration_id** med värdena från din IoT Hub Device Provisioning-tjänst och din IoT Edge-enhet med TPM.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Spara och stäng filen. 

`CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Eller, om du använde de **automatiska konfigurations** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg finns i [skapa och etablera en simulerad TPM IoT Edge-enhet på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

På resursen begränsad enheter, vi rekommenderar starkt att du ställer in den *OptimizeForPerformance* miljövariabeln till *FALSKT* enligt anvisningarna i den [felsökningsguide ](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Om ditt nätverk som har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Linux-enhet använder du följande kommandon från kommando raden. 

Ta bort IoT Edge-körningen. 

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge runtime tas bort stoppas den behållare som den skapade men finns fortfarande på enheten. Visa alla behållare för att se vilka som är kvar. 

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, inklusive de två körnings behållarna. 

```bash
sudo docker rm -f <container name>
```

Slutligen tar du bort behållar körningen från enheten. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att IoT Edge runtime-installationen korrekt, se sidan [fel sökning](troubleshoot.md#stability-issues-on-resource-constrained-devices) .

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
