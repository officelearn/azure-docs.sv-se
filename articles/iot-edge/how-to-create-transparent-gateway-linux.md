---
title: Skapa en transparent gateway med Azure IoT kant - Linux | Microsoft Docs
description: Använd Azure IoT kant för att skapa en transparent gateway som kan bearbeta information för flera enheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5a78d6fb8ee52f0daba80a77cc8a5e75c2e5248d
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036520"
---
# <a name="create-a-linux-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Skapa en insticksenhet i Linux IoT som fungerar som en transparent gateway

Den här artikeln innehåller detaljerade anvisningar för att använda en insticksenhet för IoT som en transparent gateway. I resten av den här artikeln termen *IoT gräns-gatewayen* refererar till en insticksenhet för IoT som används som en transparent gateway. Mer information finns i [hur en IoT-enhet kan användas som en gateway][lnk-edge-as-gateway], vilket ger en översikt.

>[!NOTE]
>För närvarande:
> * Om gatewayen kopplas från IoT-hubb autentisera efterföljande enheter inte med gatewayen.
> * IoT-gränsenheterna kan inte ansluta till IoT gräns-gateways.
> * Efterföljande enheter kan inte använda för filöverföring.

Hårda del om hur du skapar en transparent gateway ansluter på ett säkert sätt gateway till underordnade enheter. Azure IoT-gräns kan du använda PKI-infrastruktur för att ställa in säkra TLS-anslutningar mellan dessa enheter. I det här fallet det så att underordnade enheten ska ansluta till en insticksenhet för IoT som fungerar som en transparent gateway.  Om du vill skydda rimliga, ska underordnade enheten bekräfta identiteten på gränsenheten eftersom du bara vill att enheterna ansluter till din gateway och inte en gateway som skadlig.

Du kan skapa alla infrastrukturen för certifikat som möjliggör förtroende krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du vill använda för att aktivera [X.509 CA-säkerheten] [ lnk-iothub-x509] i IoT-hubb som innefattar ett X.509-CA-certifikat som är associerade med en specifik IoT-hubb (IoT-hubb ägaren Certifikatutfärdare ), och en serie med certifikat som signerats med den här Certifikatutfärdaren och en Certifikatutfärdare för gränsenheten.

![Installationsprogram för gateway][1]

Gatewayen visas certifikatutfärdarcertifikatet Edge enheten till den underordnade enheten under initiering av anslutningen. Om du vill kontrollera Edge enhetens Certifikatutfärdarens certifikat har signerats av CA-certifikatet ägare enhetskontroller underordnade. Den här processen kan underordnade enheten för att kontrollera gatewayen som kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikat och installera dem på rätt plats.

## <a name="prerequisites"></a>Förutsättningar
1.  Installera Azure IoT kant-körning på en Linux-enhet som du vill använda som genomskinlig gateway.
   * [Linux x64][lnk-install-linux-x64]
   * [Linux ARM32][lnk-install-linux-arm]

2.  Hämta skript för att generera certifikat som krävs icke-produktion med följande kommando. Dessa skript hjälper dig att skapa nödvändiga certifikat att ställa in en transparent gateway. 

   ```cmd
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. Dessa skript använda OpenSSL för att generera certifikat som krävs och OpenSSL kräver vissa inställningar.
   
   1. Gå till den katalog där du vill arbeta. Hädanefter ska vi refererar till detta som $WRKDIR.  Alla filer kommer att skapas i den här katalogen.

      CD $WRKDIR
   
   1. Kopiera config och skript i arbetskatalogen.

      ```cmd
      cp azure-iot-sdk-c/tools/CACertificates/*.cnf .
      cp azure-iot-sdk-c/tools/CACertificates/certGen.sh .
      chmod 700 certGen.sh 
      ```

## <a name="certificate-creation"></a>Skapande av certifikat
1.  Skapa ägare CA-certifikat och en mellanliggande certifikat. Dessa är placerade i `$WRKDIR`.

   ```cmd
   ./certGen.sh create_root_and_intermediate
   ```

   Utdata för körningen av skriptet är följande certifikat och nycklar:
   * Certifikat
      * `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`
      * `$WRKDIR/certs/azure-iot-test-only.intermediate.cert.pem`
   * Nycklar
      * `$WRKDIR/private/azure-iot-test-only.root.ca.key.pem`
      * `$WRKDIR/private/azure-iot-test-only.intermediate.key.pem`

2.  Skapa Edge enheten CA-certifikat och privata nyckeln med kommandot nedan.

   >[!NOTE]
   > **INTE** Använd ett namn som är samma som DNS-värdnamn för denna gateway. Gör klientcertifiering mot dessa certifikat misslyckas.

      ```cmd
      ./certGen.sh create_edge_device_certificate "<gateway device name>"
      ```

   Utdata för körningen av skriptet är följande certifikat och nyckel:
   * `$WRKDIR/certs/new-edge-device.*`
   * `$WRKDIR/private/new-edge-device.key.pem`

## <a name="certificate-chain-creation"></a>Skapande av kedja för certifikat
Skapa en certifikatkedja från ägare CA-certifikat, mellanliggande certifikat och Edge enheten CA-certifikat med kommandot nedan. Placera den i en kedja-fil kan du enkelt installera den på enheten kant som fungerar som en transparent gateway.

   ```cmd
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="installation-on-the-gateway"></a>Installation på gateway
1.  Kopiera följande filer från $WRKDIR var som helst på Edge-enhet, kommer kallas som $CERTDIR. Hoppa över det här steget om du genererade certifikat på enheten kant.

   * Enheten certifikatutfärdarcertifikat-  `$WRKDIR/certs/new-edge-device-full-chain.cert.pem`
   * Enheten privat certifikatutfärdarnyckel- `$WRKDIR/private/new-edge-device.key.pem`
   * Ägare CA- `$WRKDIR/certs/azure-iot-test-only.root.ca.cert.pem`

2.  Ange den `certificate` egenskaper i säkerhet Daemon yaml konfigurationsfilen till sökvägen där du lade certifikatet och nyckel.

```yaml
certificates:
  device_ca_cert: "$CERTDIR/certs/new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR/private/new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem"
```

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera EdgeHub till gatewayen
En av de viktigaste funktionerna i Azure IoT Edge är möjligheten att distribuera moduler till IoT Edge-enheter från molnet. Det här avsnittet har du skapar en tom till synes distribution; men Edge navet är automatcially läggs till i alla distributioner även om det inte finns några moduler som finns. Edge-hubben är endast modulen behöver på en insticksenhet den fungerar som en transparent gateway så att skapa en tom distribution är tillräckligt. 
1. Gå till din IoT-hubb på Azure Portal.
2. Gå till **IoT kant** och välj din IoT-Edge-enhet som du vill använda som en gateway.
3. Välj **Ange moduler**.
4. Välj **Nästa**.
5. I den **ange vägar** steg, bör du ha en standardväg som skickar alla meddelanden från alla moduler till IoT-hubb. Om inte, Lägg till följande kod och välj sedan **nästa**.
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. I steget granska mallen väljer **skicka**.

## <a name="installation-on-the-downstream-device"></a>Installation på den underordnade enheten
En underordnad enhet kan vara ett program med hjälp av den [Azure IoT-enhet SDK][lnk-devicesdk], till exempel den enkla som beskrivs i [ansluta enheten till din IoT-hubb med hjälp av .NET] [ lnk-iothub-getstarted]. En underordnad enhetsprogram ha förtroende den **ägare CA** certifikat för att kunna verifiera TLS-anslutningar till gatewayenheter. Det här steget kan vanligtvis utföras på två sätt: på OS-nivå, eller (för vissa språk) på programnivå.

### <a name="os-level"></a>OS-nivå
Installera certifikatet i certifikatarkivet OS kan alla program att använda ägaren CA-certifikat som ett betrott certifikat.

* Ubuntu - här är ett exempel på hur du installerar en certifikatutfärdare på en virtuell Ubuntu-värd.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Du bör se ett meddelande om ”uppdaterar certifikat i /etc/ssl/certs... 1 lade till 0 bort; Utfärdat ”.

* Windows - [detta](https://msdn.microsoft.com/en-us/library/cc750534.aspx) artikeln beskriver hur du gör detta på en Windows-enhet med hjälp av certifikatet importera wizzard. 

### <a name="application-level"></a>Programnivå
Du kan lägga till följande fragment för att lita på ett certifikat i PEM-format för .NET-program. Initiera variabeln `certPath` med `$CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Ansluta den underordnade till gatewayen
IoT-hubb enheten sdk måste du initiera en anslutningssträng som hänvisar till värdnamnet för gateway-enheten. Detta görs genom att lägga till den `GatewayHostName` egenskapen för anslutningssträngen enhet. Till exempel här är exempel anslutningssträngen för enheten för en enhet som vi läggs den `GatewayHostName` egenskapen:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Det här är ett exempel på kommando vilka tester som allt har korrekt. Du sohuld meddelandet ”verifieras OK”.
   >
   >openssl s_client-ansluta mygateway.contoso.com:8883 - CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem - showcerts

## <a name="routing-messages-from-downstream-devices"></a>Vidarebefordra meddelanden från underordnade enheter
IoT-Edge-körning kan vidarebefordra meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. På så sätt kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. Den nedan vägen används för att skicka meddelanden från en underordnad enhet med namnet `sensor` till ett Modulnamn `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Referera till [modulen sammansättning artikel] [lnk--modulsammansättningen] för mer information om routning av meddelanden.

## <a name="next-steps"></a>Nästa steg
[Förstå de krav och verktyg för att utveckla IoT kant moduler][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-linux-x64]: ./how-to-install-iot-edge-linux.md
[lnk-install-linux-arm]: ./how-to-install-iot-edge-linux-arm.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
