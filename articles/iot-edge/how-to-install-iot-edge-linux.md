---
title: Installera Azure IoT Edge på Linux | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner på Linux-enheter som kör Ubuntu eller Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: 7cd0935177ad4070750a9b2a0ff129af2e13959f
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772422"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Installera Azure IoT Edge runtime på Debian-baserade Linux-system

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln beskriver stegen för att installera Azure IoT Edge runtime på en x64-, ARM32-eller ARM64 Linux-enhet. Installations paket tillhandahålls för Ubuntu Server 16,04, Ubuntu Server 18,04 och Raspbian utsträckt. Se [Azure IoT Edge system som stöds](support.md#operating-systems) för en lista över operativ system och arkitekturer som stöds av Linux.

>[!NOTE]
>Stöd för ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-latest-runtime-version"></a>Installera den senaste körnings versionen

Använd följande avsnitt för att installera den senaste versionen av Azure IoT Edge runtime på din enhet. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft nyckeln och program lagringsplats feed

Förbered enheten för IoT Edge runtime-installationen.

Installera konfiguration av lagrings plats. Välj det **16,04** -eller **18,04** -kommando som matchar enhetens operativ system:

* **Ubuntu Server 16,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian-storlek**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopiera den genererade listan.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installera offentlig nyckel för Microsoft GPG

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container Runtime. För produktions scenarier rekommenderar vi att du använder den [Moby-baserade](https://mobyproject.org/) motorn som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby runtime.

Utför apt-uppdatering.

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

Om du stöter på fel när du installerar Moby container runtime följer du stegen för att [kontrol lera din Linux-kernel för Moby-kompatibilitet](#verify-your-linux-kernel-for-moby-compatibility), som beskrivs längre fram i den här artikeln. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Daemon för Azure IoT Edge-säkerhet

**IoT Edge Security daemon** tillhandahåller och upprätthåller säkerhets standarder på den IoT Edge enheten. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen.

Installations kommandot installerar även standard versionen av **libiothsm** om den inte redan finns.

Utför apt-uppdatering.

   ```bash
   sudo apt-get update
   ```

Installera daemonen säkerhet. Paketet har installerats på `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurations filen. Följ stegen i avsnittet [Konfigurera säkerhets demon](#configure-the-security-daemon) för att slutföra etableringen av enheten. 

## <a name="install-a-specific-runtime-version"></a>Installera en viss körnings version

Om du vill installera en viss version av Moby och Azure IoT Edge runtime i stället för att använda de senaste versionerna kan du rikta in komponentfilerna direkt från IoT Edge GitHub-lagringsplatsen. Använd följande steg för att hämta alla IoT Edge-komponenter på enheten: Moby-motorn och CLI, libiothsm och slutligen IoT Edge Security daemon. Gå vidare till nästa avsnitt, [Konfigurera Security daemon](#configure-the-security-daemon), om du inte vill ändra till en viss körnings version.

1. Gå till [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases)och leta upp den version som du vill använda som mål. 

2. Expandera avsnittet **till gångar** för den versionen.

3. Det kan hända att det inte finns uppdateringar av Moby-motorn i en specifik version. Om du ser filer som börjar med **Moby-Engine** och **Moby-CLI**använder du följande kommandon för att uppdatera komponenterna. Om du inte ser några Moby-filer går du tillbaka genom de äldre versions resurserna tills du hittar den senaste versionen. 

   1. Hitta den **Moby** som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen.

   2. Använd den kopierade länken i följande kommando för att installera den versionen av Moby-motorn: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Hitta den **Moby-CLI-** fil som matchar IoT Edge enhetens arkitektur. Moby CLI är en valfri komponent, men kan vara till hjälp under utvecklingen. Högerklicka på fil länken och Kopiera länk adressen. 

   4. Använd den kopierade länken i följande kommando för att installera den versionen av Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Varje utgåva bör ha nya filer för IoT Edge Security daemon och hsmlib. Använd följande kommandon för att uppdatera dessa komponenter. 

   1. Hitta filen **libiothsm-STD** som matchar IoT Edge enhetens arkitektur. Högerklicka på fil länken och Kopiera länk adressen. 

   2. Använd den kopierade länken i följande kommando för att installera den versionen av hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Hitta den **iotedge** -fil som matchar din IoT Edge enhets arkitektur. Högerklicka på fil länken och Kopiera länk adressen. 

   4. Använd den kopierade länken i följande kommando för att installera den versionen av IoT Edge Security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurations filen. Följ stegen i nästa avsnitt för att slutföra etableringen av enheten. 

## <a name="configure-the-security-daemon"></a>Konfigurera Security daemon

Konfigurera IoT Edge-körningen för att länka den fysiska enheten med en enhetsidentitet som finns i Azure IoT hub.

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard måste du kanske förhöjd behörighet att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet.

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt, måste du ange den med en [enhetsanslutningssträngen](how-to-register-device.md#register-in-the-azure-portal) att du kan skapa genom att registrera en ny enhet i IoT hub.

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
Om du vill klistra in innehållet i Urklipp i nano `Shift+Right Click` eller trycker du på `Shift+Insert`.

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

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till det avsnitt som är lämpligt för din mekanism för attestering. När du använder TPM-attestering kan du till exempel uppdatera värdena för **scope_id** och **registration_id** med värdena från din IoT Hub Device Provisioning-tjänst och din IoT Edge enhet med TPM.

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

Om du vill klistra in innehållet i Urklipp i nano `Shift+Right Click` eller trycker du på `Shift+Insert`.

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg finns i [skapa och etablera en simulerad TPM IoT Edge-enhet på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrol lera status för IoT Edge daemon:

```bash
systemctl status iotedge
```

Granska daemon-loggar:

```bash
journalctl -u iotedge --no-pager --no-full
```

Kör en automatisk kontroll av de vanligaste konfigurations-och nätverks felen: 

```bash
sudo iotedge check
```

Innan du distribuerar din första modul till IoT Edge på enheten, distribueras inte **$edgeHub** system-modulen till enheten. Det innebär att den automatiska kontrollen returnerar ett fel för kontrollen `Edge Hub can bind to ports on host` anslutning. Det här felet kan vara ingored om det inte inträffar när du har distribuerat en modul till enheten.

Slutligen visar du en lista med moduler som körs:

```bash
sudo iotedge list
```

När du har installerat IoT Edge på enheten, är den enda modul som du bör se att köra **edgeAgent**. När du har skapat din första distribution startar den andra systemmodulen **$edgeHub** på enheten också. Mer information finns i [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Felsökning och tips

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat en runtime, logga ut från datorn och logga in för att uppdatera dina behörigheter automatiskt. Tills dess kan du använda **sudo** framför alla `iotedge` kommandona.

På resursen begränsad enheter, vi rekommenderar starkt att du ställer in den *OptimizeForPerformance* miljövariabeln till *FALSKT* enligt anvisningarna i den [felsökningsguide ](troubleshoot.md).

Om ditt nätverk som har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifiera din Linux-kernel för Moby-kompatibilitet

Många inbäddade enhets tillverkare levererar enhets avbildningar som innehåller anpassade Linux-Kernels utan de funktioner som krävs för container runtime-kompatibilitet. Om du stöter på problem när du installerar den rekommenderade Moby container runtime kan du felsöka din Linux-kernel-konfiguration med hjälp av skriptet för att [kontrol lera konfiguration](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) från den officiella [Moby GitHub-lagringsplatsen](https://github.com/moby/moby). Kör följande kommandon på enheten för att kontrol lera kernel-konfigurationen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Detta ger en detaljerad utmatning som innehåller statusen för de kernel-funktioner som används av Moby-körningsmiljön. Du bör se till att alla objekt under `Generally Necessary` och `Network Drivers` är aktiverade för att säkerställa att din kernel är helt kompatibel med Moby-körningsmiljön.  Om du har identifierat vilka funktioner som saknas kan du aktivera dem genom att återskapa din kernel från källa och välja de associerade modulerna som ska ingå i den aktuella kernel. config.  Om du använder en kernel-konfigurations generator som defconfig eller menuconfig, kan du söka efter och aktivera respektive funktioner och återskapa din kernel på motsvarande sätt.  När du har distribuerat den nyligen ändrade kärnan kör du check config-skriptet igen för att kontrol lera att alla nödvändiga funktioner har Aktiver ATS.


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

Om du har problem med IoT Edge runtime-installationen kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
