---
title: Skapa test certifikat – Azure IoT Edge | Microsoft Docs
description: Skapa test certifikat och lär dig hur du installerar dem på en Azure IoT Edge enhet för att förbereda för produktions distribution.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6f64a6714b9d795a1e809c555394be6f7671c63
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510676"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Skapa demonstrations certifikat för att testa IoT Edge enhets funktioner

IoT Edge enheter kräver certifikat för säker kommunikation mellan körningen, modulerna och eventuella efterföljande enheter.
Om du inte har någon certifikat utfärdare för att skapa de certifikat som krävs kan du använda demonstrations certifikat för att testa IoT Edge funktioner i test miljön.
I den här artikeln beskrivs funktionerna i skript för generering av certifikat som IoT Edge tillhandahåller för testning.

Dessa certifikat upphör att gälla om 30 dagar och ska inte användas i något produktions scenario.

Du kan skapa certifikat på vilken dator som helst och sedan kopiera dem till din IoT Edge-enhet.
Det är enklare att använda den primära datorn för att skapa certifikaten istället för att skapa dem på själva IoT Edge enheten.
Genom att använda den primära datorn kan du konfigurera skripten en gång och sedan upprepa processen för att skapa certifikat för flera enheter.

## <a name="prerequisites"></a>Krav

En utvecklings dator med git installerat.

## <a name="set-up-scripts"></a>Konfigurera skript

IoT Edge-lagringsplatsen på GitHub innehåller skript för generering av certifikat som du kan använda för att skapa demo certifikat.
Det här avsnittet innehåller anvisningar för hur du förbereder skript som ska köras på datorn, antingen på Windows eller Linux.
Om du använder en Linux-dator kan du gå vidare till [Konfigurera i Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Konfigurera i Windows

Om du vill skapa demo certifikat på en Windows-enhet måste du installera OpenSSL och sedan klona generationens skript och konfigurera dem för att köras lokalt i PowerShell.

#### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikat.
Om du redan har OpenSSL installerat på din Windows-enhet kan du hoppa över det här steget, men se till att openssl. exe finns i miljövariabeln PATH.

Det finns flera sätt att installera OpenSSL, inklusive följande alternativ:

* **Enklare:** Hämta och installera eventuella [openssl-binärfiler från tredje part](https://wiki.openssl.org/index.php/Binaries), till exempel från [openssl på SourceForge](https://sourceforge.net/projects/openssl/). Lägga till den fullständiga sökvägen till openssl.exe till SÖKVÄGEN för miljövariabeln.

* **Rekommenderat:** ladda ned källkoden OpenSSL och skapa de binära filerna på din dator själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg att ladda ned källkoden, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till den katalog som där du vill installera vcpkg. Följ instruktionerna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).

   2. När vcpkg har installerats kör du följande kommando från en PowerShell-kommandotolk för att installera OpenSSL-paketet för Windows x64. Installationen tar vanligt vis ungefär 5 minuter att slutföra.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Lägg till `<vcpkg path>\installed\x64-windows\tools\openssl` till SÖKVÄGEN för miljövariabeln så att filen openssl.exe är tillgänglig för anrop.

#### <a name="prepare-scripts-in-powershell"></a>Förbereda skript i PowerShell

Azure IoT Edge git-lagringsplatsen innehåller skript som du kan använda för att generera test certifikat.
I det här avsnittet ska du klona IoT Edge lagrings platsen och köra skripten.

1. Öppna ett PowerShell-fönster i administratörsläge.

2. Klona IoT Edge git-lagrings platsen, som innehåller skript för att generera demo certifikat. Använd den `git clone` kommando eller [ladda ned ZIP-filen](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigera till den katalog där du vill ska fungera. I den här artikeln kallar vi katalogen *\<WRKDIR >* . Alla certifikat och nycklar kommer att skapas i den här arbets katalogen.

4. Kopiera konfigurations-och skriptfiler från den klonade lagrings platsen till din arbets katalog.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du har hämtat lagrings platsen som ett ZIP är mappnamnet `iotedge-master` och resten av sökvägen är samma.

5. Aktivera PowerShell att köra skripten.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Ta de funktioner som används av skripten till PowerShell: s globala namn område.

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell-fönstret visar en varning om att de certifikat som genereras av det här skriptet endast är i test syfte och inte ska användas i produktions scenarier.

7. Kontrol lera att OpenSSL har installerats korrekt och att det inte finns namn konflikter med befintliga certifikat. Om det uppstår problem bör utdata i skriptet beskriva hur du kan åtgärda dem i systemet.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Konfigurera på Linux

Om du vill skapa demo certifikat på en Windows-enhet måste du klona generationens skript och konfigurera dem för att köras lokalt i bash.

1. Klona IoT Edge git-lagrings platsen, som innehåller skript för att generera demo certifikat.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigera till den katalog där du vill ska fungera. Vi kommer att referera till den här katalogen i hela artikeln som *\<WRKDIR >* . Alla certifikat och viktiga filer kommer att skapas i den här katalogen.
  
3. Kopiera config-och skriptfiler från den klonade IoT Edge lagrings platsen till din arbets katalog.

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

## <a name="create-root-ca-certificate"></a>Skapa rot certifikat för certifikat utfärdare

Rot certifikat UTFÄRDARens certifikat används för att göra alla andra demonstrations certifikat för att testa ett IoT Edge scenario.
Du kan fortsätta att använda samma certifikat från rot certifikat utfärdaren för att fatta demonstrations certifikat för flera IoT Edge eller underordnade enheter.

Om du redan har ett certifikat från en rot certifikat utfärdare i arbetsmappen ska du inte skapa en ny.
Det nya rot certifikat utfärdarens certifikat kommer att skriva över det gamla, och eventuella underordnade certifikat som har skapats från den gamla fungerar inte längre.
Om du vill ha flera certifikat från rot certifikat utfärdare, se till att hantera dem i separata mappar.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnittet [Konfigurera skript](#set-up-scripts) för att förbereda en arbets katalog med skript för generering av demo certifikat.

### <a name="windows"></a>Windows

1. Navigera till arbets katalogen där du placerade skripten för att skapa certifikat.

1. Skapa rot certifikat utfärdarens certifikat och be det att signera ett mellanliggande certifikat. Alla certifikat placeras i din arbets katalog.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men när artiklar frågar efter **rot-CA-certifikatet**använder du följande fil:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Navigera till arbets katalogen där du placerade skripten för att skapa certifikat.

1. Skapa rot certifikat utfärdarens certifikat och ett mellanliggande certifikat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men när artiklar frågar efter **rot-CA-certifikatet**använder du följande fil:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Skapa IoT Edge enhets certifikat för certifikat utfärdare

Varje IoT Edge enhet som kommer till produktion behöver ett CA-certifikatutfärdarcertifikat som refereras från config. yaml-filen. Enhetens CA-certifikat ansvarar för att skapa certifikat för moduler som körs på enheten. Det är också så att den IoT Edge enheten verifierar identiteten när den ansluter till efterföljande enheter.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa certifikat för rot certifikat utfärdare](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet, till exempel **MyEdgeDeviceCA**, som används för att namnge utdatafilerna.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till en IoT Edge-enhet och refereras till i filen config. yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Gateway-enhetens namn som skickades till dessa skript får inte vara samma som parametern "hostname" i config. yaml. Skripten hjälper dig att undvika eventuella problem genom att lägga till en ". ca"-sträng till gateway-enhetens namn för att förhindra att namn krockar om en användare konfigurerar IoT Edge att använda samma namn på båda platserna. Det är dock en bra idé att undvika att använda samma namn.

### <a name="linux"></a>Linux

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet, till exempel **MyEdgeDeviceCA**, som används för att namnge utdatafilerna.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till en IoT Edge-enhet och refereras till i filen config. yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Gateway-enhetens namn som skickades till dessa skript får inte vara samma som parametern "hostname" i config. yaml. Skripten hjälper dig att undvika eventuella problem genom att lägga till en ". ca"-sträng till gateway-enhetens namn för att förhindra att namn krockar om en användare konfigurerar IoT Edge att använda samma namn på båda platserna. Det är dock en bra idé att undvika att använda samma namn.

## <a name="create-x509-certs-for-downstream-devices"></a>Skapa X. 509-certifikat för underordnade enheter

Om du skapar en underordnad IoT-enhet för ett Gateway-scenario kan du generera demo certifikat för X. 509-autentisering.
Det finns två sätt att autentisera en IoT-enhet med X. 509-certifikat: använda självsignerade certifikat eller använda signerade certifikat från certifikat utfärdare (CA).
För X. 509-självsignerad autentisering, som ibland kallas tumavtryck-autentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet.
Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering.
För X. 509-signerad autentisering (CA) behöver du ett rot certifikat för certifikat utfärdare som registrerats i IoT Hub som du använder för att signera certifikat för din IoT-enhet.
Alla enheter som använder ett certifikat som har utfärdats av rot certifikat utfärdarens certifikat eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera.

Skripten för att skapa certifikat kan hjälpa dig att fatta demonstrations certifikat för att testa något av dessa scenarier för autentisering.

Innan du fortsätter med stegen i det här avsnittet följer du stegen i avsnitten [Konfigurera skript](#set-up-scripts) och [Skapa certifikat för rot certifikat utfärdare](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Självsignerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du skapa enhets certifikat baserat på rot certifikat utfärdarens certifikat för din lösning.
Sedan hämtar du ett hexadecimalt "finger avtryck" från certifikaten för att ange IoT Hub.
Din IoT-enhet behöver också en kopia av sina enhets certifikat så att den kan autentiseras med IoT Hub.

#### <a name="windows"></a>Windows

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. En enkel namngivnings konvention att använda är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Ett exempel:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Finger avtrycket är en hexadecimal tecken sträng på 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Kör det här kommandot två gånger, en gång för det primära certifikatet och en gång för det sekundära certifikatet. Du ger finger avtryck för båda certifikaten när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat.

#### <a name="linux"></a>Linux

1. Navigera till arbets katalogen som har skript för att skapa certifikat och rot certifikat utfärdare.

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. En enkel namngivnings konvention att använda är att skapa certifikaten med namnet på IoT-enheten och sedan den primära eller sekundära etiketten. Ett exempel:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-kontexter) från varje certifikat. Finger avtrycket är en hexadecimal tecken sträng på 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Du anger både primärt och sekundärt finger avtryck när du registrerar en ny IoT-enhet med självsignerade X. 509-certifikat.

### <a name="ca-signed-certificates"></a>CA-signerade certifikat

När du autentiserar en IoT-enhet med självsignerade certifikat måste du ladda upp rot certifikat utfärdarens certifikat för din lösning till IoT Hub.
Sedan utför du en verifiering för att bevisa att IoT Hub att du äger rot certifikat utfärdarens certifikat.
Slutligen använder du samma certifikat från rot certifikat utfärdaren för att skapa enhets certifikat som ska användas på din IoT-enhet så att det kan autentiseras med IoT Hub.

Certifikaten i det här avsnittet är för stegen i [Konfigurera X. 509-säkerhet i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Ladda upp rot certifikat utfärdarens certifikat fil från arbets katalogen `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`till din IoT-hubb.

2. Använd koden som anges i Azure Portal för att kontrol lera att du äger det rot certifikat utfärdarens certifikat.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma enhets-ID som enheten har registrerats med i IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Ladda upp rot certifikat utfärdarens certifikat fil från arbets katalogen `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`till din IoT-hubb.

2. Använd koden som anges i Azure Portal för att kontrol lera att du äger det rot certifikat utfärdarens certifikat.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma enhets-ID som enheten har registrerats med i IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer. Följande certifikat och nyckel par måste kopieras till den efterföljande IoT-enheten och refereras till i de program som ansluter till IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
