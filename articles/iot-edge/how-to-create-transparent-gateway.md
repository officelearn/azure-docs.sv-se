---
title: Skapa transparent gateway-enhet – Azure IoT Edge | Microsoft Docs
description: Använda en Azure IoT Edge-enhet som en transparent gateway som kan bearbeta information från underordnade enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058377"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway

Den här artikeln innehåller detaljerade anvisningar för att konfigurera en IoT Edge-enhet kan fungera som en transparent gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln termen *IoT Edge-gateway* refererar till en IoT Edge-enhet som används som en transparent gateway. Mer information finns i [hur en IoT Edge-enhet kan användas som en gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>För närvarande:
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge-gateways. 
> * Efterföljande enheter kan inte använda filuppladdningen.

Det finns tre allmänna steg för att skapa en lyckad transparent gatewayanslutning. Den här artikeln beskriver det första steget:

1. **Gateway-enheten måste kunna ansluta till underordnade enheter, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt destination på ett säkert sätt.**
2. Underordnade enheten måste ha en enhetsidentitet för att kunna autentisera med IoT Hub och veta för att kommunicera via dess gateway-enhet. Mer information finns i [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Underordnade enheten måste vara på ett säkert sätt ansluta till dess gateway-enhet. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).


För en enhet kan fungera som en gateway krävs för att kunna ansluta säkert till dess underordnade enheter. Azure IoT Edge kan du använda en public key infrastructure (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet vi så att en underordnad enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. Om du vill skydda rimliga, bör underordnade enheten bekräfta identiteten för gateway-enheten. Den här identitetskontroll förhindrar att dina enheter ansluter till potentiellt skadliga gateways.

En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på IoT Edge-gatewayenhet själva för alla praktiska syften. 

Du kan skapa någon infrastruktur för certifikat som gör det förtroendet som krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du använder för att aktivera [X.509 CA-säkerheten](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, som innebär att ett X.509 CA-certifikat som är kopplad till en specifik IoT-hubb (IoT hub rotcertifikatutfärdaren), en serie signerade med den här Certifikatutfärdaren och en Certifikatutfärdare för IoT Edge-enhet.

![Installationsprogram för gateway-certifikatet](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termen ”rot-CA” används i den här artikeln refererar till det offentliga certifikatet översta utfärdare av PKI-certifikatkedja, och inte nödvändigtvis en syndikerade certifikatutfärdare certifikatroten. I många fall är det faktiskt en mellanliggande offentlig certifikatutfärdare. 

Gatewayen anger kanten IoT device CA-certifikat till den underordnade enheten under initiering av anslutningen. Underordnad enhet kontrollerar om du vill kontrollera IoT Edge-enhetens CA-certifikat signerat av rotcertifikatutfärdarens certifikat. Den här processen kan underordnade enheten för att kontrollera att gatewayen kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikat och installera dem på rätt plats på gatewayen. Du kan använda valfri dator för att generera certifikat och kopiera dem sedan till din IoT Edge-enhet. 

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure IoT Edge-enhet för att konfigurera som en gateway. Använd IoT Edge-installationsstegen för något av följande operativsystem:
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Den här artikeln handlar om den *gateway värdnamn* på flera platser. Gateway-värdnamnet är deklarerad i den **värdnamn** -parametern för filen config.yaml på IoT Edge-gatewayenhet. Den används för att skapa certifikaten i den här artikeln och är som anges i anslutningssträngen för efterföljande enheter. Gateway-värdnamnet måste kunna matchas till en IP-adress, antingen med hjälp av DNS- eller en host-filpost.

## <a name="generate-certificates-with-windows"></a>Generera certifikat med Windows

Använd stegen i det här avsnittet för att generera testcertifikat på Windows. Du kan använda en Windows-dator för att generera certifikat och kopiera dem sedan till alla IoT Edge-enhet som körs på alla operativsystem som stöds. 

De certifikat som genereras i det här avsnittet är avsedda för endast i testningssyfte. 

### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikat. Om du redan har OpenSSL som har installerats på din Windows-enhet kan du hoppa över detta steg, men se till att openssl.exe är tillgängliga i miljövariabeln PATH. 

Det finns flera sätt kan du installera OpenSSL:

* **Enklare:** Ladda ned och installera eventuella [från tredje part OpenSSL binärfiler](https://wiki.openssl.org/index.php/Binaries), till exempel från [OpenSSL på SourceForge](https://sourceforge.net/projects/openssl/). Lägga till den fullständiga sökvägen till openssl.exe till SÖKVÄGEN för miljövariabeln. 
   
* **Rekommenderat:** Ladda ned källkoden OpenSSL och skapa de binära filerna på din dator själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg att ladda ned källkoden, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till den katalog som där du vill installera vcpkg. Vi ska referera till den här katalogen som  *\<VCPKGDIR >* . Följ instruktionerna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. En gång vcpkg har installerats kör du följande kommando från en powershell-kommandotolk för att installera OpenSSL-paketet för Windows x64. Installationen tar vanligtvis cirka 5 minuter att slutföra.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. Lägg till `<VCPKGDIR>\installed\x64-windows\tools\openssl` till SÖKVÄGEN för miljövariabeln så att filen openssl.exe är tillgänglig för anrop.

### <a name="prepare-creation-scripts"></a>Förbereda skripten för att skapa

Git-lagringsplatsen för Azure IoT Edge innehåller skript som du kan använda för att generera testcertifikat. I det här avsnittet, klona IoT Edge-lagringsplatsen och kör skripten. 

1. Öppna ett PowerShell-fönster i administratörsläge. 

2. Klona git-lagringsplats som innehåller skript för att generera certifikat för icke-produktion. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway. Använd den `git clone` kommando eller [ladda ned ZIP-filen](https://github.com/Azure/iotedge/archive/master.zip). 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Navigera till den katalog där du vill ska fungera. I den här artikeln ska vi kallar den här katalogen  *\<WRKDIR >* . Alla certifikat och nycklar skapas i den här arbetskatalogen.

4. Kopiera konfigurations-och skript från den klonade repon till din arbetskatalog. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du har hämtat lagringsplatsen som en ZIP så mappnamnet är `iotedge-master` och resten av sökvägen är samma. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. Aktivera PowerShell att köra skripten.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Ta med de funktioner som används av skripten i PowerShell-globalt namnområde.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell-fönstret visas en varning om att de certifikat som genereras av det här skriptet är endast för testning och ska inte användas i produktionsscenarier.

8. Kontrollera att OpenSSL har installerats korrekt och se till att det inte namnkonflikter med befintliga certifikat. Om det uppstår problem kan bör skriptet beskriva hur du löser dem på datorn.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Skapa certifikat

I det här avsnittet ska du skapa tre certifikat och Anslut dem i en kedja. Placera certifikaten i en kedja-fil kan du enkelt installera dem på din IoT Edge-gatewayenhet och alla underordnade enheter.  

1. Skapa certifikatet för rotcertifikatutfärdaren och den signera en mellanliggande certifikat. Certifikaten placeras i din arbetskatalog.

   ```powershell
   New-CACertsCertChain rsa
   ```

   Det här skriptkommandot skapar flera certifikat och nyckelfiler, men vi kommer att referera till en särskild senare i den här artikeln:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Skapa IoT Edge enheten CA-certifikat och privata nyckeln med följande kommando. Ange gateway-värdnamn, som finns i filen iotedge\config.yaml på gateway-enheten. Gateway-värdnamnet används för att namnge filerna och under skapande av certifikat. 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   Det här skriptkommandot skapar flera certifikat och viktiga filer, inklusive två som vi kommer att referera till senare i den här artikeln:
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

Nu när du har certifikat som kan gå vidare till [installera certifikat på gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>Generera certifikat med Linux

Använd stegen i det här avsnittet för att generera testcertifikat i Linux. Du kan använda en Linux-dator för att generera certifikat och kopiera dem sedan till alla IoT Edge-enhet som körs på alla operativsystem som stöds. 

De certifikat som genereras i det här avsnittet är avsedda för endast i testningssyfte. 

### <a name="prepare-creation-scripts"></a>Förbereda skripten för att skapa

Git-lagringsplatsen för Azure IoT Edge innehåller skript som du kan använda för att generera testcertifikat. I det här avsnittet, klona IoT Edge-lagringsplatsen och kör skripten. 

1. Klona git-lagringsplats som innehåller skript för att generera certifikat för icke-produktion. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway. 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Navigera till den katalog där du vill ska fungera. Vi ska referera till den här katalogen i artikeln som  *\<WRKDIR >* . Alla certifikat och nyckel filer skapas i den här katalogen.
  
3. Kopiera filerna konfigurations- och skript från den klonade IoT Edge-repon till din arbetskatalog.

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

I det här avsnittet ska du skapa tre certifikat och Anslut dem i en kedja. Placera certifikaten i en kedja-fil kan enkelt installera dem på din IoT Edge-gatewayenhet och alla underordnade enheter.  

1. Skapa rotcertifikatutfärdarens certifikat och en mellanliggande certifikat. Dessa certifikat är placerade i  *\<WRKDIR >* .

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skriptet skapar flera certifikat och nycklar. Anteckna något som vi ska referera till i nästa avsnitt:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Skapa IoT Edge enheten CA-certifikat och privata nyckeln med följande kommando. Ange gateway-värdnamn, som finns i filen iotedge/config.yaml på gateway-enheten. Gateway-värdnamnet används för att namnge filerna och under skapande av certifikat. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   Skriptet skapar flera certifikat och nycklar. Anteckna två, vilket vi refererar till i nästa avsnitt: 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>Installera certifikat på gateway

Nu när du har gjort en certifikatkedja, måste du installera den på IoT Edge-gatewayenhet och konfigurerar IoT Edge-körningen för att referera till de nya certifikaten. 

1. Kopiera följande filer från  *\<WRKDIR >* . Spara dem var som helst på din IoT Edge-enhet. Vi ska referera till målkatalogen på IoT Edge-enhet som  *\<CERTDIR >* . 

   * Enhets-CA-certifikat –  `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * Enhets-CA-privata nyckel – `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Rot-CA- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [säker kopia protokollet](https://www.ssh.com/ssh/scp/) att flytta certifikatdatabasen.  Om du har genererat certifikaten på själva IoT Edge-enheten kan du hoppa över detta steg och använda sökvägen till arbetskatalogen.

2. Öppna konfigurationsfilen för IoT Edge security daemon. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ange den **certifikat** egenskaper i config.yaml-filen till den fullständiga sökvägen till filerna för certifikat och nyckel på IoT Edge-enhet. Ta bort den `#` tecknet innan certifikategenskaper att ta bort kommentarerna för de fyra raderna. Kom ihåg att indrag i yaml är två blanksteg.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. På Linux enheter, se till att användaren **iotedge** har läsbehörighet för den katalog som innehåller certifikaten. 

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera EdgeHub till gatewayen

När du först installera IoT Edge på en enhet, endast en systemmodulen startas automatiskt: IoT Edge-agenten. För enheten att fungera som en gateway, måste både systemmoduler. Om du inte har distribuerat alla moduler till din gatewayenhet innan du kan skapa en inledande distribution för din enhet att starta den andra systemmodulen, IoT Edge hub. Distributionen ser tomt eftersom du inte lägger till alla moduler i guiden, men det ser till att båda systemmoduler körs. 

Du kan kontrollera vilka moduler som körs på en enhet med kommandot `iotedge list`. Om listan endast returnerar modulen **edgeAgent** utan **edgeHub**, Använd följande steg:

1. Gå till din IoT-hubb på Azure Portal.

2. Gå till **IoT Edge** och välj din IoT Edge-enhet som du vill använda som en gateway.

3. Välj **Ange moduler**.

4. Välj **Nästa**.

5. I den **ange vägar** sidan bör du ha en standardväg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. I den **granska mallen** väljer **skicka**.

## <a name="open-ports-on-gateway-device"></a>Öppna portar på gateway-enhet

Standard IoT Edge-enheter behöver inte inkommande anslutningar till funktionen, eftersom all kommunikation med IoT Hub görs via utgående anslutningar. Gateway-enheter skiljer sig eftersom de behöver för att ta emot meddelanden från sina underordnade enheter. Om en brandvägg mellan underordnade enheter och gateway-enheten måste kommunikation sedan vara möjlig genom brandväggen samt.

Minst en av IoT Edge-hubben protokoll som stöds måste vara öppna för inkommande trafik från underordnade enheter för en gatewayscenariot ska fungera. Protokoll som stöds är MQTT-, AMQP- och HTTPS. 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Skicka meddelanden från underordnade enheter
IoT Edge-körningen kan dirigera meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. Den här funktionen kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. 

För närvarande är det sätt som du vidarebefordra meddelanden som skickas av efterföljande enheter genom att skilja dem från meddelanden som skickas av moduler. Meddelanden som skickas av moduler som alla innehåller en systemegenskap som kallas **connectionModuleId** men inte meddelanden som skickas av efterföljande enheter. Du kan använda WHERE-satsen i vägen för att exkludera alla meddelanden som innehåller den Systemegenskapen. 

Det är ett exempel som skickar meddelanden från en underordnad enhet till en modul med namnet nedan väg `ai_insights`, och sedan `ai_insights` till IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Läs mer om meddelanderoutning [distribuerar moduler och upprätta vägar](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Aktivera utökad offline åtgärd

Från och med den [v1.0.4 versionen](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) av IoT Edge-körningen gateway-enheten och underordnade enheter som ansluter till den kan konfigureras för utökade offline igen. 

Med den här funktionen lokala moduler eller efterföljande enheter autentiseras igen med IoT Edge-enheten efter behov och kommunicera med varandra med hjälp av meddelanden och metoder även när kopplas bort från IoT hub. Mer information finns i [förstå utökat offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter](offline-capabilities.md).

Om du vill aktivera utökad offlinefunktionerna upprätta du en överordnad-underordnad-relation mellan en IoT Edge-gatewayenhet och efterföljande enheter som ska ansluta till den. De här stegen beskrivs i detalj i [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet som fungerar som en transparent gateway, måste du konfigurera dina underordnade enheter för att lita på gatewayen och skicka meddelanden till den. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md) och [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).
