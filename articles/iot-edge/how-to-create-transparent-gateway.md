---
title: Skapa transparent gateway-enhet – Azure IoT Edge | Microsoft Docs
description: Använd en Azure IoT Edge enhet som en transparent gateway som kan bearbeta information från underordnade enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792338"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet som fungerar som en transparent Gateway

Den här artikeln innehåller detaljerade anvisningar för hur du konfigurerar en IoT Edge-enhet så att den fungerar som en transparent Gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln refererar termen *IoT Edge Gateway* till en IoT Edge enhet som används som en transparent Gateway. Mer information finns i [så här kan en IoT Edge enhet användas som en gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>Öppet
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge gatewayer. 
> * Underordnade enheter kan inte använda fil uppladdning.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det första steget:

1. **Gateway-enheten måste kunna ansluta till underordnade enheter på ett säkert sätt, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt mål.**
2. Den underordnade enheten måste ha en enhets identitet för att kunna autentisera med IoT Hub och kunna kommunicera via dess gateway-enhet. Mer information finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Den underordnade enheten måste kunna ansluta till sin gateway-enhet på ett säkert sätt. Mer information finns i [ansluta en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).


För att en enhet ska fungera som en gateway måste den kunna ansluta till dess underordnade enheter på ett säkert sätt. Med Azure IoT Edge kan du använda en PKI (Public Key Infrastructure) för att konfigurera säkra anslutningar mellan enheter. I det här fallet låter vi en underordnad enhet ansluta till en IoT Edge-enhet som fungerar som en transparent Gateway. För att upprätthålla rimlig säkerhet bör den underordnade enheten bekräfta gateway-enhetens identitet. Den här identitets kontrollen förhindrar att enheterna ansluter till potentiellt skadliga gatewayer.

En underordnad enhet i ett scenario med transparent Gateway kan vara vilket program eller vilken plattform som helst som har en identitet som skapats med [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) Cloud service. I många fall använder dessa program [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). I alla praktiska syfte kan en underordnad enhet även vara ett program som körs på själva IoT Edge gateway-enheten. En IoT Edge enhet kan dock inte underordnas IoT Edge Gateway. 

Du kan skapa en certifikat infrastruktur som aktiverar det förtroende som krävs för din enhets-Gateway-topologi. I den här artikeln förutsätter vi samma certifikat inställningar som du använde för att aktivera [x. 509 ca-säkerhet](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, vilket inbegriper ett X. 509 CA-certifikat som är kopplat till en speciell IoT-hubb (IoT Hub rot certifikat utfärdare), en serie med certifikat som har signerats med denna certifikat utfärdare och en certifikat utfärdare för IoT Edges enheten.

![Konfiguration av Gateway-certifikat](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termen "rot certifikat utfärdare" som används i den här artikeln avser det offentliga auktoritets certifikatet för PKI-certifikat kedjan och inte nödvändigt vis certifikat roten för en syndikerad certifikat utfärdare. I många fall är det faktiskt ett offentligt certifikat för certifikat utfärdare. 

Gatewayen visar IoT Edge enhetens CA-certifikat till den underordnade enheten under initieringen av anslutningen. Den underordnade enheten kontrollerar att IoT Edge enhetens CA-certifikat är signerat av rot certifikat utfärdarens certifikat. Den här processen gör att den underordnade enheten kan bekräfta att gatewayen kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikaten och installera dem på rätt plats på gatewayen. Du kan använda vilken dator som helst för att generera certifikaten och sedan kopiera dem till din IoT Edge-enhet. 

## <a name="prerequisites"></a>Krav

* En utvecklings dator för att skapa certifikat. 
* En Azure IoT Edge enhet som ska konfigureras som en gateway. Använd IoT Edge installations steg för något av följande operativ system:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>Generera certifikat med Windows

Använd stegen i det här avsnittet för att generera test certifikat i Windows. Du kan använda en Windows-dator för att generera certifikaten och sedan kopiera dem till valfri IoT Edge enhet som körs på ett operativ system som stöds. 

De certifikat som genereras i det här avsnittet är endast avsedda för testning. 

### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikaten. Om du redan har OpenSSL installerat på din Windows-enhet kan du hoppa över det här steget, men se till att openssl. exe finns i miljövariabeln PATH. 

Det finns flera sätt att installera OpenSSL, inklusive:

* **Enklare:** Hämta och installera eventuella [openssl-binärfiler från tredje part](https://wiki.openssl.org/index.php/Binaries), till exempel från [openssl på SourceForge](https://sourceforge.net/projects/openssl/). Lägg till den fullständiga sökvägen till openssl. exe i din PATH-miljö variabel. 
   
* **Rekommenderas:** Ladda ned OpenSSL-källkoden och bygg binärfilerna på din dator av dig själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg för att ladda ned källkod, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till en katalog där du vill installera vcpkg. Vi kommer att referera till den här katalogen som *\<VCPKGDIR >* . Följ anvisningarna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. När vcpkg har installerats kör du följande kommando från en PowerShell-kommandotolk för att installera OpenSSL-paketet för Windows x64. Det tar vanligt vis ungefär 5 minuter att slutföra installationen.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Lägg till `<VCPKGDIR>\installed\x64-windows\tools\openssl` i miljövariabeln PATH så att filen openssl. exe är tillgänglig för anrop.

### <a name="prepare-creation-scripts"></a>Förbereda skapande av skript

Azure IoT Edge git-lagringsplatsen innehåller skript som du kan använda för att generera test certifikat. I det här avsnittet ska du klona IoT Edge lagrings platsen och köra skripten. 

1. Öppna ett PowerShell-fönster i administratörs läge. 

2. Klona git-lagrings platsen som innehåller skript för att generera icke-produktions certifikat. De här skripten hjälper dig att skapa de certifikat som krävs för att konfigurera en transparent Gateway. Använd `git clone` kommandot eller [Hämta zip](https://github.com/Azure/iotedge/archive/master.zip)-filen. 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigera till den katalog som du vill arbeta med. I den här artikeln kallar vi katalogen *\<WRKDIR >* . Alla certifikat och nycklar kommer att skapas i den här arbets katalogen.

4. Kopiera konfigurations-och skriptfiler från den klonade lagrings platsen till din arbets katalog. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du har hämtat lagrings platsen som ett ZIP är mappnamnet `iotedge-master` och resten av sökvägen är samma. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Aktivera PowerShell för att köra skripten.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Ta de funktioner som används av skripten till PowerShell: s globala namn område.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell-fönstret visar en varning om att de certifikat som genereras av det här skriptet endast är i test syfte och inte ska användas i produktions scenarier.

8. Kontrol lera att OpenSSL har installerats korrekt och att det inte finns namn konflikter med befintliga certifikat. Om det finns problem bör skriptet beskriva hur de ska åtgärdas i systemet.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Skapa certifikat

I det här avsnittet skapar du tre certifikat och ansluter dem sedan i en kedja. Genom att placera certifikaten i en kedje fil kan du enkelt installera dem på din IoT Edge gateway-enhet och eventuella efterföljande enheter.  

1. Skapa rot certifikat utfärdarens certifikat och be det att signera ett mellanliggande certifikat. Alla certifikat placeras i din arbets katalog.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, men vi ska referera till ett i synnerhet i den här artikeln:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet, till exempel **MyEdgeDeviceCA**. Namnet används för att namnge filerna och under genereringen av certifikatet. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   Det här skript kommandot skapar flera certifikat och viktiga filer, inklusive två som vi ska referera till senare i den här artikeln:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Om du anger ett annat namn än **MyEdgeDeviceCA**, kommer de certifikat och nycklar som skapas med det här kommandot att återspegla det namnet. 

Nu när du har certifikaten kan du gå vidare till [Installera certifikat på gatewayen](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generera certifikat med Linux

Använd stegen i det här avsnittet för att generera test certifikat på Linux. Du kan använda en Linux-dator för att generera certifikaten och sedan kopiera dem till valfri IoT Edge enhet som körs på ett operativ system som stöds. 

De certifikat som genereras i det här avsnittet är endast avsedda för testning. 

### <a name="prepare-creation-scripts"></a>Förbereda skapande av skript

Azure IoT Edge git-lagringsplatsen innehåller skript som du kan använda för att generera test certifikat. I det här avsnittet ska du klona IoT Edge lagrings platsen och köra skripten. 

1. Klona git-lagrings platsen som innehåller skript för att generera icke-produktions certifikat. De här skripten hjälper dig att skapa de certifikat som krävs för att konfigurera en transparent Gateway. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigera till den katalog som du vill arbeta med. Vi kommer att referera till den här katalogen i hela artikeln som *\<WRKDIR >* . Alla certifikat och viktiga filer kommer att skapas i den här katalogen.
  
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

### <a name="create-certificates"></a>Skapa certifikat

I det här avsnittet skapar du tre certifikat och ansluter dem sedan i en kedja. Genom att placera certifikaten i en kedje fil kan du enkelt installera dem på din IoT Edge gateway-enhet och eventuella efterföljande enheter.  

1. Skapa rot certifikat utfärdarens certifikat och ett mellanliggande certifikat. Dessa certifikat placeras i *\<WRKDIR >* .

   Kör inte det här skriptet igen om du redan har skapat rot-och mellanliggande certifikat i den här arbets katalogen. Om du kör det här skriptet skrivs befintliga certifikat över. Fortsätt i stället till nästa steg. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skriptet skapar flera certifikat och nycklar. Anteckna ett, som vi ska referera till i nästa avsnitt:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Skapa IoT Edge enhetens CA-certifikat och privata nyckel med följande kommando. Ange ett namn för CA-certifikatet, till exempel **MyEdgeDeviceCA**. Namnet används för att namnge filerna och under genereringen av certifikatet. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   Skriptet skapar flera certifikat och nycklar. Anteckna två, som vi ska referera till i nästa avsnitt: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >Om du anger ett annat namn än **MyEdgeDeviceCA**, kommer de certifikat och nycklar som skapas med det här kommandot att återspegla det namnet. 

## <a name="install-certificates-on-the-gateway"></a>Installera certifikat på gatewayen

Nu när du har skapat en certifikat kedja måste du installera den på den IoT Edge gateway-enheten och konfigurera IoT Edge körningen så att den refererar till de nya certifikaten. 

1. Kopiera följande filer från *\<WRKDIR >* . Spara dem var som helst på din IoT Edge-enhet. Vi refererar till mål katalogen på din IoT Edge-enhet eftersom *\<CERTDIR >* . 

   * Enhetens CA-certifikat – `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * Privat nyckel för enhets-CA-`<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Rot-CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.  Om du har genererat certifikaten på själva enheten för IoT Edge kan du hoppa över det här steget och använda sökvägen till arbets katalogen.

2. Öppna konfigurations filen för IoT Edge Security daemon. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ange **certifikat** egenskaperna i filen config. yaml till den fullständiga sökvägen till certifikatet och nyckelfilen på den IoT Edge enheten. Ta bort `#`-tecknen innan certifikat egenskaperna tar bort kommentarer till de fyra raderna. Kom ihåg att indrag i yaml är två blank steg.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. På Linux-enheter ser du till att användar **iotedge** har Läs behörighet för den katalog som innehåller certifikaten. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera EdgeHub till gatewayen

Första gången du installerar IoT Edge på en enhet startar bara en systemmodul automatiskt: IoT Edge agenten. För att enheten ska fungera som en gateway behöver du båda systemmodulerna. Om du inte har distribuerat några moduler till din gateway-enhet tidigare skapar du en första distribution för enheten för att starta den andra systemmodulen, IoT Edge hubben. Distributionen kommer att se Tom ut eftersom du inte lägger till några moduler i guiden, men det ser till att båda systemmodulerna körs. 

Du kan kontrol lera vilka moduler som körs på en enhet med kommandot `iotedge list`. Om listan endast returnerar modulen **edgeAgent** utan **edgeHub**, använder du följande steg:

1. Gå till din IoT-hubb på Azure Portal.

2. Gå till **IoT Edge** och välj IoT Edge enheten som du vill använda som en gateway.

3. Välj **Ange moduler**.

4. Välj **Nästa**.

5. På sidan **Ange vägar** ska du ha en standard väg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. På sidan **Granska mall** väljer du **Skicka**.

## <a name="open-ports-on-gateway-device"></a>Öppna portar på gateway-enhet

Standard IoT Edge enheter behöver ingen inkommande anslutning för att fungera eftersom all kommunikation med IoT Hub görs via utgående anslutningar. Gateway-enheter skiljer sig åt eftersom de måste ta emot meddelanden från deras underordnade enheter. Om en brand vägg är mellan de efterföljande enheterna och gateway-enheten, behöver kommunikationen också vara möjlig genom brand väggen.

För att ett Gateway-scenario ska fungera måste minst ett av de protokoll som stöds av IoT Edge Hub vara öppet för inkommande trafik från efterföljande enheter. De protokoll som stöds är MQTT, AMQP, HTTPS, MQTT över WebSockets och AMQP för WebSockets. 

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>Dirigera meddelanden från underordnade enheter
IoT Edge runtime kan dirigera meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. Med den här funktionen kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. 

För närvarande är det hur du dirigerar meddelanden som skickas av underordnade enheter genom att skilja dem från meddelanden som skickas av moduler. Meddelanden som skickas av moduler innehåller alla system egenskaper som heter **connectionModuleId** men meddelanden som skickas av underordnade enheter gör inte det. Du kan använda WHERE-satsen för vägen för att undanta eventuella meddelanden som innehåller system egenskapen. 

Nedanstående väg är ett exempel som skickar meddelanden från en underordnad enhet till en modul med namnet `ai_insights`och sedan från `ai_insights` till IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Mer information om meddelanderoutning finns i [distribuera moduler och upprätta vägar](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Aktivera utökad offline-åtgärd

Från och med [v 1.0.4-versionen](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) av IoT Edge runtime kan gateway-enheten och underordnade enheter som ansluter till den konfigureras för utökad offline-åtgärd. 

Med den här funktionen kan lokala moduler eller underordnade enheter återautentiseras med den IoT Edge enheten vid behov och kommunicera med varandra med hjälp av meddelanden och metoder, även när de är frånkopplade från IoT-hubben. Mer information finns i [förstå utökade offline-funktioner för IoT Edge enheter, moduler och underordnade enheter](offline-capabilities.md).

Om du vill aktivera utökade offline-funktioner upprättar du en överordnad-underordnad relation mellan en IoT Edge gateway-enhet och underordnade enheter som ska ansluta till den. Dessa steg beskrivs i detalj i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet som fungerar som en transparent Gateway måste du konfigurera de underordnade enheterna så att de litar på gatewayen och skicka meddelanden till den. Fortsätt med att [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för nästa steg i konfigurationen av scenariot för transparent Gateway. 
