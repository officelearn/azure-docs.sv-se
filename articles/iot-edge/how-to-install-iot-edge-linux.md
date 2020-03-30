---
title: Installera Azure IoT Edge på Linux | Microsoft-dokument
description: Installationsinstruktioner för Azure IoT Edge på Linux-enheter som kör Ubuntu eller Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: kgremban
ms.openlocfilehash: fb86ee9ce956917f8da44146e58a4775e0ba639f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535909"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Installera Azure IoT Edge-körningen på Debian-baserade Linux-system

Azure IoT Edge-körningen är det som förvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter så små som en Raspberry Pi eller så stor som en industriell server. När en enhet har konfigurerats med IoT Edge-körningen kan du börja distribuera affärslogik till den från molnet. Mer information finns [i Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

I den här artikeln visas stegen för att installera Azure IoT Edge-körningen på en X64-, ARM32- eller ARM64 Linux-enhet. Vi tillhandahåller installationspaket för Ubuntu Server 16.04, Ubuntu Server 18.04 och Raspbian Stretch. Se [Azure IoT Edge-stödda system](support.md#operating-systems) för en lista över linux-operativsystem och arkitekturer som stöds.

> [!NOTE]
> Paket i Linux-programvarudatabaserna omfattas av licensvillkoren i varje paket (/usr/share/doc/package-name).*package-name* Läs licensvillkoren innan du använder paketet. Din installation och användning av paketet utgör ditt godkännande av dessa villkor. Om du inte godkänner licensvillkoren ska du inte använda paketet.

## <a name="install-the-latest-runtime-version"></a>Installera den senaste runtime-versionen

Använd följande avsnitt för att installera den senaste versionen av Azure IoT Edge-körningen på din enhet.

>[!NOTE]
>Stöd för ARM64-enheter finns i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsofts nyckel- och programvarudatabasfeed

Förbered enheten för installationen av IoT Edge-körningen.

Installera databaskonfigurationen. Välj kommandot **16.04** eller **18.04** som matchar enhetens operativsystem:

* **Ubuntu Server 16,04:**

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopiera den genererade listan.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installera microsoft GPG-allmän nyckel

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installera behållarkörningen

Azure IoT Edge förlitar sig på en [OCI-kompatibel](https://www.opencontainers.org/) containerkörning. För produktionsscenarier rekommenderar vi att du använder den [Moby-baserade](https://mobyproject.org/) motorn nedan. Moby-motorn är den enda behållarmotorn som officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby-körningen.

Uppdatera paketlistor på enheten.

   ```bash
   sudo apt-get update
   ```

Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

Installera Moby-kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfritt för produktionsdistributioner.

   ```bash
   sudo apt-get install moby-cli
   ```

Om du får fel när du installerar Moby-behållarens körning följer du stegen för att [verifiera din Linux-kärna för Moby-kompatibilitet](#verify-your-linux-kernel-for-moby-compatibility), som beskrivs senare i den här artikeln.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Säkerhetsdemonen Azure IoT Edge

**Säkerhetsdemonen IoT Edge** tillhandahåller och upprätthåller säkerhetsstandarder på IoT Edge-enheten. Demonen startar på varje känga och bootstraps enheten genom att starta resten av IoT Edge runtime.

Installationskommandot installerar också standardversionen av **libiothsm** om den inte redan finns.

Uppdatera paketlistor på enheten.

   ```bash
   sudo apt-get update
   ```

Installera säkerhetsdemonen. Paketet installeras på `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen. Följ stegen i avsnittet [Konfigurera säkerhetsdemon](#configure-the-security-daemon) för att slutföra enhetsetablering.

## <a name="install-a-specific-runtime-version"></a>Installera en specifik runtime-version

Om du vill installera en specifik version av Moby och Azure IoT Edge-körningen i stället för att använda de senaste versionerna kan du rikta komponentfilerna direkt från IoT Edge GitHub-databasen. Följ följande steg för att få alla IoT Edge-komponenter på din enhet: Moby-motorn och CLI, libiothsm och slutligen säkerhetsdemonen IoT Edge. Gå till nästa avsnitt, [Konfigurera säkerhetsdemonen](#configure-the-security-daemon), om du inte vill ändra till en viss körningsversion.

1. Navigera till [Azure IoT Edge-versionerna](https://github.com/Azure/azure-iotedge/releases)och hitta den versionsversion som du vill rikta in dig på.

2. Expandera avsnittet **Tillgångar** för den versionen.

3. Det kan finnas uppdateringar av Moby-motorn i en given utgåva. Om du ser filer som börjar med **moby-motor** och **moby-cli**använder du följande kommandon för att uppdatera dessa komponenter. Om du inte ser några Moby-filer går du tillbaka till de äldre utgivningstillgångarna tills du hittar den senaste versionen.

   1. Hitta **moby-engine-filen** som matchar din IoT Edge-enhets arkitektur. Högerklicka på fillänken och kopiera länkadressen.

   2. Använd den kopierade länken i följande kommando för att installera den versionen av Moby-motorn:

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Hitta **moby-cli-filen** som matchar din IoT Edge-enhets arkitektur. Moby CLI är en valfri komponent, men kan vara till hjälp under utvecklingen. Högerklicka på fillänken och kopiera länkadressen.

   4. Använd den kopierade länken i följande kommando för att installera den versionen av Moby CLI:

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Varje utgåva bör ha nya filer för IoT Edge säkerhetsdemon och hsmlib. Använd följande kommandon för att uppdatera dessa komponenter.

   1. Hitta **libiothsm-std-filen** som matchar din IoT Edge-enhets arkitektur. Högerklicka på fillänken och kopiera länkadressen.

   2. Använd den kopierade länken i följande kommando för att installera den versionen av hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Hitta **den iotedge-fil** som matchar IoT Edge-enhetens arkitektur. Högerklicka på fillänken och kopiera länkadressen.

   4. Använd den kopierade länken i följande kommando för att installera den versionen av säkerhetsdemonen IoT Edge.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen. Följ stegen i nästa avsnitt för att slutföra enhetsetablering.

## <a name="configure-the-security-daemon"></a>Konfigurera säkerhetsdemonen

Konfigurera IoT Edge-körningen för att länka din fysiska enhet med en enhetsidentitet som finns i en Azure IoT-hubb.

Demonen kan konfigureras med hjälp av `/etc/iotedge/config.yaml`konfigurationsfilen på . Filen är skrivskyddad som standard, du kan behöva förhöjda behörigheter för att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en enhetsanslutningssträng som tillhandahålls av IoT Hub. Du kan också använda tjänsten Enhetsetablering för att automatiskt etablera enheter, vilket är användbart när du har många enheter att etablera. Välj lämpligt installationsskript beroende på vilket etableringsval du vill.

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt måste du förse den med en [enhetsanslutningssträng](how-to-register-device.md#register-in-the-azure-portal) som du kan skapa genom att registrera en ny enhet i IoT-hubben.

Öppna konfigurationsfilen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta etableringskonfigurationerna för filen och ta av avsnittet **Manuell etableringskonfiguration.** Uppdatera värdet **för device_connection_string** med anslutningssträngen från IoT Edge-enheten. Kontrollera att andra etableringsavsnitt kommenteras. Kontrollera att **etableringen:** raden inte har något föregående blanktecken och att kapslade objekt är indragna med två blanksteg.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Om du vill klistra `Shift+Right Click` in `Shift+Insert`innehållet i Urklipp i Nano eller trycka på .

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

När du har angett etableringsinformationen i konfigurationsfilen startar du om demonen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: Automatisk etablering

IoT Edge-enheter kan etableras automatiskt med hjälp av [DPS (Azure IoT Hub Device Provisioning Service).](../iot-dps/index.yml) För närvarande stöder IoT Edge två attestationmekanismer när du använder automatisk etablering, men maskinvarukraven kan påverka dina val. Raspberry Pi-enheter levereras till exempel inte med ett TPM-chip (Trusted Platform Module) som standard. Mer information finns i följande artiklar:

* [Skapa och etablera en IoT Edge-enhet med en virtuell TPM på en Virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md)
* [Skapa och etablera en IoT Edge-enhet med X.509-certifikat](how-to-auto-provision-x509-certs.md)
* [Skapa och etablera en IoT Edge-enhet med symmetrisk nyckeltysstik](how-to-auto-provision-symmetric-keys.md)

Dessa artiklar går igenom att ställa in registreringar i DPS och generera rätt certifikat eller nycklar för attestering. Oavsett vilken attestation-mekanism du väljer läggs etableringsinformationen till i IoT Edge-konfigurationsfilen på IoT Edge-enheten.

Öppna konfigurationsfilen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Hitta de etableringskonfigurationer av filen och avkommentera avsnittet som är lämpligt för din attestation mekanism. Kontrollera att andra etableringsavsnitt kommenteras. **Etableringen:** raden ska inte ha något föregående blanktecken och kapslade objekt ska dras in med två blanksteg. Uppdatera värdet **för scope_id** med värdet från ioT Hub Device Provisioning Service-instansen och ange lämpliga värden för attestationfälten.

TPM-intyg:

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

X.509 intyg:

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

Symmetrisk nyckeltörsstation:

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

Om du vill klistra `Shift+Right Click` in `Shift+Insert`innehållet i Urklipp i Nano eller trycka på .

Spara och stäng filen. `CTRL + X`, `Y`, `Enter`

När du har angett etableringsinformationen i konfigurationsfilen startar du om demonen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera lyckad installation

Om du använde de **manuella konfigurationsstegen** i föregående avsnitt bör IoT Edge-körningen etableras och köras på enheten. Om du har använt de **automatiska konfigurationsstegen** måste du slutföra ytterligare steg så att körningen kan registrera enheten med din IoT-hubb för din räkning. Nästa steg finns i [Skapa och etablera en simulerad TPM IoT Edge-enhet på en virtuell Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrollera status för IoT Edge Daemon:

```bash
systemctl status iotedge
```

Undersök daemon loggar:

```bash
journalctl -u iotedge --no-pager --no-full
```

Kör en automatisk kontroll efter de vanligaste konfigurations- och nätverksfelen:

```bash
sudo iotedge check
```

Tills du distribuerar din första modul till IoT Edge på enheten kommer **$edgeHub** systemmodulen inte att distribueras till enheten. Som ett resultat returnerar den automatiska `Edge Hub can bind to ports on host` kontrollen ett fel för anslutningskontrollen. Det här felet kan ignoreras om det inte inträffar efter distribution av en modul till enheten.

Slutligen, lista som kör moduler:

```bash
sudo iotedge list
```

När du har installerat IoT Edge på enheten är den enda modulen du bör se köra **edgeAgent**. När du har skapat din första distribution startar även den andra systemmodulen **$edgeHub** på enheten. Mer information finns i [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Felsökning och tips

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat körningen loggar du ut från datorn och loggar in igen för att uppdatera dina behörigheter automatiskt. Tills dess, använd **sudo** framför alla `iotedge` kommandon.

På resursbegränsade enheter rekommenderar vi starkt att du ställer in miljövariabeln *OptimizeForPerformance* på *false* enligt instruktionerna i [felsökningsguiden](troubleshoot.md).

Om nätverket som har en proxyserver följer du stegen i [Konfigurera IoT Edge-enheten för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verifiera din Linux-kärna för Moby-kompatibilitet

Många inbäddade enhetstillverkare skickar enhetsavbildningar som innehåller anpassade Linux-kärnor utan de funktioner som krävs för kompatibilitet med behållarens körning. Om du stöter på problem när du installerar den rekommenderade Moby-behållarkörningen kan du kanske felsöka din Linux-kärnkonfiguration med hjälp av [check-config-skriptet](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) från den officiella [Moby GitHub-lagringsplatsen](https://github.com/moby/moby). Kör följande kommandon på enheten för att kontrollera din kärnkonfiguration:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Det här kommandot innehåller en detaljerad utdata som innehåller status för kärnfunktioner som används av Moby-körningen. Du kommer att vilja se `Generally Necessary` `Network Drivers` till att alla objekt under och är aktiverade för att säkerställa att din kärna är helt kompatibel med Moby runtime.  Om du har identifierat några funktioner som saknas aktiverar du dem genom att återskapa kärnan från källan och välja de associerade modulerna för att ingå i lämplig kärna .config.  På samma sätt, om du använder `defconfig` `menuconfig`en kernel konfigurationsgenerator som eller , hitta och aktivera respektive funktioner och återskapa din kärna därefter.  När du har distribuerat den nyligen ändrade kärnan kör du check-config-skriptet igen för att kontrollera att alla nödvändiga funktioner har aktiverats.

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Linux-enhet använder du följande kommandon från kommandoraden.

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge-körningen tas bort stoppas behållarna som skapas men finns fortfarande på enheten. Visa alla behållare för att se vilka som finns kvar.

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, inklusive de två körningsbehållarna.

```bash
sudo docker rm -f <container name>
```

Ta slutligen bort behållarkörningen från enheten.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nästa steg

Nu när du har etablerat en IoT Edge-enhet med körningen installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med att IoT Edge-körningen installeras korrekt kan du läsa [felsökningssidan.](troubleshoot.md)

Information om hur du uppdaterar en befintlig installation till den senaste versionen av IoT Edge finns i [Uppdatera säkerhetsdemonen och körningen i IoT Edge](how-to-update-iot-edge.md).
