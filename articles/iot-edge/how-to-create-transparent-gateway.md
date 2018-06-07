---
title: Skapa en transparent gateway-enhet med Azure IoT kant | Microsoft Docs
description: Använd Azure IoT kant för att skapa en transparent gateway-enhet som kan bearbeta information för flera enheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fa4a2f6757a2a9dd114ea332b409759b0d4f4896
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630654"
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>Skapa en insticksenhet för IoT som fungerar som en transparent gateway - förhandsgranskning

Den här artikeln innehåller detaljerade anvisningar för att använda en insticksenhet för IoT som en transparent gateway. I resten av den här artikeln termen *IoT gräns-gatewayen* refererar till en insticksenhet för IoT som används som en transparent gateway. Mer information finns i [hur en IoT-enhet kan användas som en gateway][lnk-edge-as-gateway], vilket ger en översikt. 

>[!NOTE]
>För närvarande:
> * Om gatewayen kopplas från IoT-hubb autentisera efterföljande enheter inte med gatewayen.
> * IoT-gränsenheterna kan inte ansluta till IoT gräns-gateways.

## <a name="understand-the-azure-iot-device-sdk"></a>Förstå Azure IoT-enhet SDK


Edge-hubb som är installerad på alla IoT-gränsenheterna visar följande primitiver till underordnade enheter:

* enhet till moln-och moln till enhet
* direkt-metoder
* enheten dubbla åtgärder

Underordnade enheter är för närvarande inte kan använda ladda upp filen när du ansluter via en IoT-gräns-gatewayen.

När du ansluta enheter till en IoT-gräns-gatewayen använder Azure IoT-enhet SDK behöver du:

* Konfigurera underordnade enheten med en anslutningssträng som hänvisar till gateway-enhet hostname; och
* Kontrollera att den underordnade enheten litar på certifikatet som används för att godkänna anslutningen som gateway-enheten.

När du installerar Azure IoT kant körningsmiljön med hjälp av skript för kontrollen skapas ett certifikat för Edge-hubb som du gjorde i kursen [installera IoT kanten på en simulerad enhet i Windows] [ lnk-tutorial1-win] och [Linux][lnk-tutorial1-lin]. Det här certifikatet används av Edge hubben för att acceptera inkommande TLS-anslutningar och måste vara betrott av underordnade enheten när du ansluter till gateway-enheten.

Du kan skapa alla infrastrukturen för certifikat som möjliggör förtroende krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du vill använda för att aktivera [X.509 CA-säkerheten] [ lnk-iothub-x509] i IoT-hubb som innefattar ett X.509-CA-certifikat som är associerade med en specifik IoT-hubb ( *IoT-hubb ägare CA*), och en serie med certifikat som signerats med den här Certifikatutfärdaren installeras i kanten av IoT-enheter.

>[!IMPORTANT]
>För närvarande IoT-gränsenheterna och efterföljande enheter kan bara använda [SAS-token] [ lnk-iothub-tokens] att autentisera med IoT-hubben. Certifikaten används bara för att verifiera TLS-anslutningen mellan lägsta och gateway-enhet.

Vår konfiguration använder **IoT-hubb ägare CA** både som:
* Ett signeringscertifikat för installationen av körningsmiljön IoT kanten på alla IoT-gränsenheterna; och
* Certifikatets offentliga nyckel installerad i efterföljande enheter.

Detta resulterar i en lösning som gör att alla enheter ska använda en IoT-enhet som en gateway, så länge de är anslutna till samma IoT-hubben.

## <a name="create-the-certificates-for-test-scenarios"></a>Skapa certifikat för testscenarier

Du kan använda exempel Powershell och Bash-skript som beskrivs i [hantera CA-certifikat exempel] [ lnk-ca-scripts] att generera ett självsignerat **IoT-hubb ägare CA** och enhetscertifikat signeras med den.

>[!IMPORTANT]
>Det här exemplet är avsedd endast för testning. Scenarier för produktion, finns i [säkra din IoT-distribution] [ lnk-iothub-secure-deployment] Azure IoT-riktlinjer att skydda din IoT-lösning och därefter etablera ditt certifikat.


1. Klona Microsoft Azure IoT-SDK: er och bibliotek för C från GitHub:

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. Om du vill installera certifikatet skript, följ instruktionerna i **steg 1 - installationen** av [hantera CA-certifikat exempel][lnk-ca-scripts]. 
3. Att generera den **IoT-hubb ägare CA**, följ instruktionerna i **steg 2 – skapa certifikatkedjan**. Den här filen används av underordnade enheter för att verifiera anslutningen.
4. Använd antingen Bash eller PowerShell-instruktioner för att generera ett certifikat för gateway-enhet:

### <a name="bash"></a>Bash

Skapa det nya enhetscertifikatet.  **INTE** namn på `myGatewayCAName` ska vara detsamma som namnet på din gateway-värd.  Gör klientcertifiering mot dessa certifikat misslyckas.

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

Nya filer skapas:.\certs\new-edge-device.* innehåller den offentliga nyckeln och PFX och.\private\new-edge-device.key.pem innehåller enhetens privat nyckel.
 
I den `certs` directory, kör följande kommando för att få fullständig kedja av den offentliga nyckeln för enhet:

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

Skapa det nya enhetscertifikatet: 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

MyEdgeDevice * skapas nya filer, som innehåller den offentliga nyckel och privat nyckel PFX av det här certifikatet. 

När du uppmanas att ange ett lösenord under signeringsprocessen, anger du ”1234”.

## <a name="configure-a-gateway-device"></a>Konfigurera en gateway-enhet

För att kunna konfigurera din IoT-Edge-enhet som en gateway måste du konfigurera om du vill använda certifikat för enhet som skapats i föregående avsnitt.

Vi förutsätter följande namn från skriptexemplen ovan:

| Resultat | Filnamn |
| ------ | --------- |
| Certifikat för enhet | `certs/new-edge-device.cert.pem` |
| Privata nyckel | `private/new-edge-device.cert.pem` |
| Certifikatkedja för enhet | `certs/new-edge-device-full-chain.cert.pem` |
| IoT-hubb ägare Certifikatutfärdare | `certs/azure-iot-test-only.root.ca.cert.pem`  |

Ger information för enheten och certifikat till IoT kant-körningsmiljön. 
 
I Linux, använder du Bash utdata:

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

I Windows, använder du PowerShell-utdata:

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

Som standard ange skriptexemplen inte en lösenfras till den privata nyckeln för enheten. Om du ställer in en lösenfras, lägger du till följande parameter: `--device-ca-passphrase {passphrase}`.

Skriptet uppmanas du att ange en lösenfras för Edge agentcertifikatet. Starta om IoT kant runtime efter det här kommandot:

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>Konfigurera en underordnad enhet

En underordnad enhet kan vara ett program med hjälp av den [Azure IoT-enhet SDK][lnk-devicesdk], till exempel den enkla som beskrivs i [ansluta enheten till din IoT-hubb med hjälp av .NET] [ lnk-iothub-getstarted].

Först en underordnad enhetsprogram ha förtroende den **IoT-hubb ägare CA** certifikat för att kunna verifiera TLS-anslutningar till gatewayenheter. Det här steget kan vanligtvis utföras på två sätt: på OS-nivå, eller (för vissa språk) på programnivå.

Till exempel .NET-program kan du lägga till följande fragment för att lita på ett certifikat i PEM-format som lagras i sökvägen `certPath`. Beroende på vilken version av skriptet som du använde sökvägen refererar till antingen `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) eller `RootCA.pem` (Powershell).

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

Den här åtgärden på nivån OS skiljer sig mellan Windows och Linux-distributioner.

Det andra steget är att initiera sdk för IoT-hubb-enhet med en anslutningssträng som hänvisar till värdnamnet för gateway-enheten.
Detta görs genom att lägga till den `GatewayHostName` egenskapen för anslutningssträngen enhet. Till exempel här är exempel anslutningssträngen för enheten för en enhet som vi läggs den `GatewayHostName` egenskapen:

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

Dessa två steg aktivera tillämpningsprogrammet enhet att ansluta till gatewayenheten.

## <a name="next-steps"></a>Nästa steg
[Förstå de krav och verktyg för att utveckla IoT kant moduler][lnk-module-dev].

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
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
