---
title: Ansluta underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Konfigurera underordnade eller löv enheter för att ansluta till Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510812"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>En underordnad ansluts till en Azure IoT Edge-gateway

Den här artikeln innehåller instruktioner för att upprätta en betrodd anslutning mellan underordnade enheter och IoT Edge transparenta gatewayer. I ett scenario med transparent Gateway kan en eller flera enheter skicka sina meddelanden via en enda gateway-enhet som upprätthåller anslutningen till IoT Hub. En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det tredje steget:

1. Gateway-enheten måste ansluta till underordnade enheter på ett säkert sätt, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt mål. Mer information finns i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).
2. Den underordnade enheten behöver en enhets identitet för att kunna autentisera med IoT Hub och kunna kommunicera via dess gateway-enhet. Mer information finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Den underordnade enheten måste ansluta till sin gateway-enhet på ett säkert sätt.**

Den här artikeln identifierar vanliga problem med underordnade enhetsanslutningar och hjälper dig att konfigurera dina underordnade enheter genom att:

* Förklarar transport layer security (TLS) och certifikatet grunderna.
* Förklarar hur TLS-biblioteken fungerar i olika operativsystem och hur varje operativsystem behandlar certifikat.
* Gå igenom Azure IoT-exempel i flera språk för att snabbt komma igång.

I den här artikeln villkoren *gateway* och *IoT Edge-gateway* referera till en IoT Edge-enhet som har konfigurerats som en transparent gateway.

## <a name="prerequisites"></a>Krav

* Ha certifikat filen **Azure-IoT-test-Only. root. ca. cert. pem** som genererades i [Konfigurera en IoT Edge-enhet för att fungera som en transparent Gateway](how-to-create-transparent-gateway.md) som är tillgänglig på din underordnade enhet. Den underordnade enheten använder det här certifikatet för att verifiera identiteten för gateway-enheten.
* Ha den ändrade anslutnings strängen som pekar på gateway-enheten, enligt beskrivningen i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Förbereda en underordnad enhet

En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på själva IoT Edge gateway-enheten. En annan IoT Edge enhet kan dock inte underordnas IoT Edge Gateway.

>[!NOTE]
>IoT-enheter som har registrerade identiteter i IoT Hub kan använda [modulerna](../iot-hub/iot-hub-devguide-module-twins.md) för att isolera olika processer, maskin vara eller funktioner på en enskild enhet. IoT Edge gateways stöder underordnad modul anslutningar med hjälp av symmetrisk nyckel autentisering men inte X. 509-certifikatautentisering.

Om du vill ansluta en underordnad enhet till en IoT Edge-gateway, behöver du två saker:

* En enhet eller ett program som är konfigurerad med en IoT Hub-enhetsanslutningssträngen läggas till med information för att ansluta till gatewayen.

    Det här steget förklaras i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Enheten eller programmet måste lita på gatewayens **rotcertifikatutfärdarcertifikat** för att verifiera TLS-anslutningarna till gateway-enheten.

    Det här steget beskrivs i detalj i resten av den här artikeln. Det här steget kan utföras på ett av två sätt: genom att installera CA-certifikatet i operativ systemets certifikat arkiv eller (för vissa språk) genom att referera till certifikatet i program med hjälp av Azure IoT SDK: er.

## <a name="tls-and-certificate-fundamentals"></a>TLS och certifikat

Den stora utmaningen med på ett säkert sätt ansluta underordnade enheter till IoT Edge är precis som andra säker klient/server-kommunikation som sker via internet. En klient och en server på ett säkert sätt kommunicerar via internet med [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS byggs med standard [offentlig nyckelinfrastruktur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstruktioner kallas certifikat. TLS är en ganska engagerad specifikation och åtgärdar en mängd olika ämnen relaterade till att skydda två slut punkter. I det här avsnittet sammanfattas de begrepp som är relevanta för att ansluta enheter till en IoT Edge-gateway på ett säkert sätt.

När en klient ansluter till en server, servern visar en kedja av certifikat, kallas de *servercertifikatkedjan*. En certifikatkedja består vanligtvis av ett rotcertifikat certificate authority (CA), en eller flera mellanliggande CA-certifikat och slutligen servercertifikat själva. En klient upprättar förtroende med en server genom att kryptografiskt Verifiera certifikatkedja för hela servern. Den här klient verifieringen av Server certifikat kedjan kallas för *Server kedje verifiering*. Klienten kryptografiskt utmaningar tjänsten för att bevisa att den privata nyckeln är kopplad till Server certifikatet i en process som kallas *bevis på innehavet*. Kombinationen av Server kedje validering och bevis på innehav kallas *serverautentisering*. För att verifiera en servercertifikatkedjan kan behöver en klient en kopia av certifikatet för rotcertifikatutfärdaren som används för att skapa (eller utfärda) serverns certifikat. Normalt när du ansluter till webbplatser, är en webbläsare förkonfigurerad med vanliga CA-certifikat så att klienten har en sömlös process.

När en enhet ansluter till Azure IoT Hub, enheten är klienten och tjänsten IoT Hub cloud är servern. Tjänsten IoT Hub cloud backas upp av ett rot-CA-certifikat som kallas **Baltimore CyberTrust Root**, som är allmänt tillgängliga och vanligt. Eftersom IoT Hub CA-certifikatet är redan installerad på de flesta enheter kan använda många olika implementeringar TLS (OpenSSL, Schannel, LibreSSL) det automatiskt under verifieringen av servercertifikatet. En enhet som kan ansluta till IoT Hub kan ha problem med försöker ansluta till en IoT Edge-gateway.

När en enhet ansluter till en IoT Edge-gateway, underordnade enheten är klienten och gateway-enheten är servern. Azure IoT Edge kan operatörer (eller användare) att skapa gateway-certifikatkedjor men passar behoven. Operatören kan välja att använda en offentlig CA-certifikat som Baltimore, eller använda ett självsignerat (eller interna) rot CA-certifikat. Offentlig CA-certifikat har en associerad kostnad till dem ofta, så används vanligtvis i produktionsscenarier. Självsignerade certifikat är prioriterade för utveckling och testning. De artiklar om transparent Gateway-installation som anges i introduktionen Använd självsignerade rot certifikat utfärdare.

När du använder ett självsignerat rotcertifikat för en IoT Edge-gateway måste vara installerad på eller tillhandahålls till alla underordnade enheter försöker ansluta till gatewayen.

![Installationsprogram för gateway-certifikatet](./media/how-to-create-transparent-gateway/gateway-setup.png)

Läs mer om IoT Edge-certifikat och vissa konsekvenser för produktion i [användningsinformation för IoT Edge certifikat](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Ange rot certifikat utfärdarens certifikat

För att verifiera gateway-enhetens certifikat behöver den underordnade enheten en egen kopia av rot certifikat utfärdarens certifikat. Om du använde skripten som tillhandahölls i IoT Edge git-lagringsplatsen för att skapa test certifikat, kallas rot certifikat utfärdarens certifikat **Azure-IoT-test-Only. root. ca. cert. pem**. Om du inte redan har en del av de andra stegen för att förbereda enheten i den andra enheten flyttar du den här certifikat filen till valfri katalog på den underordnade enheten. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [säker kopierings protokoll](https://www.ssh.com/ssh/scp/) för att flytta certifikat filen.

## <a name="install-certificates-in-the-os"></a>Installera certifikat i operativ systemet

Om du installerar rot certifikat utfärdarens certifikat i operativ systemets certifikat Arkiv, kan de flesta program använda rot certifikat utfärdarens certifikat. Det finns vissa undantag, t. ex. NodeJS-program som inte använder sig av operativ systemets certifikat Arkiv, men som i stället använder Node Runtime: s interna certifikat arkiv. Om du inte kan installera certifikatet på operativ Systems nivå kan du gå vidare till [använda certifikat med Azure IoT SDK](#use-certificates-with-azure-iot-sdks): er.

### <a name="ubuntu"></a>Ubuntu

Följande kommandon är ett exempel på hur du installerar ett CA-certifikat på en Ubuntu-värd. I det här exemplet förutsätter vi att du använder certifikatet **Azure-IoT-test-Only. root. ca. cert. pem** från de nödvändiga artiklarna och att du har kopierat certifikatet till en plats på den underordnade enheten.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Du bör se ett meddelande som säger "uppdaterar certifikat i/etc/ssl/certs... 1 tillagt, 0 borttagen; färdig. "

### <a name="windows"></a>Windows

Följande är ett exempel på hur du installerar ett CA-certifikat på en Windows-värd. I det här exemplet förutsätter vi att du använder certifikatet **Azure-IoT-test-Only. root. ca. cert. pem** från de nödvändiga artiklarna och att du har kopierat certifikatet till en plats på den underordnade enheten.

Du kan installera certifikat med hjälp av PowerShell: s [import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) som administratör:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

Du kan också installera certifikat med **certlm** -verktyget:

1. I Start-menyn, Sök efter och välj **hantera datorcertifikat**. Ett verktyg som kallas **certlm** öppnas.
2. Gå till **certifikat – lokal dator** > **betrodda rotcertifikatutfärdare**.
3. Högerklicka på **certifikat** och välj **alla uppgifter** > **Import**. Guiden Importera certifikat ska starta.
4. Följ stegen enligt anvisningarna och importera certifikatfil `<path>/azure-iot-test-only.root.ca.cert.pem`. När du är klar visas meddelandet ”importerades”.

Du kan också installera certifikat via programmering med hjälp av .NET-API: er, som visas i .NET-exempel senare i den här artikeln.

Program använder vanligtvis Windows tillhandahållna TLS stack kallas [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) att på ett säkert sätt ansluta via TLS. Schannel *kräver* att alla certifikat installeras i certifikatarkivet Windows innan du försöker upprätta en anslutning med TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Använda certifikat med Azure IoT SDK: er

Det här avsnittet beskrivs hur Azure IoT SDK ansluter till en IoT Edge-enhet med hjälp av enkla exempelprogram. Målet med alla exempel är att ansluta enhetsklienten och skicka telemetrimeddelanden till gatewayen, och sedan stänga anslutningen och avsluta.

Har två saker som är redo innan du använder programnivå exemplen:

* Den underordnade enhetens IoT Hub anslutnings sträng har ändrats för att peka på gateway-enheten och eventuella certifikat som krävs för att autentisera din underordnade enhet till IoT Hub. Mer information finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Den fullständiga sökvägen till rot-CA-certifikatet som du kopierade och sparat någonstans på underordnade enheten.

    Till exempel `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Det här avsnittet innehåller ett exempelprogram för att ansluta en Azure IoT NodeJS enhetsklienten till en IoT Edge-gateway. För NodeJS-program måste du installera rot certifikat utfärdarens certifikat på program nivå enligt vad som visas här. NodeJS-program använder inte systemets certifikat arkiv.

1. Hämta det här exemplet för **edge_downstream_device.js** från den [lagringsplatsen för Azure IoT-enhetens SDK för Node.js-exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil.
3. I filen edge_downstream_device.js uppdatera den **connectionString** och **edge_ca_cert_path** variabler.
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

För att förstå exemplet som du kör, är ett kodfragment hur klient-SDK läser certifikatfilen och används för att upprätta en säker TLS-anslutning:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Det här avsnittet introducerar ett exempelprogram för att ansluta en klient för Azure IoT .NET-enhet till en IoT Edge-gateway. .NET-program är dock automatiskt kan använda alla installerade certifikat i datorns certifikatarkiv på både Linux och Windows-värdar.

1. Hämta det här exemplet för **EdgeDownstreamDevice** från den [IoT Edge .NET exempelmappen](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice).
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil.
3. I den **egenskaper / launchSettings.json** uppdaterar den **DEVICE_CONNECTION_STRING** och **CA_CERTIFICATE_PATH** variabler. Om du vill använda certifikatet som installerades i det betrodda certifikatarkivet på värdsystemet du inget i den här variabeln.
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

Om du vill installera programmässigt ett betrott certifikat i certifikatarkivet via ett .NET-program, referera till den **InstallCACert()** fungera i den **EdgeDownstreamDevice / Program.cs** fil. Den här åtgärden är idempotenta, så kan köras flera gånger med samma värden utan ytterligare påverkas.

### <a name="c"></a>C

Det här avsnittet introducerar ett exempelprogram för att ansluta en klient för Azure IoT C-enheten till en IoT Edge-gateway. C SDK kan arbeta med många TLS-bibliotek, inklusive OpenSSL, WolfSSL och Schannel. Mer information finns i den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c).

1. Hämta den **iotedge_downstream_device_sample** programmet från den [Azure IoT-enhetens SDK för C-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil.
3. I filen iotedge_downstream_device_sample.c uppdatera den **connectionString** och **edge_ca_cert_path** variabler.
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

Azure IoT-enhetens SDK för C ger ett alternativ för att registrera ett CA-certifikat när du konfigurerar klienten. Den här åtgärden installera inte certifikatet var som helst, men i stället använder en strängformat för certifikatet i minnet. Sparade certifikatet har angetts för den underliggande TLS-stacken när en anslutning upprättas.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

På Windows-värdar, om du inte använder OpenSSL eller en annan TLS-biblioteket, SDK: N som standard använder Schannel. För Schannel ska fungera, IoT Edge rot-CA-certifikatet bör installeras i certifikatarkivet Windows inte har angetts med hjälp av den `IoTHubDeviceClient_SetOption` igen.

### <a name="java"></a>Java

Det här avsnittet introducerar ett exempelprogram som du ansluter en enhet Azure IoT-Java-klient till en IoT Edge-gateway.

1. Hämta det här exemplet för **överföringshändelse** från den [Azure IoT-enhetens SDK för Java-exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil.
3. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

### <a name="python"></a>Python

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT Python-klienten för enheter till en IoT Edge-gateway.

1. Hämta exemplet för **send_message** från [Azure IoT-enhetens SDK för python-exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios).
2. Se till att du antingen kör i en IoT Edge-behållare eller i ett fel söknings scenario och har angett `EdgeHubConnectionString`-och `EdgeModuleCACertificateFile` miljövariablerna.
3. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

## <a name="test-the-gateway-connection"></a>Testa gateway-anslutning

Använd det här exempel kommandot för att testa att den underordnade enheten kan ansluta till gateway-enheten:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Det här kommandot testar anslutningar över MQTTS (port 8883). Om du använder ett annat protokoll, justera kommandot efter behov för AMQPS (5671) eller HTTPS (433)

Utdata från det här kommandot kan vara långa, inklusive information om alla certifikat i kedjan. Om anslutningen lyckas visas en rad som `Verification: OK` eller `Verify return code: 0 (ok)`.

![Verifiera Gateway-anslutning](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Felsöka Gateway-anslutningen

Om din löv enhet har en tillfällig anslutning till sin gateway-enhet kan du prova följande steg för att lösa problemet.

1. Är Gateway-värdnamnet i anslutnings strängen detsamma som hostname-värdet i filen IoT Edge config. yaml på gateway-enheten?
2. Går det att matcha Gateway-värdnamnet till en IP-adress? Du kan lösa tillfälliga anslutningar antingen genom att använda DNS eller genom att lägga till en värd fil post på löv enheten.
3. Är kommunikations portar öppna i brand väggen? Kommunikation baserat på det protokoll som används (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) måste vara möjlig mellan underordnad enhet och transparent IoT Edge.

## <a name="next-steps"></a>Nästa steg

Lär dig hur IoT Edge kan utöka [offlinefunktionerna](offline-capabilities.md) till underordnade enheter.
