---
title: Skapa en transparent gateway med Azure IoT Edge | Microsoft Docs
description: Använda en Azure IoT Edge-enhet som en transparent gateway som kan bearbeta information för flera enheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a48699507fbba18b20cb94e404c4814f25d31f44
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50915305"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway

Den här artikeln innehåller detaljerade anvisningar för hur du konfigurerar IoT Edge-enheter att fungera som en transparent gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln termen *IoT Edge-gateway* refererar till en IoT Edge-enhet som används som en transparent gateway. Mer information finns i [hur en IoT Edge-enhet kan användas som en gateway](./iot-edge-as-gateway.md), vilket ger en översikt.

>[!NOTE]
>För närvarande:
> * Om gatewayen kopplas bort från IoT Hub kan kan inte underordnade enheter autentisera med gatewayen.
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge-gateways. 
> * Efterföljande enheter kan inte använda filuppladdningen.

För en enhet kan fungera som en gateway krävs för att kunna ansluta säkert till efterföljande enheter. Azure IoT Edge kan du använda en public key infrastructure (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet vi så att en underordnad enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. Om du vill skydda rimliga, bör underordnad enhet bekräftar identiteten hos Edge-enhet eftersom du bara vill att dina enheter som ansluter till din gateway och inte en potentiellt skadliga gateway.

En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på IoT Edge-gatewayenhet själva för alla praktiska syften. 

Du kan skapa någon infrastruktur för certifikat som gör det förtroendet som krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du använder för att aktivera [X.509 CA-säkerheten](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, som omfattar ett X.509 CA-certifikat som är kopplad till en specifik IoT-hubb (IoT hub ägaren CA) och en serie med certifikat registrerat med den här Certifikatutfärdaren och en Certifikatutfärdare för Edge-enhet.

![Installationsprogram för gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Gatewayen anger certifikatutfärdarcertifikatet Edge-enhet till underordnade enheten under initiering av anslutningen. Underordnad enhet kontrollerar om du vill kontrollera att Edge-enhetens CA-certifikat som har signerats av CA-certifikatet ägare. Den här processen kan underordnade enheten för att kontrollera gatewayen kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikat och installera dem på rätt plats.

## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet för att konfigurera som en gateway. Du kan använda utvecklingsdatorn eller en virtuell dator som en IoT Edge-enhet med steg för följande operativsystem:
* [Windows](./how-to-install-iot-edge-windows-with-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

Du kan använda valfri dator för att generera certifikat och kopiera dem sedan till din IoT Edge-enhet. 

## <a name="generate-certificates-with-windows"></a>Generera certifikat med Windows

Använd stegen i det här avsnittet för att generera testcertifikat på en Windows-enhet. Du kan generera certifikaten på din IoT Edge-enhet, eller använda en separat dator och kopiera de slutliga certifikat till alla IoT Edge-enheter som kör ett operativsystem som stöds. 

De certifikat som genereras i det här avsnittet är avsedda för endast i testningssyfte. 

### <a name="install-openssl"></a>Installera OpenSSL

Installera OpenSSL för Windows på den dator som du använder för att generera certifikat. Det finns flera sätt kan du installera OpenSSL:

   >[!NOTE]
   >Om du redan har OpenSSL som har installerats på din Windows-enhet kan du hoppa över detta steg men se till att openssl.exe är tillgänglig i miljövariabeln PATH.

* **Enklare:** ladda ned och installera eventuella [från tredje part OpenSSL binärfiler](https://wiki.openssl.org/index.php/Binaries), till exempel från [det här projektet på SourceForge](https://sourceforge.net/projects/openssl/). Lägga till den fullständiga sökvägen till openssl.exe till SÖKVÄGEN för miljövariabeln. 
   
* **Rekommenderat:** ladda ned källkoden OpenSSL och skapa de binära filerna på din dator själv eller via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg att ladda ned källkoden, kompilera och installera OpenSSL på din Windows-dator med enkla steg.

   1. Navigera till den katalog som där du vill installera vcpkg. Vi ska referera till den här katalogen som  *\<VCPKGDIR >*. Följ instruktionerna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).
   
   2. När vcpkg installeras från powershell-prompten, kör följande kommando för att installera OpenSSL-paketet för Windows x64. Installationen tar vanligtvis cirka 5 minuter att slutföra.

      ```PowerShell
      .\vcpkg install openssl:x64-windows
      ```
   3. Lägg till `<VCPKGDIR>\installed\x64-windows\tools\openssl` till SÖKVÄGEN för miljövariabeln så att filen openssl.exe är tillgänglig för anrop.

### <a name="prepare-creation-scripts"></a>Förbereda skripten för att skapa

Azure IoT-enhetens SDK för C innehåller skript som du kan använda för att generera testcertifikat. I det här avsnittet ska du klona SDK och konfigurerar PowerShell.

1. Öppna ett PowerShell-fönster i administratörsläge. 

2. Klona git-lagringsplats som innehåller skript för att generera certifikat för icke-produktion. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway. Använd den `git clone` kommando eller [ladda ned ZIP-filen](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip). 

   ```PowerShell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Navigera till den katalog där du vill ska fungera. Vi ska referera till den här katalogen som  *\<WRKDIR >*.  Alla filer kommer att skapas i den här katalogen.

4. Kopiera konfigurations-och skript till din arbetskatalog. 

   ```PowerShell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   Om du har laddat ned SDK: N som en ZIP så mappnamnet är `azure-iot-sdk-c-master` och resten av sökvägen är samma. 

5. Ange miljövariabeln OPENSSL_CONF att använda konfigurationsfilen openssl_root_ca.cnf.

    ```PowerShell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. Aktivera PowerShell att köra skripten.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Ta med de funktioner som används av skripten i PowerShell-globalt namnområde.
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

8. Kontrollera att OpenSSL har installerats korrekt och kontrollera att det inte att finnas namnkonflikter med befintliga certifikat. Om det uppstår problem kan bör skriptet beskriva hur du löser dem på datorn.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>Skapa certifikat

I det här avsnittet ska du skapa tre certifikat och Anslut dem i en kedja. Placera certifikaten i en kedja-fil kan du enkelt installera dem på din IoT Edge-gatewayenhet och alla underordnade enheter.  

1. Skapa ägare CA-certifikatet och den signera en mellanliggande certifikat. Certifikat som är placerade i  *\<WRKDIR >*.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

2. Skapa Microsoft Edge-enhet CA-certifikat och privata nyckeln med följande kommando. Ange ett namn för gateway-enheten som ska användas som namn på filerna och under skapande av certifikat. 

   ```PowerShell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. Skapa en certifikatkedja från ägare CA-certifikat, mellanliggande certifikat och Edge-enhet CA-certifikat med följande kommando. 

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   Skriptet skapar följande certifikat och nyckel:
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Generera certifikat med Linux

Använd stegen i det här avsnittet för att generera testcertifikat på en Linux-enhet. Du kan generera certifikaten på din IoT Edge-enhet, eller använda en separat dator och kopiera de slutliga certifikat till alla IoT Edge-enheter som kör ett operativsystem som stöds. 

### <a name="prepare-creation-scripts"></a>Förbereda skripten för att skapa

1. Klona git-lagringsplats som innehåller skript för att generera certifikat för icke-produktion. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. Navigera till den katalog där du vill ska fungera. Vi ska referera till den här katalogen som  *\<WRKDIR >*.  Alla filer kommer att skapas i den här katalogen.
  
3. Kopiera filerna konfigurations- och skript till din arbetskatalog.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. Konfigurera OpenSSL för att generera certifikat med hjälp av skriptet. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>Skapa certifikat

I det här avsnittet ska du skapa tre certifikat och Anslut dem i en kedja. Placera certifikaten i en kedja-fil kan enkelt installera dem på din IoT Edge-gatewayenhet och alla underordnade enheter.  

1.  Skapa ägare CA-certifikat och en mellanliggande certifikat. Dessa certifikat är placerade i  *\<WRKDIR >*.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Skriptet skapar följande certifikat och nycklar:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2.  Skapa Microsoft Edge-enhet CA-certifikat och privata nyckeln med följande kommando. Ange ett namn för gateway-enheten som ska användas som namn på filerna och under skapande av certifikat. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   Skriptet skapar följande certifikat och nyckel:
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. Skapa en certifikatkedja som kallas **nya-edge-enhet-full-chain.cert.pem** från ägare CA-certifikat, mellanliggande certifikat och certifikat för Edge-enhet CA: N.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>Installera certifikat på gateway

Nu när du har gjort en certifikatkedja, måste du installera den på IoT Edge-gatewayenhet och konfigurerar IoT Edge-körningen för att referera till de nya certifikaten. 

1. Kopiera följande filer från  *\<WRKDIR >*. Spara dem var som helst på din IoT Edge-enhet. Vi ska referera till målkatalogen på IoT Edge-enhet som  *\<CERTDIR >*. 

   Om du har genererat certifikaten på själva Edge-enheten kan du hoppa över detta steg och använda sökvägen till arbetskatalogen.

   * Enhets-CA-certifikat –  `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * Enhets-CA-privata nyckel – `<WRKDIR>\private\new-edge-device.key.pem`
   * Ägare CA- `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Öppna konfigurationsfilen för IoT Edge security daemon. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Ange den **certifikat** egenskaper i filen config.yaml till sökvägen där du lade till certifikat och nyckel filerna på IoT Edge-enhet.

```yaml
certificates:
  device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera EdgeHub till gatewayen

När du först installera IoT Edge på en enhet, endast en systemmodulen startas automatiskt: Edge-agenten. För enheten att fungera som en gateway, måste både systemmoduler. Om du inte har distribuerat alla moduler till din gatewayenhet innan du kan skapa en distribution för din enhet att starta andra systemmodulen Edge hub. Distributionen ser tomt eftersom du inte lägger till alla moduler i guiden, men båda systemmoduler kommer att distribueras. 

Du kan kontrollera vilka moduler som körs på en enhet med kommandot `iotedge list`.

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

## <a name="route-messages-from-downstream-devices"></a>Skicka meddelanden från underordnade enheter
IoT Edge-körningen kan dirigera meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. På så sätt kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. Den nedan väg skulle användas för att skicka meddelanden från en underordnad enhet med namnet `sensor` till ett Modulnamn `ai_insights`.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Läs mer om meddelanderoutning [modulsammansättningen](./module-composition.md).

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet som fungerar som en transparent gateway, måste du konfigurera dina underordnade enheter för att lita på gatewayen och skicka meddelanden till den. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
