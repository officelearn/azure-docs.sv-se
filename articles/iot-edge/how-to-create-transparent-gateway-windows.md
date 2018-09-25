---
title: Skapa en transparent gateway med Azure IoT Edge – Windows | Microsoft Docs
description: Använd Azure IoT Edge för att skapa en transparent gateway som kan bearbeta information för flera enheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e9de037f886db7a48411959ef62e1e6687e54beb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984304"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Skapa en Windows IoT Edge-enhet som fungerar som en transparent gateway

Den här artikeln innehåller detaljerade anvisningar för att använda en IoT Edge-enhet som en transparent gateway. I resten av den här artikeln termen *IoT Edge-gateway* refererar till en IoT Edge-enhet som används som en transparent gateway. Mer information finns i [hur en IoT Edge-enhet kan användas som en gateway][lnk-edge-as-gateway], vilket ger en översikt.

>[!NOTE]
>För närvarande:
> * Om gatewayen kopplas bort från IoT Hub kan kan inte underordnade enheter autentisera med gatewayen.
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge-gateways. 
> * Efterföljande enheter kan inte använda filuppladdningen.

Hårda del om hur du skapar en transparent gateway ansluter på ett säkert sätt gatewayen till efterföljande enheter. Azure IoT Edge kan du använda PKI-infrastruktur för att ställa in säkra TLS-anslutningar mellan dessa enheter. I det här fallet vi så att en underordnad enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway.  Om du vill skydda rimliga, bör underordnad enhet bekräftar identiteten hos Edge-enhet eftersom du bara vill att dina enheter som ansluter till din gateway och inte en potentiellt skadliga gateway.

Du kan skapa någon infrastruktur för certifikat som gör det förtroendet som krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du använder för att aktivera [X.509 CA-säkerheten] [ lnk-iothub-x509] i IoT Hub, som innebär att ett X.509 CA-certifikat som är kopplad till en specifik IoT-hubb (IoT hub ägaren CA ), och en serie med certifikat som signerats med den här Certifikatutfärdaren och en CA för Edge-enhet.

![Installationsprogram för gateway][1]

Gatewayen anger certifikatutfärdarcertifikatet Edge-enhet till underordnade enheten under initiering av anslutningen. Underordnad enhet kontrollerar om du vill kontrollera att Edge-enhetens CA-certifikat som har signerats av CA-certifikatet ägare. Den här processen kan underordnade enheten för att kontrollera gatewayen kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikat och installera dem på rätt plats.

## <a name="prerequisites"></a>Förutsättningar
1.  [Installera Azure IoT Edge-körningen] [ lnk-install-windows-x64] på en Windows-enhet som du vill använda som en transparent gateway.

1. Hämta OpenSSL för Windows. Det finns många sätt kan du installera OpenSSL:

   >[!NOTE]
   >Om du redan har OpenSSL som har installerats på din Windows-enhet kan du hoppa över detta steg men se till att `openssl.exe` är tillgängligt i din `%PATH%` miljövariabeln.

   * ladda ned och installera eventuella [från tredje part OpenSSL binärfiler](https://wiki.openssl.org/index.php/Binaries), till exempel från [det här projektet på SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Ladda ned källkoden OpenSSL och skapa de binära filerna på din dator själv eller göra det via [vcpkg](https://github.com/Microsoft/vcpkg). Anvisningarna nedan använder vcpkg att ladda ned källkoden, kompilera och installera OpenSSL på din Windows-dator på mycket enkelt att använda steg.

      1. Navigera till den katalog som där du vill installera vcpkg. Hädanefter kommer vi refererar till den här som $VCPKGDIR. Följ instruktionerna för att ladda ned och installera [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. När vcpkg installeras från powershell-prompten, kör följande kommando för att installera OpenSSL-paketet för Windows x64. Detta brukar ta cirka 5 minuter att slutföra.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Lägg till `$VCPKGDIR\installed\x64-windows\tools\openssl` till din `PATH` miljövariabeln så att den `openssl.exe` filen är tillgänglig för anrop.

1. Navigera till den katalog där du vill ska fungera. Hädanefter kommer vi refererar till den här som $WRKDIR.  Alla filer kommer att skapas i den här katalogen.
   
   CD $WRKDIR

1.  Hämta skript för att generera certifikat som krävs inte är i produktion med följande kommando. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway.

      ```PowerShell
      git clone https://github.com/Azure/azure-iot-sdk-c.git
      ```

1. Kopiera konfigurations-och skript till din arbetskatalog. Du kan dessutom ange env variabeln OPENSSL_CONF att använda konfigurationsfilen openssl_root_ca.cnf.

   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
   ```

1. Aktivera PowerShell att köra skripten genom att köra följande kommando

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Ta med de funktioner som används av skripten i PowerShell-globalt namnområde genom dot-källa med följande kommando
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Kontrollera OpenSSL som har installerats korrekt och kontrollera att det inte att finnas namnkonflikter med befintliga certifikat genom att köra följande kommando. Om det uppstår problem kan bör skriptet beskriva hur du löser dem på datorn.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Skapandet av certifikat
1.  Skapa ägare CA-certifikat och en mellanliggande certifikat. Dessa är placerade i `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1.  Skapa Microsoft Edge-enhet CA-certifikat och privata nyckeln med kommandot nedan.

   >[!NOTE]
   > **INTE** använda ett namn som är samma som gatewayens DNS-värdnamn. Gör klienten certifiering mot dessa certifikat misslyckas.

   ```PowerShell
   New-CACertsEdgeDevice "<gateway device name>"
   ```

## <a name="certificate-chain-creation"></a>Skapandet av certifikatkedja
Skapa en certifikatkedja från ägare CA-certifikat, mellanliggande certifikat och Edge-enhet CA-certifikat med kommandot nedan. Placera det i en kedja-fil kan du enkelt installera den på din Edge-enhet som fungerar som en transparent gateway.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

   Utdata från körningen av skriptet är följande certifikat och nyckel:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Installation på gatewayen
1.  Kopiera följande filer från $WRKDIR var som helst på din Edge-enhet, kommer kallas som $CERTDIR. Hoppa över det här steget om du har genererat certifikaten på din Edge-enhet.

   * Enhets-CA-certifikat –  `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Enhets-CA-privata nyckel – `$WRKDIR\private\new-edge-device.key.pem`
   * Ägare CA- `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  Ange den `certificate` egenskaper i säkerhet-Daemon yaml konfigurationsfilen till sökvägen där du lade till certifikatet och nyckel.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera EdgeHub till gatewayen
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera moduler till IoT Edge-enheter från molnet. Det här avsnittet har du skapar en till synes tom distribution; Edge Hub är dock automatcially läggs till i alla distributioner även om det finns inga andra moduler som finns. Edge Hub är den enda modul som du behöver på en Edge-enhet att den fungerar som en transparent gateway så att skapa en tom distribution är tillräckligt. 
1. Gå till din IoT-hubb på Azure Portal.
2. Gå till **IoT Edge** och välj din IoT Edge-enhet som du vill använda som en gateway.
3. Välj **Ange moduler**.
4. Välj **Nästa**.
5. I steget **Ange rutter** ska du ha en standardväg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. I steget granska mallen väljer **skicka**.

## <a name="installation-on-the-downstream-device"></a>Installation på den underordnade enheten
En underordnad enhet kan vara program med hjälp av den [Azure IoT-enhetens SDK][lnk-devicesdk], t.ex. den enkla som beskrivs i [ansluta enheten till IoT-hubben med hjälp av .NET] [ lnk-iothub-getstarted]. En underordnad enhet programmet måste lita på den **ägare CA** certifikat för att kunna verifiera TLS-anslutningar till gatewayenheter. Det här steget kan vanligtvis utföras på två sätt: på operativsystemsnivån, eller (för vissa språk) på programnivå.

### <a name="os-level"></a>OS-nivå
Installera det här certifikatet i certifikatarkivet OS kan alla program du använder ägaren CA-certifikat som ett betrott certifikat.

* Ubuntu - här är ett exempel på hur du installerar ett CA-certifikat på en Ubuntu-värd.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Du bör se ett meddelande om ”uppdaterar certifikat i /etc/ssl/certs... 1 lade till 0 bort; gjorts ”.

* Windows - här är ett exempel på hur du installerar ett CA-certifikat på en Windows-värd.
  * På menyn starttypen i ”hantera certifikat”. Detta bör ta fram ett verktyg som kallas `certlm`.
  * Gå till certifikat lokal dator--> betrodda rotcertifikat--> certifikat--> höger Klicka--> alla uppgifter--> Import för att starta guiden Importera certifikat.
  * Följ stegen enligt anvisningarna och importera certifikatet filen $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  * När du är klar visas meddelandet ”importerades”.

### <a name="application-level"></a>Programnivå
Du kan lägga till följande fragment för att lita på ett certifikat i PEM-format för .NET-program. Initiera variabeln `certPath` med `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Underordnade ansluts till gateway
Med en anslutningssträng som refererar till värdnamnet för gateway-enheten måste du initiera enhets-sdk för IoT Hub. Detta görs genom att lägga till den `GatewayHostName` som enhetens anslutningssträng. Här är exempelvis en exempel enhetens anslutningssträng för en enhet som vi läggs den `GatewayHostName` egenskapen:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Det här är ett exempel på kommando vilka tester som allt har ställa in korrekt. Du sohuld ett meddelande som säger ”verifieras OK”.
   >
   >openssl s_client-ansluta mygateway.contoso.com:8883 - CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Meddelanderoutning från underordnade enheter
IoT Edge-körningen kan dirigera meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. På så sätt kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. Den nedan väg skulle användas för att skicka meddelanden från en underordnad enhet med namnet `sensor` till ett Modulnamn `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Referera till den [modulen sammansättning artikeln] [ lnk-module-composition] för mer information om meddelanderoutning.

[!INCLUDE [](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Nästa steg
[Förstå de krav och verktyg för att utveckla IoT Edge-moduler][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-windows-x64]: ./how-to-install-iot-edge-windows-with-windows.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/quickstart-send-telemetry-dotnet.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
