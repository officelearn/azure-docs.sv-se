---
title: Skapa en gateway-enhet med Azure IoT kant | Microsoft Docs
description: "Använd Azure IoT kant för att skapa en gateway-enhet som kan bearbeta information för flera enheter"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e1337ddf5ed84a06a62e2faa198f3e8fb49bc3bd
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-an-iot-edge-gateway-device-to-process-data-from-other-iot-devices---preview"></a>Skapa en IoT-Edge-gateway för bearbetning av data från andra IoT-enheter – förhandsgranskning

Det finns två sätt att använda IoT-gränsenheterna som gateway:

* Ansluta underordnade enheter som använder [Azure IoT-enhet SDK][lnk-devicesdk];
* Ansluta enheter som inte använder protokollet IoT-hubb.

När du använder en IoT-enhet som en gateway kan du få följande fördelar:

* **Anslutningen multiplexering**. Alla enheter som ansluter till IoT-hubb via en IoT-enhet ska använda samma underliggande anslutningen.
* **Trafik Utjämning**. IoT gränsenheten implementerar automatiskt exponentiell backoff vid IoT-hubb begränsning vid beständighet meddelanden lokalt. Detta gör din lösning flexibel till toppar i trafiken.
* **Begränsad offlinestöd**. Gateway-enheten lagras lokalt meddelanden som inte levereras till IoT-hubben.

I den här artikeln ska vi kallar *IoT gräns-gatewayen* en insticksenhet för IoT som används som en gateway.

>[!NOTE]
>För närvarande:
> * Underordnade enheter kan inte autentisera med gatewayen om gatewayen kopplas bort från IoT-hubb; och
> * IoT-gränsenheterna kan inte ansluta till IoT gräns-gateways.

## <a name="use-the-azure-iot-device-sdk"></a>Använd SDK för Azure IoT-enhet

Edge-hubb som är installerad på alla IoT-gränsenheterna visar följande primitiver till underordnade enheter:

* enhet till moln och moln till enhet-meddelanden.
* direkta metoder. och
* enheten dubbla åtgärder.

>[!NOTE]
>Underordnade enheter är för närvarande inte kan använda ladda upp filen när du ansluter via en IoT-gräns-gatewayen.

När du ansluta enheter till en IoT-gräns-gatewayen använder Azure IoT-enhet SDK behöver du:

* Konfigurera underordnade enheten med en anslutningssträng som hänvisar till gateway-enhet hostname; och
* Kontrollera att den underordnade enheten litar på certifikatet som används för att godkänna anslutningen som gateway-enheten.

När du installerar Azure IoT kant körningsmiljön med hjälp av skript för kontrollen skapas ett certifikat för kant-hubb som du gjorde i självstudiekursen installera IoT kanten på en simulerad enhet på [Windows] [ lnk-tutorial1-win] och [Linux][lnk-tutorial1-lin]. Det här certifikatet används av Edge hubben för att acceptera inkommande TLS-anslutningar och måste vara betrott av underordnade enheten när du ansluter till gateway-enheten.

Du kan skapa alla infrastrukturen för certifikat som möjliggör förtroende krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi att samma inställningar för certifikat som du vill använda för att aktivera [X.509 CA-säkerheten] [ lnk-iothub-x509] i IoT-hubb som innefattar ett X.509-CA-certifikat som är associerade med en specifik IoT-hubb (den  *IoT-hubb ägare CA*), och en serie med certifikat som signerats med den här Certifikatutfärdaren installeras i kanten av IoT-enheter.

>[!IMPORTANT]
>För närvarande IoT-gränsenheterna och efterföljande enheter kan bara använda [SAS-token] [ lnk-iothub-tokens] att autentisera med IoT-hubben. Certifikaten används bara för att verifiera TLS-anslutningen mellan lägsta och gateway-enhet.

Vår konfiguration använder **IoT-hubb ägare CA** som:
* ett signeringscertifikat för installationen av körningsmiljön IoT kanten på alla IoT-gränsenheterna och som
* Certifikatets offentliga nyckel installerad i efterföljande enheter.

Detta resulterar i en lösning som gör att alla enheter ska använda en IoT-enhet som en gateway, så länge de är anslutna till samma IoT-hubben.

### <a name="create-the-certificates-for-test-scenarios"></a>Skapa certifikat för testscenarier

Du kan använda exempel Powershell och Bash-skript som beskrivs i [hantera CA-certifikat exempel] [ lnk-ca-scripts] att generera ett självsignerat **IoT-hubb ägare CA** och enhetscertifikat signeras med den.

1. Följ steg 1 av [hantera CA-certifikat exempel] [ lnk-ca-scripts] installera skripten.
2. Följ steg 2 för att generera den **IoT-hubb ägare CA**, den här filen kommer att användas av efterföljande enheter för att verifiera anslutningen.

Använd följande instruktioner för att generera ett certifikat för gateway-enhet.

#### <a name="bash"></a>Bash

* Kör `./certGen.sh create_edge_device_certificate myGateway` att skapa det nya enhetscertifikatet.  
  Detta skapar filer.\certs\new-edge-device.* som innehåller den offentliga nyckeln och PFX och.\private\new-edge-device.key.pem som innehåller enhetens privata nyckeln.  
* `cat new-edge-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-edge-device-full-chain.cert.pem`att hämta den offentliga nyckeln.
* `./private/new-edge-device.cert.pem`innehåller enhetens privat nyckel.

#### <a name="powershell"></a>PowerShell

* Kör `New-CACertsEdgeDevice myGateway` att skapa det nya enhetscertifikatet.
  Detta skapar filer myEdgeDevice * som innehåller den offentliga nyckel och privat nyckel PFX av det här certifikatet.  När du uppmanas att ange ett lösenord under signeringsprocessen, anger du ”1234”.


>[!IMPORTANT]
>Det här exemplet är avsedd endast för testning. Scenarier för produktion, finns i [säkra din IoT-distribution] [ lnk-iothub-secure-deployment] Azure IoT-riktlinjer att skydda din IoT-lösning och därefter etablera ditt certifikat.

### <a name="configure-an-iot-edge-device-as-a-gateway"></a>Konfigurera en IoT-enhet som en gateway

För att kunna konfigurera din IoT-Edge-enhet som en gateway måste du konfigurera om du vill använda certifikat för enhet som skapats i föregående avsnitt.

Vi förutsätter följande namn från skriptexemplen ovan:

| Resultat | Bash-skript | PowerShell |
| ------ | ----------- | ---------- |
| Certifikat för enhet | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Privata nyckel | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Certifikatkedja för enhet | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| IoT-hubb ägare Certifikatutfärdare | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 På motsvarande sätt till installationen som beskrivs i distribuera Azure IoT kanten på en simulerad enhet i [Windows] [ lnk-tutorial1-win] eller [Linux][lnk-tutorial1-lin], du behöver Ange ovanstående information till IoT kant-körningsmiljön. 
 
 I Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

I Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

Som standard ange skriptexemplen inte en lösenfras till den privata nyckeln för enheten. Om du anger en lösenfras, lägger du till följande parameter:

        --device-ca-passphrase {passphrase}

Skriptet uppmanas för att ange en lösenfras för Edge agentcertifikatet.
Du måste starta om IoT kant runtime efter det här kommandot.

### <a name="configure-an-iot-hub-device-application-as-a-downstream-device"></a>Konfigurera ett program för IoT Hub-enhet som en underordnad enhet

En underordnad enhet kan vara ett program med hjälp av den [Azure IoT-enhet SDK][lnk-devicesdk], till exempel den enkla som beskrivs i [ansluta enheten till din IoT-hubb med hjälp av .NET] [ lnk-iothub-getstarted].

Först en underordnad enhetsprogram ha förtroende den **IoT-hubb ägare CA** certifikat för att kunna verifiera TLS-anslutningar till gatewayenheter. Det här steget kan vanligtvis utföras på två sätt: på OS-nivå, eller (för vissa språk) på programnivå.

Till exempel .NET-program kan du lägga till följande fragment för att lita på ett certifikat i PEM-format som lagras i sökvägen `certPath`.

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Observera att exempelskript som anges ovan genererar den offentliga nyckeln i filen `certs/azure-iot-test-only.root.ca.cert.pem` (Bash) eller `RootCA.pem` (Powershell).

Den här åtgärden på nivån OS skiljer sig mellan Windows och Linux-distributioner.

Det andra steget är att initiera sdk för IoT-hubb-enhet med en anslutningssträng som hänvisar till värdnamnet för gateway-enheten.
Detta görs genom att lägga till den `GatewayHostName` egenskapen för anslutningssträngen enhet. Till exempel här är exempel anslutningssträngen för enheten för en enhet som vi läggs den `GatewayHostName` egenskapen:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

Dessa två steg aktiverar enhet programmet att ansluta till gatewayenheten.

## <a name="use-other-protocols"></a>Använda andra protokoll

En av huvudfunktionerna i en gateway-enhet är att tolka protokoll för efterföljande enheter. För att ansluta enheter som inte använder protokollet IoT-hubb, kan du utveckla en IoT-Edge-modul som utför den här översättning och ansluter uppdrag underordnade enhet till IoT-hubben.

Du kan välja att skapa en *transparent* eller *täckande* gateway:

| &nbsp; | Transparent gateway | Täckande gateway|
|--------|-------------|--------|
| Identiteter som lagras i registret för IoT-hubb-identitet | Identiteten för alla anslutna enheter. | Endast identiteten för gateway-enhet |
| Enhetstvilling | Varje enhet har sin egen enhet dubbla | Endast gateway har en enhet och modulen twins |
| Direkta metoder och meddelanden moln till enhet | Molnet kan adressera varje ansluten enhet individuellt | Molnet kan bara adressera gateway-enhet |
| [IoT-hubb begränsningar och kvoter][lnk-iothub-throttles-quotas] | Gäller för varje enhet | Gäller för gateway-enhet |

När du använder ett mönster för täckande gateway, dela alla enheter som ansluter via denna gateway samma kö från moln till enhet, som kan innehålla högst 50 meddelanden. Det följer att täckande gateway mönstret bör endast användas när mycket få enheter ansluter via varje fält gateway och moln till enhet trafiken är låg.

När du implementerar en täckande gateway använder din modulen för översättning av protokollet modulen anslutningssträngen.

När du implementerar en transparent gateway skapar modulen flera instanser av enhetsklienten IoT-hubb med anslutningssträngar för efterföljande enheter.

## <a name="next-steps"></a>Nästa steg

- [Förstå de krav och verktyg för att utveckla IoT kant moduler][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/CACertToolEdge/tools/CACertificates/CACertificateOverview.md