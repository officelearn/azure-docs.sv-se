---
title: Skapa testcertifikat – Azure IoT Edge | Microsoft-dokument
description: Skapa testcertifikat och lär dig hur du installerar dem på en Azure IoT Edge-enhet för att förbereda för produktionsdistribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364846f6cef196f6cefa7872af48f262b387db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393824"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Skapa democertifikat för att testa IoT Edge-enhetsfunktioner

IoT Edge-enheter kräver certifikat för säker kommunikation mellan körningen, modulerna och alla nedströmsenheter.
Om du inte har en certifikatutfärdare för att skapa de certifikat som krävs kan du använda democertifikat för att prova IoT Edge-funktioner i testmiljön.
I den här artikeln beskrivs funktionerna i certifikatgenereringsskripten som IoT Edge tillhandahåller för testning.

Dessa certifikat upphör att gälla om 30 dagar och bör inte användas i något produktionsscenario.

Du kan skapa certifikat på vilken dator som helst och sedan kopiera dem till din IoT Edge-enhet.
Det är enklare att använda din primära dator för att skapa certifikaten i stället för att generera dem på själva IoT Edge-enheten.
Genom att använda den primära datorn kan du ställa in skripten en gång och sedan upprepa processen för att skapa certifikat för flera enheter.

Så här skapar du democertifikat för att testa ditt IoT Edge-scenario:

1. [Konfigurera skript](#set-up-scripts) för certifikatgenerering på enheten.
2. [Skapa rotcertifikatutfärdarcertifikatet](#create-root-ca-certificate) som du använder för att signera alla andra certifikat för ditt scenario.
3. Generera de certifikat du behöver för det scenario som du vill testa:
   * [Skapa IoT Edge-enhetsidentitetscertifikat](#create-iot-edge-device-identity-certificates) för att testa automatisk etablering med IoT Hub Device Provisioning Service.
   * [Skapa IoT Edge-enhetscertifikatutfärdare](#create-iot-edge-device-ca-certificates) för att testa produktionsscenarier eller gatewayscenarier.
   * [Skapa underordnade enhetscertifikat](#create-downstream-device-certificates) för att testa autentisering av underordnade enheter till IoT Hub i ett gateway-scenario.

## <a name="prerequisites"></a>Krav

En utvecklingsmaskin med Git installerad.

## <a name="set-up-scripts"></a>Konfigurera skript

IoT Edge-databasen på GitHub innehåller certifikatgenereringsskript som du kan använda för att skapa democertifikat.
Det här avsnittet innehåller instruktioner för hur du förbereder skripten så att de körs på datorn, antingen i Windows eller Linux.
Om du är på en Linux-maskin, hoppa framåt för att [ställa in på Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Konfigurera på Windows

Om du vill skapa democertifikat på en Windows-enhet måste du installera OpenSSL och sedan klona genereringsskripten och konfigurera dem så att de körs lokalt i PowerShell.

#### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikaten.
Om du redan har OpenSSL installerat på din Windows-enhet kan du hoppa över det här steget, men se till att openssl.exe är tillgängligt i din PATH-miljövariabel.

Det finns flera sätt att installera OpenSSL, inklusive följande alternativ:

* **Enklare:** Hämta och installera alla [OpenSSL-binärfiler](https://wiki.openssl.org/index.php/Binaries)från tredje part , till exempel från [OpenSSL på SourceForge](https://sourceforge.net/projects/openssl/). Lägg till den fullständiga sökvägen till openssl.exe i sökvägsmiljövariabeln.

* **Rekommenderas:** Ladda ner OpenSSL källkoden och bygga binärer på din maskin själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Instruktionerna nedan använder vcpkg för att hämta källkod, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till en katalog där du vill installera vcpkg. Följ instruktionerna för att ladda ner och installera [vcpkg](https://github.com/Microsoft/vcpkg).

   2. När vcpkg har installerats kör du följande kommando från en PowerShell-uppmaning för att installera OpenSSL-paketet för Windows x64. Installationen tar vanligtvis cirka 5 minuter att slutföra.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Lägg `<vcpkg path>\installed\x64-windows\tools\openssl` till i sökvägsmiljövariabeln så att filen openssl.exe är tillgänglig för anrop.

#### <a name="prepare-scripts-in-powershell"></a>Förbereda skript i PowerShell

Azure IoT Edge git-databasen innehåller skript som du kan använda för att generera testcertifikat.
I det här avsnittet klonar du IoT Edge-repoen och kör skripten.

1. Öppna ett PowerShell-fönster i administratörsläge.

2. Klona IoT Edge git-repoen, som innehåller skript för att generera democertifikat. Använd `git clone` kommandot eller [hämta ZIP](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigera till den katalog där du vill arbeta. I hela den här artikeln kallar vi den här katalogen * \<WRKDIR>*. Alla certifikat och nycklar skapas i den här arbetskatalogen.

4. Kopiera konfigurations- och skriptfilerna från den klonade repoen i arbetskatalogen.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du hämtade repoen som en ZIP `iotedge-master` är mappnamnet och resten av sökvägen är densamma.

5. Aktivera PowerShell för att köra skripten.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. För in de funktioner som används av skripten i PowerShells globala namnområde.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell-fönstret visar en varning om att certifikaten som genereras av det här skriptet endast är avsedda för testning och inte bör användas i produktionsscenarier.

7. Kontrollera att OpenSSL har installerats korrekt och kontrollera att det inte blir namnkollisioner med befintliga certifikat. Om det finns problem bör skriptutdata beskriva hur du åtgärdar dem på datorn.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Konfigurera på Linux

Om du vill skapa democertifikat på en Windows-enhet behöver du klona genereringsskripten och konfigurera dem så att de körs lokalt i bash.

1. Klona IoT Edge git-repoen, som innehåller skript för att generera democertifikat.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigera till den katalog där du vill arbeta. Vi kommer att hänvisa till denna katalog i hela artikeln som * \<WRKDIR>*. Alla certifikat- och nyckelfiler skapas i den här katalogen.
  
3. Kopiera konfigurations- och skriptfilerna från den klonade IoT Edge-repoen i arbetskatalogen.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Skapa rotcertifikatutfärdarcertifikat

Rotcertifikatutfärdarcertifikatet används för att göra alla andra democertifikat för att testa ett IoT Edge-scenario.
Du kan fortsätta använda samma rotcertifikatutfärdare för att skapa democertifikat för flera IoT Edge- eller nedströmsenheter.

Om du redan har ett rotcertifikatutfärdarcertifikat i arbetsmappen ska du inte skapa ett nytt.
Det nya rotcertifikatutfärdarcertifikatet skriver över det gamla och alla nedströmscertifikat som görs från det gamla slutar fungera.
Om du vill ha flera rotcertifikatutfärdare måste du hantera dem i separata mappar.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnittet [Konfigurera skript](#set-up-scripts) för att förbereda en arbetskatalog med skript för generering av democertifikat.

### <a name="windows"></a>Windows

1. Navigera till arbetskatalogen där du placerade certifikatgenereringsskripten.

1. Skapa rotcertifikatutfärdarcertifikatet och låt det signera ett mellanliggande certifikat. Alla certifikat placeras i arbetskatalogen.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler, men när artiklar frågar efter **rotcertifikatet**använder du följande fil:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigera till arbetskatalogen där du placerade certifikatgenereringsskripten.

1. Skapa rotcertifikatutfärdarcertifikatet och ett mellanliggande certifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler, men när artiklar frågar efter **rotcertifikatet**använder du följande fil:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Skapa certifikat för IoT Edge-enhetscertifikat

Varje IoT Edge-enhet som går till produktion behöver ett enhetscertifikatutfärdarcertifikat som refereras från filen config.yaml.
Enhetscertifikatutfärdaren ansvarar för att skapa certifikat för moduler som körs på enheten.
Det är också hur IoT Edge-enheten verifierar sin identitet när den ansluter till nedströmsenheter.

Enhetscertifikatutfärdare finns i **avsnittet Certifikat** i filen config.yaml på IoT Edge-enheten.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa rotcertifikatcertifikat.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

1. Navigera till arbetskatalogen som har certifikatgenereringsskript och rotcertifikatutfärdarcertifikat.

2. Skapa IoT Edge-enhetscertifikatutfärdaren och den privata nyckeln med följande kommando. Ange ett namn för CERTIFIKATUTFÄRD-certifikatet, till exempel **MyEdgeDeviceCA**, som används för att namnge utdatafilerna.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till en IoT Edge-enhet och refereras i filen config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Gateway-enhetsnamnet som skickas till dessa skript bör inte vara samma som parametern "hostname" i config.yaml eller enhetens ID i IoT Hub.
Skripten hjälper dig att undvika problem genom att lägga till en ".ca"-sträng till gateway-enhetens namn för att förhindra att namnet kollision om en användare ställer in IoT Edge med samma namn på båda ställena.
Det är dock bra att undvika att använda samma namn.

### <a name="linux"></a>Linux

1. Navigera till arbetskatalogen som har certifikatgenereringsskript och rotcertifikatutfärdarcertifikat.

2. Skapa IoT Edge-enhetscertifikatutfärdaren och den privata nyckeln med följande kommando. Ange ett namn för CERTIFIKATUTFÄRD-certifikatet, till exempel **MyEdgeDeviceCA**, som används för att namnge utdatafilerna.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till en IoT Edge-enhet och refereras i filen config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Gateway-enhetsnamnet som skickas till dessa skript bör inte vara samma som parametern "hostname" i config.yaml eller enhetens ID i IoT Hub.
Skripten hjälper dig att undvika problem genom att lägga till en ".ca"-sträng till gateway-enhetens namn för att förhindra att namnet kollision om en användare ställer in IoT Edge med samma namn på båda ställena.
Det är dock bra att undvika att använda samma namn.

## <a name="create-iot-edge-device-identity-certificates"></a>Skapa identitetscertifikat för IoT Edge-enheten

Enhetsidentitetscertifikat används för att etablera IoT Edge-enheter via [DPS (Azure IoT Hub Device Provisioning Service).](../iot-dps/index.yml)

Enhetsidentitetscertifikat finns i avsnittet **Etablering** i filen config.yaml på IoT Edge-enheten.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa rotcertifikatcertifikat.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

Skapa IoT Edge-enhetsidentitetscertifikatet och den privata nyckeln med följande kommando:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Namnet som du skickar in till det här kommandot är enhets-ID för IoT Edge-enheten i IoT Hub.

Det nya enhetsidentitetskommandot skapar flera certifikat- och nyckelfiler, inklusive tre som du ska använda när du skapar en enskild registrering i DPS och installerar IoT Edge-körningen:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Skapa IoT Edge-enhetsidentitetscertifikatet och den privata nyckeln med följande kommando:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Namnet som du skickar in till det här kommandot är enhets-ID för IoT Edge-enheten i IoT Hub.

Skriptet skapar flera certifikat- och nyckelfiler, inklusive tre som du ska använda när du skapar en enskild registrering i DPS och installerar IoT Edge-körningen:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Skapa underordnade enhetscertifikat

Om du konfigurerar en nedströms IoT-enhet för ett gatewayscenario kan du generera democertifikat för X.509-autentisering.
Det finns två sätt att autentisera en IoT-enhet med X.509-certifikat: använda självsignerade certifikat eller använda certifikatutfärdare signerade certifikat.
För X.509 självsignerad autentisering, ibland kallad tumavtrycksautentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet.
Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering.
För X.509 certifikatutfärdare (CA) signerad autentisering behöver du ett rotcertifikatutfärdarcertifikat som registrerats i IoT Hub och som du använder för att signera certifikat för din IoT-enhet.
Alla enheter som använder ett certifikat som har utfärdats av rotcertifikatutfärdaren eller något av dess mellanliggande certifikat tillåts autentiseras.

Certifikatgenereringsskripten kan hjälpa dig att skapa democertifikat för att testa något av dessa autentiseringsscenarier.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa rotcertifikatcertifikat.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Självsignerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du skapa enhetscertifikat baserat på rotcertifikatutfärdaren för din lösning.
Sedan hämtar du ett hexadecimalt "fingeravtryck" från certifikaten som ska tillhandahållas till IoT Hub.
Din IoT-enhet behöver också en kopia av sina enhetscertifikat så att den kan autentiseras med IoT Hub.

#### <a name="windows"></a>Windows

1. Navigera till arbetskatalogen som har certifikatgenereringsskript och rotcertifikatutfärdarcertifikat.

2. Skapa två certifikat (primära och sekundära) för nedströmsenheten. En enkel namngivningskonvention som ska användas är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Ett exempel:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till IoT-enheten nedströms och refereras i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-fingeravtrycket (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Fingeravtrycket är en 40 hexadecimal teckensträng. Använd följande kommandot openssl för att visa certifikatet och hitta fingeravtrycket:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du anger fingeravtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X.509-certifikat.

#### <a name="linux"></a>Linux

1. Navigera till arbetskatalogen som har certifikatgenereringsskript och rotcertifikatutfärdarcertifikat.

2. Skapa två certifikat (primära och sekundära) för nedströmsenheten. En enkel namngivningskonvention som ska användas är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Ett exempel:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till IoT-enheten nedströms och refereras i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-fingeravtrycket (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Fingeravtrycket är en 40 hexadecimal teckensträng. Använd följande kommandot openssl för att visa certifikatet och hitta fingeravtrycket:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Du anger både det primära och sekundära fingeravtrycket när du registrerar en ny IoT-enhet med självsignerade X.509-certifikat.

### <a name="ca-signed-certificates"></a>CA-signerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du ladda upp rotcertifikatutfärdaren för din lösning till IoT Hub.
Sedan utför du en verifiering för att bevisa för IoT Hub att du äger rotcertifikatutfärdarcertifikatet.
Slutligen använder du samma rotcertifikatutfärdare för att skapa enhetscertifikat som ska läggas på din IoT-enhet så att den kan autentiseras med IoT Hub.

Certifikaten i det här avsnittet är för stegen i [Konfigurera X.509-säkerhet i din Azure IoT-hubb](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Ladda upp rotcertifikatutfärdarcertifikatfilen `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`från arbetskatalogen till IoT-hubben.

2. Använd koden som finns i Azure-portalen för att verifiera att du äger rotcertifikatet.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Skapa en certifikatkedja för nedströmsenheten. Använd samma enhets-ID som enheten är registrerad med i IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till IoT-enheten nedströms och refereras i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Ladda upp rotcertifikatutfärdarcertifikatfilen `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`från arbetskatalogen till IoT-hubben.

2. Använd koden som finns i Azure-portalen för att verifiera att du äger rotcertifikatet.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Skapa en certifikatkedja för nedströmsenheten. Använd samma enhets-ID som enheten är registrerad med i IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Det här skriptkommandot skapar flera certifikat- och nyckelfiler. Följande certifikat- och nyckelpar måste kopieras över till IoT-enheten nedströms och refereras i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
