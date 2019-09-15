---
title: Autentisera underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Så här autentiserar du underordnade enheter eller löv enheter till IoT Hub och dirigerar deras anslutning via Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a032056a684107de3dd00fe4861f34c013a80db
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003618"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

I ett scenario med transparent Gateway behöver underordnade enheter (ibland kallade löv enheter eller underordnade enheter) identiteter i IoT Hub som andra enheter. Den här artikeln vägleder dig genom alternativen för att autentisera en underordnad enhet till IoT Hub och visar hur du deklarerar Gateway-anslutningen.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det andra steget:

1. Gateway-enheten måste kunna ansluta till underordnade enheter på ett säkert sätt, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt mål. Mer information finns i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).
2. **Den underordnade enheten måste ha en enhets identitet för att kunna autentisera med IoT Hub och kunna kommunicera via dess gateway-enhet.**
3. Den underordnade enheten måste kunna ansluta till sin gateway-enhet på ett säkert sätt. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Underordnade enheter kan autentiseras med IoT Hub med hjälp av någon av tre metoder: symmetriska nycklar (kallas ibland för delade åtkomst nycklar), X. 509 självsignerade certifikat eller certifikat från X. 509 certifikat utfärdare (CA) signerade certifikat. Stegen för autentisering liknar de steg som används för att konfigurera en icke-IoT-Edge-enhet med IoT Hub, med små skillnader för att deklarera Gateway-relationen.

Stegen i den här artikeln visar manuell enhets etablering, inte automatisk etablering med Azure-IoT Hub Device Provisioning Service. 

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway](how-to-create-transparent-gateway.md).

Den här artikeln hänvisar till *Gateway-värdnamnet* på flera punkter. Gateway-värdnamnet deklareras i parametern **hostname** i filen config. yaml på den IoT Edge gateway-enheten. Den används för att skapa certifikaten i den här artikeln och det hänvisas till anslutnings strängen för de underordnade enheterna. Gateway-värdnamnet måste matchas till en IP-adress, antingen med DNS eller en värd fil post.

## <a name="symmetric-key-authentication"></a>Autentisering med symmetrisk nyckel

Autentisering med symmetrisk nyckel eller autentisering med delad åtkomst nyckel är det enklaste sättet att autentisera med IoT Hub. Med symmetrisk nyckel autentisering är en Base64-nyckel kopplad till ditt IoT-enhets-ID i IoT Hub. Du inkluderar den nyckeln i dina IoT-program så att enheten kan presentera den när den ansluter till IoT Hub. 

### <a name="create-the-device-identity"></a>Skapa enhets identiteten 

Lägg till en ny IoT-enhet i IoT Hub med hjälp av antingen Azure Portal, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att underordnade enheter måste identifieras i IoT Hub som vanlig IoT-enhet, inte IoT Edge enheter. 

Ange följande information när du skapar den nya enhets identiteten: 

* Skapa ett ID för enheten.

* Välj **symmetrisk nyckel** som autentiseringstyp. 

* Du kan också välja att **Ange en överordnad enhet** och välja den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. Det här steget är valfritt för autentisering med symmetrisk nyckel, men det rekommenderas eftersom inställning av en överordnad enhet möjliggör [Offline-kapacitet](offline-capabilities.md) för din underordnade enhet. Du kan alltid uppdatera enhets informationen för att lägga till eller ändra den överordnade senare. 

   ![Skapa enhets-ID med symmetrisk nyckel-auth i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma åtgärd. I följande exempel skapas en ny IoT-enhet med autentisering med symmetrisk nyckel och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Mer information om Azure CLI-kommandon för att skapa och överordnad och underordnad hantering av enheter finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Ansluta till IoT Hub via en gateway

Samma process används för att autentisera vanliga IoT-enheter för att IoT Hub med symmetriska nycklar gäller även för underordnade enheter. Den enda skillnaden är att du måste lägga till en pekare till gateway-enheten för att dirigera anslutningen eller, i offline-scenarier, för att hantera autentiseringen för IoT Hubs räkning. 

För symmetrisk nyckel autentisering finns det inga ytterligare steg som du behöver utföra på enheten för att autentisera med IoT Hub. Du behöver fortfarande certifikaten på plats så att den underordnade enheten kan ansluta till sin gateway-enhet, enligt beskrivningen i [ansluta en underordnad enhet till en Azure IoT Edge Gateway](how-to-connect-downstream-device.md).

När du har skapat en IoT-enhets identitet i portalen kan du hämta dess primära eller sekundära nycklar. En av dessa nycklar måste inkluderas i anslutnings strängen som du inkluderar i alla program som kommunicerar med IoT Hub. För symmetrisk nyckel autentisering ger IoT Hub den fullständigt utformade anslutnings strängen i enhets informationen för din bekvämlighet. Du måste lägga till extra information om gateway-enheten i anslutnings strängen. 

De symmetriska nyckel anslutnings strängarna för underordnade enheter behöver följande komponenter: 

* IoT-hubben som enheten ansluter till:`Hostname={iothub name}.azure-devices.net`
* Enhets-ID som är registrerat i hubben:`DeviceID={device ID}`
* Antingen primär eller sekundär nyckel:`SharedAccessKey={key}`
* Den gateway-enhet som enheten ansluter via. Ange **hostname** -värdet från filen config. yaml för IoT Edge gateway-enheten:`GatewayHostName={gateway hostname}`

Tillsammans ser en fullständig anslutnings sträng ut så här:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Om du har upprättat en överordnad/underordnad-relation för den här underordnade enheten kan du förenkla anslutnings strängen genom att anropa gatewayen direkt som anslutnings värd. Exempel: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X. 509-autentisering 

Det finns två sätt att autentisera en IoT-enhet med X. 509-certifikat. På vilket sätt du väljer att autentisera, är stegen för att ansluta enheten till IoT Hub samma. Välj antingen självsignerade eller CA-signerade certifikat för autentisering och fortsätt sedan och lär dig hur du ansluter till IoT Hub. 

Mer information om hur IoT Hub använder X. 509-autentisering finns i följande artiklar: 
* [Enhetsautentisering som använder X. 509 CA-certifikat](../iot-hub/iot-hub-x509ca-overview.md)
* [Konceptuell förståelse för X. 509 CA-certifikat i IoT-branschen](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Skapa enhets identiteten med självsignerade X. 509-certifikat

För X. 509-självsignerad autentisering, som ibland kallas tumavtryck-autentisering, måste du skapa nya certifikat som ska placeras på din IoT-enhet. Dessa certifikat har ett tumavtryck i dem som du delar med IoT Hub för autentisering. 

Det enklaste sättet att testa det här scenariot är att använda samma dator som du använde för att skapa certifikat i [Konfigurera en IoT Edge enhet som fungerar som en transparent Gateway](how-to-create-transparent-gateway.md). Datorn bör redan ha kon figurer ATS med rätt verktyg, rotcertifikatutfärdarcertifikat och mellanliggande CA-certifikat för att skapa IoT-enhetens certifikat. Du kan kopiera de slutgiltiga certifikaten och deras privata nycklar till din efterföljande enhet efteråt. Följ stegen i artikeln Gateway, konfigurera openssl på datorn och klona sedan IoT Edge-lagrings platsen för att få åtkomst till certifikat skapande skript. Sedan gjorde du en arbets katalog som vi kallar  **\<WRKDIR >** för att lagra certifikaten. Standard certifikaten är avsedda för utveckling och testning, så de senaste 30 dagarna. Du bör ha skapat ett certifikat från en rot certifikat utfärdare och ett mellanliggande certifikat. 

1. Navigera till arbets katalogen i antingen ett bash-eller PowerShell-fönster. 

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. Ange enhetens namn och sedan den primära eller sekundära etiketten. Den här informationen används för att namnge filerna så att du kan hålla reda på certifikat för flera enheter. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Hämta SHA1-finger avtryck (kallas ett tumavtryck i IoT Hub-gränssnittet) från varje certifikat, som är en hexadecimal tecken sträng i 40. Använd följande openssl-kommando för att visa certifikatet och hitta finger avtrycket:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Navigera till din IoT-hubb i Azure Portal och skapa en ny IoT-enhets identitet med följande värden: 

   * Välj **X. 509 själv signerad** som autentiseringstyp.
   * Klistra in de hexadecimala strängarna som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **Ange en överordnad enhet** och välj den IoT Edge gateway-enhet som den här underordnade enheten ska ansluta till. En överordnad enhet krävs för X. 509-autentisering av en underordnad enhet. 

   ![Skapa enhets-ID med X. 509 självsignerad autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Kopiera följande filer till valfri katalog på den underordnade enheten:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Du refererar till de här filerna i de löv enhets program som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma enhets skapande åtgärd. I följande exempel skapas en ny IoT-enhet med en självsignerad X. 509-autentisering och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Mer information om Azure CLI-kommandon för skapande av enheter, rapportgenerering och över-och underordnade hantering finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Skapa enhets identiteten med X. 509 CA-signerade certifikat

För X. 509-signerad autentisering (CA) behöver du ett rot certifikat för certifikat utfärdare som registrerats i IoT Hub som du använder för att signera certifikat för din IoT-enhet. Alla enheter som använder ett certifikat som har problem med rot certifikat utfärdaren eller något av dess mellanliggande certifikat kommer att tillåtas att autentisera. 

Det här avsnittet baseras på instruktionerna som beskrivs i IoT Hub artikel konfigurera [säkerhet för X. 509 i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md). Följ stegen i det här avsnittet för att veta vilka värden som ska användas för att konfigurera en underordnad enhet som ansluter via en gateway. 

Det enklaste sättet att testa det här scenariot är att använda samma dator som du använde för att skapa certifikat i [Konfigurera en IoT Edge enhet som fungerar som en transparent Gateway](how-to-create-transparent-gateway.md). Datorn bör redan ha kon figurer ATS med rätt verktyg, rotcertifikatutfärdarcertifikat och mellanliggande CA-certifikat för att skapa IoT-enhetens certifikat. Du kan kopiera de slutgiltiga certifikaten och deras privata nycklar till din efterföljande enhet efteråt. Följ stegen i artikeln Gateway, konfigurera openssl på datorn och klona sedan IoT Edge-lagrings platsen för att få åtkomst till certifikat skapande skript. Sedan gjorde du en arbets katalog som vi kallar  **\<WRKDIR >** för att lagra certifikaten. Standard certifikaten är avsedda för utveckling och testning, så de senaste 30 dagarna. Du bör ha skapat ett certifikat från en rot certifikat utfärdare och ett mellanliggande certifikat. 

1. Följ anvisningarna i avsnittet [Registrera x. 509 CA-certifikat i IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg: 

   1. Ladda upp ett rot certifikat för certifikat utfärdare. Om du använder de certifikat som du skapade i den transparenta Gateway-artikeln laddar du upp  **\<WRKDIR >/certs/Azure-IoT-test-Only.root.ca.cert.pem** som rot certifikat fil. 
   2. Kontrol lera att du äger det rot certifikat utfärdarens certifikat. Du kan kontrol lera att du har till gång \<till cert-verktygen i WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Följ anvisningarna i avsnittet [skapa en X. 509-enhet för din IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) i *Konfigurera X. 509-säkerhet i Azure IoT Hub*. I det avsnittet utför du följande steg: 

   1. Lägg till en ny enhet. Ange ett gement namn för **enhets-ID**och välj autentiseringstypen **X. 509 ca signerad**. 
   2. Ange en överordnad enhet. För underordnade enheter väljer du **Ange en överordnad enhet** och väljer den IoT Edge gateway-enhet som ska anslutas till IoT Hub. 

3. Skapa en certifikat kedja för din underordnade enhet. Använd samma rot certifikat för certifikat utfärdare som du laddade upp till IoT Hub för att skapa den här kedjan. Använd samma små enhets-ID som du gav din enhets identitet i portalen.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Kopiera följande filer till valfri katalog på den underordnade enheten: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Du refererar till de här filerna i de löv enhets program som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) för att flytta certifikatfiler.

Du kan använda [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) för att slutföra samma enhets skapande åtgärd. I följande exempel skapas en ny IoT-enhet med X. 509 CA-signerad autentisering och tilldelar en överordnad enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Mer information om Azure CLI-kommandon för att skapa och överordnad och underordnad hantering av enheter finns i referens innehållet för [AZ IoT Hub Device-Identity-](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Ansluta till IoT Hub via en gateway

Varje Azure IoT SDK hanterar X. 509-autentisering lite annorlunda. Samma process används dock för att autentisera vanliga IoT-enheter för att IoT Hub med X. 509-certifikat gäller även för underordnade enheter. Den enda skillnaden är att du måste lägga till en pekare till gateway-enheten för att dirigera anslutningen eller, i offline-scenarier, för att hantera autentiseringen för IoT Hubs räkning. I allmänhet kan du följa samma steg i X. 509 för alla IoT Hub enheter och sedan bara ersätta värdet för **hostname** i anslutnings strängen som värd namnet för din gateway-enhet. 

I följande avsnitt visas några exempel på olika SDK-språk. 

>[!IMPORTANT]
>Följande exempel visar hur IoT Hub SDK: er använder certifikat för att autentisera enheter. I en produktions distribution bör du lagra alla hemligheter som privata eller SAS-nycklar i en maskin säker modul (HSM). 

#### <a name="net"></a>.NET

Ett exempel på ett C# program som autentiseras för att IoT Hub med X. 509-certifikat finns i [Konfigurera X. 509-säkerhet i Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Några av de viktigaste raderna i det exemplet finns här för att demonstrera autentiseringsprocessen.

När du har deklarerat värd namnet för din DeviceClient-instans använder du värd namnet för IoT Edge gateway-enheten. Värd namnet finns i gateway-enhetens config. yaml-fil. 

Om du använder test certifikaten som tillhandahålls av IoT Edge git-lagringsplatsen är nyckeln till certifikaten **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Ett exempel på ett C-program som autentiseras för att IoT Hub med X. 509-certifikat finns i C IoT SDK: s [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) -exempel. Några av de viktigaste raderna i det exemplet finns här för att demonstrera autentiseringsprocessen.

När du definierar anslutnings strängen för den underordnade enheten använder du värd namnet för IoT Edge gateway-enheten för parametern **hostname** . Värd namnet finns i gateway-enhetens config. yaml-fil. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Ett exempel på ett Node. js-program som autentiseras för IoT Hub med X. 509-certifikat finns i Node. js IoT SDK: s [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) -exempel. Några av de viktigaste raderna i det exemplet finns här för att demonstrera autentiseringsprocessen.

När du definierar anslutnings strängen för den underordnade enheten använder du värd namnet för IoT Edge gateway-enheten för parametern **hostname** . Värd namnet finns i gateway-enhetens config. yaml-fil. 

Om du använder test certifikaten som tillhandahålls av IoT Edge git-lagringsplatsen är nyckeln till certifikaten **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Python SDK stöder för närvarande bara användning av X509-certifikat och-nycklar från filer, inte de som definieras infogade. I följande exempel lagras relevanta fil Sök vägar i miljövariabler.

När du definierar värd namnet för den underordnade enheten använder du värd namnet för IoT Edge gateway-enheten för parametern **hostname** . Värd namnet finns i gateway-enhetens config. yaml-fil. 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

Ett exempel på ett Java-program som autentiseras för att IoT Hub med X. 509-certifikat finns i Java IoT SDK: s [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) -exempel. Några av de viktigaste raderna i det exemplet finns här för att demonstrera autentiseringsprocessen.

När du definierar anslutnings strängen för den underordnade enheten använder du värd namnet för IoT Edge gateway-enheten för parametern **hostname** . Värd namnet finns i gateway-enhetens config. yaml-fil. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Nästa steg

Genom att slutföra den här artikeln bör du ha en IoT Edge-enhet som fungerar som en transparent gateway och en underordnad enhet som är registrerad i IoT Hub. Därefter måste du konfigurera de underordnade enheterna så att de litar på gateway-enheten och skicka meddelanden till den. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
