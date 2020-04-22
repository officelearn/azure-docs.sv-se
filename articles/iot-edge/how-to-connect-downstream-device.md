---
title: Ansluta underordnade enheter – Azure IoT Edge | Microsoft-dokument
description: Konfigurera nedströms- eller lövenheter för att ansluta till Azure IoT Edge-gatewayenheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3113f01341d2a1ec6160cfea3eb9d12d18b8495c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687172"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Ansluta en underordnad enhet till en Azure IoT Edge-gateway

Den här artikeln innehåller instruktioner för hur du upprättar en betrodd anslutning mellan underordnade enheter och IoT Edge transparenta gateways. I ett genomskinligt gatewayscenario kan en eller flera enheter skicka sina meddelanden via en enda gateway-enhet som upprätthåller anslutningen till IoT Hub. En nedströmsenhet kan vara alla program eller plattformar som har en identitet som skapats med [Azure IoT](https://docs.microsoft.com/azure/iot-hub) Hub-molntjänsten. I många fall använder dessa program [Azure IoT-enheten SDK](../iot-hub/iot-hub-devguide-sdks.md). En nedströmsenhet kan till och med vara ett program som körs på själva IoT Edge-gatewayenheten.

Det finns tre allmänna steg för att konfigurera en lyckad transparent gateway-anslutning. Den här artikeln täcker det tredje steget:

1. Gateway-enheten måste ansluta till nedströmsenheter på ett säkert sätt, ta emot kommunikation från nedströmsenheter och dirigera meddelanden till rätt destination. Mer information finns i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent gateway](how-to-create-transparent-gateway.md).
2. Nedströmsenheten behöver en enhetsidentitet för att kunna autentisera med IoT Hub och veta att kommunicera via sin gateway-enhet. Mer information finns i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Nedströmsenheten måste ansluta till gatewayenheten på ett säkert sätt.**

Den här artikeln identifierar vanliga problem med underordnade enhetsanslutningar och vägleder dig att konfigurera nedströmsenheter genom att:

* Förklara transportlagersäkerhet (TLS) och certifikatgrundningar.
* Förklara hur TLS-bibliotek fungerar i olika operativsystem och hur varje operativsystem hanterar certifikat.
* Gå igenom Azure IoT-exempel på flera språk för att komma igång.

I den här artikeln refererar *termerna gateway* och *IoT Edge-gateway* till en IoT Edge-enhet som konfigurerats som en transparent gateway.

## <a name="prerequisites"></a>Krav

* Låt **azure-iot-test-only.root.ca.cert.pem-certifikatfilen** som genererades i [Konfigurera en IoT Edge-enhet för att fungera som en transparent gateway som](how-to-create-transparent-gateway.md) är tillgänglig på din nedströmsenhet. Din nedströmsenhet använder det här certifikatet för att verifiera gateway-enhetens identitet.
* Har den ändrade anslutningssträngen som pekar på gateway-enheten, som förklaras i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Förbereda en nedströmsenhet

En nedströmsenhet kan vara alla program eller plattformar som har en identitet som skapats med [Azure IoT](https://docs.microsoft.com/azure/iot-hub) Hub-molntjänsten. I många fall använder dessa program [Azure IoT-enheten SDK](../iot-hub/iot-hub-devguide-sdks.md). En nedströmsenhet kan till och med vara ett program som körs på själva IoT Edge-gatewayenheten. En annan IoT Edge-enhet kan dock inte vara nedströms en IoT Edge-gateway.

>[!NOTE]
>IoT-enheter som har identiteter som är registrerade i IoT Hub kan använda [modultvillingar](../iot-hub/iot-hub-devguide-module-twins.md) för att isolera olika processer, maskinvara eller funktioner på en enda enhet. IoT Edge-gateways stöder underordnade moduler anslutningar med symmetrisk nyckelautentisering men inte X.509-certifikatautentisering.

Om du vill ansluta en nedströmsenhet till en IoT Edge-gateway behöver du två saker:

* En enhet eller ett program som är konfigurerat med en IoT Hub-enhetsanslutningssträng som läggs till med information för att ansluta den till gatewayen.

    Det här steget förklaras i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Enheten eller programmet måste lita på gatewayens **rotcertifikatutfärdare** för att validera TLS-anslutningarna till gateway-enheten.

    Det här steget förklaras i detalj i resten av den här artikeln. Det här steget kan utföras på två sätt: genom att installera CERTIFIKATUTFÄRDERcertifikatet i operativsystemets certifikatarkiv eller (för vissa språk) genom att referera till certifikatet i program med Hjälp av Azure IoT SDK:er.

## <a name="tls-and-certificate-fundamentals"></a>TLS och certifikatgrundprinciper

Utmaningen att säkert ansluta nedströms enheter till IoT Edge är precis som alla andra säkra klient / server kommunikation som sker över Internet. En klient och en server kommunicerar säkert via internet med hjälp av [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS är byggd med standard [PKI-konstruktioner (Public Key Infrastructure)](https://en.wikipedia.org/wiki/Public_key_infrastructure) som kallas certifikat. TLS är en ganska involverad specifikation och behandlar ett brett spektrum av ämnen relaterade till att säkra två slutpunkter. I det här avsnittet sammanfattas de begrepp som är relevanta för att du ska kunna ansluta enheter till en IoT Edge-gateway på ett säkert sätt.

När en klient ansluter till en server visar servern en certifikatkedja, som kallas *servercertifikatkedjan*. En certifikatkedja består vanligtvis av ett certifikatutfärdarcertifikat, ett eller flera mellanliggande certifikatutfärdarcertifikat och slutligen själva serverns certifikat. En klient upprättar förtroende med en server genom att kryptografiskt verifiera hela servercertifikatkedjan. Den här klientvalideringen av servercertifikatkedjan kallas validering av *serverkedja*. Klienten kryptografiskt utmanar tjänsten att bevisa innehav av den privata nyckeln i samband med servercertifikatet i en process som kallas *bevis på innehav*. Kombinationen av validering av serverkedja och bevis på innehav kallas *serverautentisering*. För att validera en servercertifikatkedja behöver en klient en kopia av rotcertifikatutfärdarcertifikatet som användes för att skapa (eller utfärda) serverns certifikat. Normalt när du ansluter till webbplatser, en webbläsare levereras förkonfigurerad med vanliga CA-certifikat så att klienten har en sömlös process.

När en enhet ansluter till Azure IoT Hub är enheten klienten och IoT Hub-molntjänsten är servern. Molntjänsten IoT Hub backas upp av ett rotcertifikatutfärdare som kallas **Baltimore CyberTrust Root**, som är allmänt tillgänglig och allmänt använd. Eftersom IoT Hub CA-certifikatet redan är installerat på de flesta enheter använder många TLS-implementeringar (OpenSSL, Schannel, LibreSSL) automatiskt under validering av servercertifikat. En enhet som kan ansluta till IoT Hub kan ha problem med att försöka ansluta till en IoT Edge-gateway.

När en enhet ansluter till en IoT Edge-gateway är nedströmsenheten klienten och gateway-enheten är servern. Azure IoT Edge tillåter operatörer (eller användare) att skapa gateway-certifikatkedjor hur de passar. Operatören kan välja att använda ett offentligt certifikatutfärdarcertifikat, till exempel Baltimore, eller använda ett självsignerat (eller internt) rotcertifikatutfärdarcertifikat. Offentliga CA-certifikat har ofta en kostnad som är associerad med dem, så används vanligtvis i produktionsscenarier. Självsignerade CA-certifikat är att föredra för utveckling och testning. De genomskinliga gateway-inställningsartiklarna som anges i introduktionen använder självsignerade rotcertifikatutfärdare.

När du använder ett självsignerat rotcertifikatutfärdarcertifikat för en IoT Edge-gateway måste det installeras på eller tillhandahållas till alla underordnade enheter som försöker ansluta till gatewayen.

![Konfigurera gatewaycertifikat](./media/how-to-create-transparent-gateway/gateway-setup.png)

Mer information om IoT Edge-certifikat och vissa produktionskonsekvenser finns i [användningsinformation för IoT Edge-certifikat](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Ange rotcertifikatutfärdarcertifikatet

För att verifiera gateway-enhetens certifikat behöver nedströmsenheten en egen kopia av rotcertifikatutfärdarcertifikatet. Om du använde skripten i IoT Edge git-databasen för att skapa testcertifikat kallas rotcertifikatutfärdarcertifikatet **azure-iot-test-only.root.ca.cert.pem**. Om du inte redan har som en del av de andra stegen för förberedelse av nedströmsenheter flyttar du den här certifikatfilen till valfri katalog på nedströmsenheten. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som Secure copy [protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfilen.

## <a name="install-certificates-in-the-os"></a>Installera certifikat i operativsystemet

Genom att installera rotcertifikatet i operativsystemets certifikatarkiv kan de flesta program i allmänhet använda rotcertifikatet. Det finns några undantag, till exempel NodeJS-program som inte använder OS-certifikatarkivet utan i stället använder nodkörningstidens interna certifikatarkiv. Om du inte kan installera certifikatet på operativsystemsnivå går du vidare till [Använd certifikat med Azure IoT SDK:er](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

Följande kommandon är ett exempel på hur du installerar ett CA-certifikat på en Ubuntu-värd. Det här exemplet förutsätter att du använder **azure-iot-test-only.root.ca.cert.pem-certifikatet** från standardartiklarna och att du har kopierat certifikatet till en plats på den nedströms enheten.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Du bör se ett meddelande som säger, "Uppdatera certifikat i / etc / ssl / certs ... 1 tillagd, 0 bort; gjort."

### <a name="windows"></a>Windows

Följande steg är ett exempel på hur du installerar ett certifikatutfärdarcertifikat på en Windows-värd. Det här exemplet förutsätter att du använder **azure-iot-test-only.root.ca.cert.pem-certifikatet** från standardartiklarna och att du har kopierat certifikatet till en plats på den nedströms enheten.

Du kan installera certifikat med Hjälp av PowerShells [importcertifikat](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) som administratör:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Du kan också installera certifikat med hjälp av **certlm-verktyget:**

1. Sök efter och välj **Hantera datorcertifikat på**Start-menyn . Ett verktyg som kallas **certlm** öppnas.
2. Navigera till **certifikat – betrodda** > **rotcertifikatutfärdare**för lokal dator .
3. Högerklicka på **Certifikat** och välj **Importera alla aktiviteter** > **.** Guiden importera certifikat bör startas.
4. Följ stegen enligt anvisningarna `<path>/azure-iot-test-only.root.ca.cert.pem`och importera certifikatfilen . När du är klar bör du se meddelandet "Har importerat".

Du kan också installera certifikat programmässigt med .NET API:er, som visas i .NET-exemplet senare i den här artikeln.

Vanligtvis använder program den Windows som tillhandahålls TLS-stacken [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) för att säkert ansluta via TLS. Schannel *kräver* att alla certifikat installeras i Windows-certifikatarkivet innan du försöker upprätta en TLS-anslutning.

## <a name="use-certificates-with-azure-iot-sdks"></a>Använda certifikat med Azure IoT SDK:er

I det här avsnittet beskrivs hur Azure IoT SDK:er ansluter till en IoT Edge-enhet med enkla exempelprogram. Målet med alla exempel är att ansluta enhetsklienten och skicka telemetrimeddelanden till gatewayen och sedan stänga anslutningen och avsluta.

Ha två saker klara innan du använder proverna på programnivå:

* Den nedströms enhetens IoT Hub-anslutningssträng har ändrats för att peka på gateway-enheten och alla certifikat som krävs för att autentisera nedströmsenheten till IoT Hub. Mer information finns i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Den fullständiga sökvägen till rotcertifikatutfärdarcertifikatet som du kopierade och sparade någonstans på nedströmsenheten.

    Till exempel `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Det här avsnittet innehåller ett exempelprogram för att ansluta en Azure IoT NodeJS-enhetsklient till en IoT Edge-gateway. För NodeJS-program måste du installera rotcertifikatutfärdaren på programnivå som visas här. NodeJS-program använder inte systemets certifikatarkiv.

1. Hämta exemplet för **edge_downstream_device.js** från [Azure IoT-enheten SDK för nod.js-exempel repo](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Kontrollera att du har alla förutsättningar att köra exemplet genom att granska **readme.md** filen.
3. Uppdatera **variablerna connectionString** och **edge_ca_cert_path** i edge_downstream_device.js-filen.
4. Se SDK-dokumentationen för instruktioner om hur du kör exemplet på enheten.

För att förstå exemplet som du kör är följande kodavsnitt hur klienten SDK läser certifikatfilen och använder den för att upprätta en säker TLS-anslutning:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT .NET-enhetsklient till en IoT Edge-gateway. .NET-program kan dock automatiskt använda alla installerade certifikat i systemets certifikatarkiv på både Linux- och Windows-värdar.

1. Hämta exemplet för **EdgeDownstreamDevice** från [mappen IoT Edge .NET samples](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Kontrollera att du har alla förutsättningar att köra exemplet genom att granska **readme.md** filen.
3. Uppdatera **variablerna DEVICE_CONNECTION_STRING** och **CA_CERTIFICATE_PATH** i filen **Properties / launchSettings.json.** Om du vill använda certifikatet som är installerat i det betrodda certifikatarkivet på värdsystemet lämnar du den här variabeln tom.
4. Se SDK-dokumentationen för instruktioner om hur du kör exemplet på enheten.

Om du vill installera ett betrott certifikat i certifikatarkivet program via ett .NET-program läser du funktionen **InstallCACert()** i **filen EdgeDownstreamDevice / Program.cs.** Den här åtgärden är idempotent, så kan köras flera gånger med samma värden utan ytterligare effekt.

### <a name="c"></a>C

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT C-enhetsklient till en IoT Edge-gateway. C SDK kan fungera med många TLS-bibliotek, inklusive OpenSSL, WolfSSL och Schannel. Mer information finns i [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Hämta **iotedge_downstream_device_sample-programmet** från [Azure IoT-enheten SDK för C-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Kontrollera att du har alla förutsättningar att köra exemplet genom att granska **readme.md** filen.
3. Uppdatera **variablerna connectionString** och **edge_ca_cert_path** i filen iotedge_downstream_device_sample.c.
4. Se SDK-dokumentationen för instruktioner om hur du kör exemplet på enheten.

Azure IoT-enheten SDK för C är ett alternativ för att registrera ett CERTIFIKATUTfärdarcertifikat när klienten konfigureras. Den här åtgärden installerar inte certifikatet någonstans, utan använder i stället ett strängformat för certifikatet i minnet. Det sparade certifikatet tillhandahålls till den underliggande TLS-stacken när en anslutning upprättas.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Om du inte använder OpenSSL eller något annat TLS-bibliotek på Windows-värdar används SDK som standard med Schannel. För att Schannel ska fungera bör IoT Edge-rotcertifikatutfärdaren installeras `IoTHubDeviceClient_SetOption` i Windows-certifikatarkivet, inte anges med hjälp av åtgärden.

### <a name="java"></a>Java

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT Java-enhetsklient till en IoT Edge-gateway.

1. Hämta exemplet för **Send-event** från [Azure IoT-enheten SDK för Java-exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Kontrollera att du har alla förutsättningar att köra exemplet genom att granska **readme.md** filen.
3. Se SDK-dokumentationen för instruktioner om hur du kör exemplet på enheten.

### <a name="python"></a>Python

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT Python-enhetsklient till en IoT Edge-gateway.

1. Hämta exemplet för **send_message** från [Azure IoT-enheten SDK för Python-exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Kontrollera att du antingen körs i en IoT Edge-behållare eller `EdgeHubConnectionString` `EdgeModuleCACertificateFile` i ett felsökningsscenario, har miljövariablerna och miljövariablera inställda.
3. Se SDK-dokumentationen för instruktioner om hur du kör exemplet på enheten.

## <a name="test-the-gateway-connection"></a>Testa gatewayanslutningen

Använd det här exempelkommandot för att testa att nedströmsenheten kan ansluta till gateway-enheten:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Det här kommandot testar anslutningar över MQTTS (port 8883). Om du använder ett annat protokoll justerar du kommandot efter behov för AMQPS (5671) eller HTTPS (433)

Utdata för det här kommandot kan vara lång, inklusive information om alla certifikat i kedjan. Om anslutningen lyckas visas en linje som `Verification: OK` `Verify return code: 0 (ok)`eller .

![Verifiera gatewayanslutning](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Felsöka gatewayanslutningen

Om lövenheten har intermittent anslutning till gateway-enheten provar du följande steg för upplösning.

1. Är gateway-värdnamnet i anslutningssträngen samma värde som värdnamnsvärdet i IoT Edge config.yaml-filen på gateway-enheten?
2. Går gatewayvärden att matchas till en IP-adress? Du kan lösa intermittenta anslutningar antingen genom att använda DNS eller genom att lägga till en värdfilpost på lövenheten.
3. Är kommunikationsportar öppna i brandväggen? Kommunikation baserad på det protokoll som används (MQTTS:8883/AMQPS:5671/HTTPS:433) måste vara möjlig mellan nedströmsenheten och den genomskinliga IoT Edge.

## <a name="next-steps"></a>Nästa steg

Lär dig hur IoT Edge kan utöka [offlinefunktionerna](offline-capabilities.md) till underordnade enheter.
