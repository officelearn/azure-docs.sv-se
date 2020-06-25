---
title: Installera Azure IoT Edge på Linux | Microsoft Docs
description: Azure IoT Edge Installationsinstruktioner på Linux-enheter som kör Ubuntu eller Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: d73f3a37bb084533733b27b49ac171747cee814c
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85321889"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Installera Azure IoT Edge-körningen på Debian-baserade Linux-system

Azure IoT Edge runtime är vad som förvandlar en enhet till en IoT Edge enhet. Körningen kan distribueras på enheter så små som Raspberry Pi eller lika stora som en industriell Server. När en enhet har kon figurer ATS med IoT Edge runtime kan du börja distribuera affärs logiken till den från molnet. Mer information finns i [förstå Azure IoT Edge Runtime och dess arkitektur](iot-edge-runtime.md).

Den här artikeln beskriver stegen för att installera Azure IoT Edge runtime på en x64-, ARM32-eller ARM64 Linux-enhet. Vi tillhandahåller installations paket för Ubuntu Server 16,04, Ubuntu Server 18,04 och Raspbian utsträckt. Se [Azure IoT Edge system som stöds](support.md#operating-systems) för en lista över operativ system och arkitekturer som stöds av Linux.

> [!NOTE]
> Paket i Linux-programdatabaserna omfattas av licens villkoren som finns i varje paket (/usr/share/doc/*-paketets namn*). Läs licens villkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte accepterar licens villkoren ska du inte använda paketet.

## <a name="install-iot-edge-and-container-runtimes"></a>Installera IoT Edge och container runtime

Använd följande avsnitt för att installera den senaste versionen av Azure IoT Edge runtime på din enhet.

>[!NOTE]
>Stöd för ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft-nyckel och lagrings plats för lagrings plats

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

Installera Microsoft GPG offentlig nyckel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Installera en körmiljö för containrar

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) container Runtime. För produktions scenarier rekommenderar vi att du använder den [Moby-baserade](https://mobyproject.org/) motorn som anges nedan. Moby-motorn är den enda behållar motorn som stöds officiellt med Azure IoT Edge. Docker CE/EE-behållar avbildningar är kompatibla med Moby-körningsmiljön.

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

Installera Moby kommando rads gränssnitt (CLI). CLI är användbart för utveckling men valfritt för produktions distributioner.

   ```bash
   sudo apt-get install moby-cli
   ```

Om du får fel när du installerar Moby container runtime följer du stegen för att [kontrol lera din Linux-kernel för Moby-kompatibilitet](#verify-your-linux-kernel-for-moby-compatibility), som tillhandahålls längre fram i den här artikeln.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Azure IoT Edge Security daemon

**IoT Edge Security daemon** tillhandahåller och upprätthåller säkerhets standarder på den IoT Edge enheten. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge Runtime.

Uppdatera paket listor på enheten.

   ```bash
   sudo apt-get update
   ```

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a iotedge
   ```

Om du vill installera den senaste versionen av Security daemon använder du följande kommando som också installerar den senaste versionen av **libiothsm-STD-** paketet:

   ```bash
   sudo apt-get install iotedge
   ```

Om du vill installera en version av säkerhets-daemonen anger du versionen från apt lista utdata. Ange också samma version för **libiothsm-STD-** paketet, vilket annars skulle installera den senaste versionen. Följande kommando installerar till exempel den senaste versionen av 1.0.8-versionen:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Om den version som du vill installera inte visas följer du stegen i [Installera runtime med hjälp av release assets](#install-runtime-using-release-assets). I avsnittet visas hur du riktar in dig på en tidigare version av IoT Edge Security daemon eller Release Candidate-versioner.

När IoT Edge har installerats på `/etc/iotedge/` , kommer utdata att bli ombedd att uppdatera konfigurations filen. Fortsätt till nästa avsnitt för att slutföra enhets etableringen.

## <a name="configure-the-security-daemon"></a>Konfigurera Security daemon

Konfigurera IoT Edge runtime för att länka din fysiska enhet med en enhets identitet som finns i en Azure IoT-hubb.

Daemonen kan konfigureras med hjälp av konfigurations filen på `/etc/iotedge/config.yaml` . Filen är skrivskyddad som standard. du kan behöva utökade behörigheter för att redigera den.

En enskild IoT Edge enhet kan tillhandahållas manuellt med hjälp av en enhets anslutnings sträng från IoT Hub. Du kan också använda enhets etablerings tjänsten för att etablera enheter automatiskt, vilket är användbart när du har många enheter att etablera. Välj lämpligt installations skript beroende på ditt etablerings alternativ.

### <a name="option-1-manual-provisioning"></a>Alternativ 1: manuell etablering

Om du vill etablera en enhet manuellt måste du tillhandahålla en [enhets anslutnings sträng](how-to-register-device.md#register-in-the-azure-portal) som du kan skapa genom att registrera en ny enhet i IoT-hubben.

Öppna konfigurations filen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till det **manuella etablerings konfigurations** avsnittet. Uppdatera värdet för **device_connection_string** med anslutnings strängen från IoT Edge enheten. Se till att alla andra etablerings avsnitt är kommenterade. Se till att **etableringen:** raden inte har några föregående blank steg och att kapslade objekt är indragna med två blank steg.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: automatisk etablering

IoT Edge enheter kan tillhandahållas automatiskt med [Azure-IoT Hub Device Provisioning service (DPS)](../iot-dps/index.yml). För närvarande har IoT Edge stöd för tre attesterings metoder när du använder automatisk etablering, men maskin varu kraven kan påverka dina val. Till exempel levereras inte Raspberry Pi-enheter med ett Trusted Platform Module-chip (TPM) som standard. Mer information finns i följande artiklar:

* [Skapa och etablera en IoT Edge enhet med en virtuell TPM på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md)
* [Skapa och etablera en IoT Edge-enhet med X. 509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera en IoT Edge enhet med hjälp av symmetrisk nyckel attestering](how-to-auto-provision-symmetric-keys.md)

De här artiklarna vägleder dig genom att ställa in registreringar i DPS och att generera rätt certifikat eller nycklar för attestering. Oavsett vilken mekanism för attestering du väljer läggs etablerings informationen till i IoT Edge konfigurations filen på IoT Edges enheten.

Öppna konfigurations filen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etablerings konfigurationerna för filen och ta bort kommentaren till det avsnitt som är lämpligt för din mekanism för attestering. Se till att alla andra etablerings avsnitt är kommenterade. **Etableringen:** raden får inte ha något föregående blank steg, och kapslade objekt bör dras av två blank steg. Uppdatera värdet för **scope_id** med värdet från din IoT Hub Device Provisioning service-instans och ange lämpliga värden för attesterings fälten.

TPM-attestering:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X. 509-attestering:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

Attestering av symmetrisk nyckel:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Klistra in innehållet i Urklipp i nano `Shift+Right Click` eller tryck på `Shift+Insert` .

Spara och stäng filen. `CTRL + X`, `Y`, `Enter`

När du har angett etablerings informationen i konfigurations filen startar du om daemonen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om du använde de **manuella konfigurations** stegen i föregående avsnitt, ska IoT Edge runtime vara etablerade och köras på enheten. Om du använde de **automatiska konfigurations** stegen måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg finns i [skapa och etablera en simulerad TPM IoT Edge-enhet på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrol lera status för IoT Edge daemon:

```bash
systemctl status iotedge
```

Granska daemon-loggar:

```bash
journalctl -u iotedge --no-pager --no-full
```

Kör [fel söknings verktyget](troubleshoot.md#run-the-check-command) för att kontrol lera de vanligaste konfigurations-och nätverks felen:

```bash
sudo iotedge check
```

Innan du distribuerar din första modul till IoT Edge på enheten, distribueras inte **$edgeHub** system-modulen till enheten. Det innebär att den automatiska kontrollen returnerar ett fel för `Edge Hub can bind to ports on host` anslutnings kontrollen. Felet kan ignoreras om det inte inträffar när du har distribuerat en modul till enheten.

Slutligen visar du en lista med moduler som körs:

```bash
sudo iotedge list
```

När du har installerat IoT Edge på enheten, är den enda modul som du bör se att köra **edgeAgent**. När du har skapat din första distribution startar den andra systemmodulen **$edgeHub** på enheten också. Mer information finns i [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Tips och felsökning

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat körningen loggar du ut från datorn och loggar in igen för att uppdatera dina behörigheter automatiskt. Tills dess använder du **sudo** framför alla `iotedge` kommandon.

På resurs begränsade enheter rekommenderar vi starkt att du ställer in miljövariabeln *OptimizeForPerformance* på *falskt* enligt anvisningarna i [fel söknings guiden](troubleshoot.md).

Om nätverket har en proxyserver följer du stegen i [Konfigurera din IoT Edge enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifiera din Linux-kernel för Moby-kompatibilitet

Många inbäddade enhets tillverkare levererar enhets avbildningar som innehåller anpassade Linux-Kernels utan de funktioner som krävs för container runtime-kompatibilitet. Om du stöter på problem när du installerar den rekommenderade Moby container runtime kan du felsöka din Linux-kernel-konfiguration med hjälp av skriptet för att [kontrol lera konfiguration](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) från den officiella [Moby GitHub-lagringsplatsen](https://github.com/moby/moby). Kör följande kommandon på enheten för att kontrol lera kernel-konfigurationen:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Det här kommandot innehåller en detaljerad utmatning som innehåller statusen för de kernel-funktioner som används av Moby-körningsmiljön. Du bör se till att alla objekt under `Generally Necessary` och `Network Drivers` är aktiverade för att säkerställa att din kernel är helt kompatibel med Moby-körningsmiljön.  Om du har identifierat vilka funktioner som saknas kan du aktivera dem genom att återskapa din kernel från källa och välja de associerade modulerna som ska ingå i den aktuella kernel. config.  På samma sätt kan du, om du använder en kernel-konfigurations generator som `defconfig` eller `menuconfig` , hitta och aktivera respektive funktioner och återskapa din kernel enligt detta.  När du har distribuerat den nyligen ändrade kärnan kör du check config-skriptet igen för att kontrol lera att alla nödvändiga funktioner har Aktiver ATS.

## <a name="install-runtime-using-release-assets"></a>Installera runtime med hjälp av release-tillgångar

Använd stegen i det här avsnittet om du vill installera en viss version av Moby och Azure IoT Edge runtime som inte är tillgänglig via `apt-get install` . Listan med Microsoft-paket innehåller bara en begränsad uppsättning av de senaste versionerna och deras under versioner, så dessa steg är för alla som vill installera en äldre version eller en version av en version av versionen.

Med hjälp av ett spiral kommando kan du rikta in komponentfilerna direkt från IoT Edge GitHub-lagringsplatsen. Använd följande steg för att hämta alla IoT Edge-komponenter på enheten: Moby-motorn och CLI, libiothsm och slutligen IoT Edge Security daemon.

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

När IoT Edge har installerats på `/etc/iotedge` , kommer utdata att bli ombedd att uppdatera konfigurations filen. Följ stegen i avsnittet [Konfigurera säkerhets daemon](#configure-the-security-daemon) för att slutföra enhets etableringen.

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Linux-enhet använder du följande kommandon från kommando raden.

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge runtime tas bort stoppas de behållare som den skapade, men de finns kvar på enheten. Visa alla behållare för att se vilka som är kvar.

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

Nu när du har en IoT Edge enhet som har installerats med körnings miljön kan du [distribuera IoT Edge moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge runtime-installationen kan du kolla in [fel söknings](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge Security daemon och runtime](how-to-update-iot-edge.md).
