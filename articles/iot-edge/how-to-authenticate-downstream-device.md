---
title: Autentisera underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Så här att autentisera underordnade enheter eller lövenheter till IoT Hub och dirigera sin anslutning via Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082396"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autentisera en underordnad enhet på Azure IoT Hub

I ett scenario med transparent gateway måste efterföljande enheter (kallas ibland för lövenheter eller underordnade enheter) identiteter i IoT Hub som någon annan enhet. Den här artikeln beskriver alternativ för att autentisera en underordnad enhet till IoT Hub och sedan visar hur du deklarera gateway-anslutning.

Det finns tre allmänna steg för att skapa en lyckad transparent gatewayanslutning. Den här artikeln beskriver det andra steget:

1. Gateway-enheten måste kunna ansluta till underordnade enheter, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt destination på ett säkert sätt. Mer information finns i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md).
2. **Underordnade enheten måste ha en enhetsidentitet för att kunna autentisera med IoT Hub och veta för att kommunicera via dess gateway-enhet.**
3. Underordnade enheten måste vara på ett säkert sätt ansluta till dess gateway-enhet. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Efterföljande enheter kan autentisera med IoT Hub med någon av tre metoder: symmetriska nycklar (kallas ibland nycklar för delad åtkomst), självsignerat X.509-certifikat eller X.509-certifikat (certifikatutfärdare) signerat certifikat. Autentisering-stegen är liknande de steg som används för att konfigurera alla IoT Edge-enhet med IoT Hub med mindre skillnader vad gäller att deklarera gateway-relation.

Stegen i den här artikeln visar manuell enhetsetablering inte automatisk etablering med Azure IoT Hub Device Provisioning-tjänsten. 

## <a name="symmetric-key-authentication"></a>Symmetrisk nyckel-autentisering

Symmetrisk nyckel-autentisering eller autentisering med delad åtkomst nyckel, är det enklaste sättet att autentisera med IoT Hub. En base64-nyckel är kopplad till din IoT-enhets-ID i IoT Hub med symmetrisk nyckelautentisering. Du kan inkludera nyckeln i din IoT-program så att enheten kan den när den ansluter till IoT Hub. 

### <a name="create-the-device-identity"></a>Skapa enhetens identitet 

Lägg till en ny IoT-enhet i IoT hub, med hjälp av Azure-portalen, Azure CLI eller IoT-tillägget för Visual Studio Code. Kom ihåg att efterföljande enheter måste identifieras i IoT Hub som vanliga IoT-enheter inte IoT Edge-enheter. 

När du skapar en ny enhetsidentitet kan du ange följande information: 

* Skapa ett ID för din enhet.

* Välj **symmetrisk nyckel** som autentiseringstyp. 

* Du kan också välja att **ange en överordnad enhet** och välj IoT Edge-gatewayenhet som underordnade enheten ansluter via. Det här steget är valfritt för symmetrisk nyckel-autentisering, men rekommenderas eftersom inställningen gör det möjligt för en överordnad enhet [offlinefunktionerna](offline-capabilities.md) för en underordnad enhet. Du kan alltid uppdatera information om enheten att lägga till eller ändra överordnat senare. 

   ![Skapa enhets-ID med symmetriska nyckel autentisering i portalen](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Du kan använda den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) att slutföra på samma gång. I följande exempel skapar en ny IoT-enhet med symmetrisk nyckelautentisering och tilldelar en överordnad-enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Mer information om Azure CLI-kommandon för enheten skapandet och hanteringen av överordnad/underordnad finns Referensinnehåll för [az iot hub-enhetsidentitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Ansluta till IoT Hub via en gateway

Samma process som används för att autentisera vanliga IoT-enheter till IoT Hub med symmetriska nycklar gäller även för efterföljande enheter. Den enda skillnaden är att du behöver lägga till en pekare till gatewayenheten för att dirigera anslutningen eller offline-scenarier, hantera autentisering för IoT Hub. 

Det finns inga ytterligare åtgärder som du behöver göra på din enhet att autentisera med IoT Hub för symmetrisk nyckelautentisering. Du behöver certifikaten på plats så att en underordnad enhet kan ansluta till dess gatewayenhet, enligt beskrivningen i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

Du kan hämta dess primära eller sekundära nycklar när du har skapat en enhetsidentitet i IoT i portalen. En av dessa nycklar måste ingå i anslutningssträngen som inkluderas i alla program som kommunicerar med IoT Hub. IoT Hub innehåller fullständigt formaterad anslutningssträngen i enhetsinformation för din bekvämlighet för symmetrisk nyckelautentisering. Du måste lägga till extra information om gateway-enheten i anslutningssträngen. 

Symmetrisk nyckel-anslutningssträngar för efterföljande enheter måste följande komponenter: 

* IoT-hubben som enheten ansluter till: `Hostname={iothub name}.azure-devices.net`
* Enhets-ID som registrerats på hubben: `DeviceID={device ID}`
* Antingen den primära eller sekundära nyckeln: `SharedAccessKey={key}`
* Gateway-enheten som enheten ansluter via. Ange den **värdnamn** värdet från IoT Edge-gatewayenhet config.yaml fil: `GatewayHostName={gateway hostname}`

En fullständig anslutningssträng ut allt på samma plats, som:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Om du har skapat en överordnad/underordnad relation för den här underordnade enheten kan du förenkla anslutningssträngen genom att anropa gateway direkt som värd för anslutningen. Exempel: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509-autentisering 

Det finns två sätt att autentisera en IoT-enhet med X.509-certifikat. Oavsett vilken du väljer att autentisera, stegen för att ansluta din enhet till IoT Hub är desamma. Välj antingen självsignerade eller CA-signerat certifikat för autentisering och fortsätta att lära dig hur du ansluter till IoT Hub. 

Mer information om hur IoT Hub använder X.509-autentisering finns i följande artiklar: 
* [Autentisering med X.509 CA-certifikat](../iot-hub/iot-hub-x509ca-overview.md)
* [Konceptuell förståelse för X.509 CA-certifikat i IoT-branschen](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Skapa enhetens identitet med självsignerat X.509-certifikat

För X.509 självsignerat autentisering, kallas ibland tumavtryck för autentisering, måste du skapa nya certifikat att placera på din IoT-enhet. De här certifikaten har ett tumavtryck i dem som du delar med IoT Hub för autentisering. 

Det enklaste sättet att testa det här scenariot är att använda samma dator som du använde för att skapa certifikat i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md). Den datorn bör redan konfigureras med rätt verktyg, rotcertifikatutfärdarens certifikat och mellanliggande certifikatutfärdare att skapa IoT-enhetscertifikat. Du kan kopiera de slutliga certifikat och privata nycklar över till en underordnad enhet efteråt. Följande steg i artikeln om datagatewayer du ställa in openssl på din dator och sedan klona IoT Edge-lagringsplatsen att skapa skript för åtkomst till certifikatet. Sedan kan du gjort en arbetskatalog som vi kallar  **\<WRKDIR >** att lagra certifikaten. Standard-certifikat är avsedda för utveckling och testning, så bara de senaste 30 dagarna. Du ska ha skapat en rotcertifikatutfärdarens certifikat och ett mellanliggande certifikat. 

1. Gå till din arbetskatalog i ett bash- eller PowerShell-fönster. 

2. Skapa två certifikat (primära och sekundära) för den underordnade enheten. Ange enhetens namn och sedan på den primära eller sekundära etiketten. Den här informationen som filnamn så att du kan hålla reda på certifikat för flera enheter. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Hämta SHA1-fingeravtryck (kallas ett tumavtryck i IoT Hub-gränssnittet) från varje certifikat som är en 40 hexadecimala tecken lång sträng. Du kan använda kommandot openssl för att visa certifikatet och hitta fingeravtrycket:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Gå till din IoT-hubb i Azure-portalen och skapa en ny enhetsidentitet i IoT med följande värden: 

   * Välj **självsignerat X.509** som autentiseringstyp.
   * Klistra in de hexadecimala strängar som du kopierade från enhetens primära och sekundära certifikat.
   * Välj **ange en överordnad enhet** och välj IoT Edge-gatewayenhet som underordnade enheten ansluter via. En överordnad enhet krävs för X.509-autentisering för en underordnad enhet. 

   ![Skapa enhets-ID med självsignerat X.509-autentisering i portalen](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Kopiera följande filer till valfri katalog i en underordnad enhet:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Du måste referera till dessa filer i löv enhetsprogram som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [säker kopia protoco](https://www.ssh.com/ssh/scp/) att flytta certifikatdatabasen.

Du kan använda den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) att slutföra åtgärden för skapande av samma enhet. I följande exempel skapar en ny IoT-enhet med självsignerat X.509-autentisering och tilldelar en överordnad-enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Mer information om Azure CLI-kommandon för enheten skapas, skapande av certifikat och överordnade och underordnade hantering finns i Referensinnehåll för [az iot hub-enhetsidentitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Skapa enheten identitet med X.509 CA-signerat certifikat

För X.509-certifikat (certifikatutfärdare) signerade autentisering behöver du ett rot-CA-certifikat som registrerats i IoT Hub som används för att signera certifikat för din IoT-enheter. Alla enheter som använder ett certifikat som har problem med certifikatet för rotcertifikatutfärdaren eller någon av dess mellanliggande certifikat tillåts att autentisera. 

Det här avsnittet baseras på anvisningarna som beskrivs i IoT Hub-artikel [Konfigurera X.509 säkerheten i Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md). Följ stegen i det här avsnittet veta vilka värden som ska användas för att ställa in en underordnad enhet som ansluter via en gateway. 

Det enklaste sättet att testa det här scenariot är att använda samma dator som du använde för att skapa certifikat i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md). Den datorn bör redan konfigureras med rätt verktyg, rotcertifikatutfärdarens certifikat och mellanliggande certifikatutfärdare att skapa IoT-enhetscertifikat. Du kan kopiera de slutliga certifikat och privata nycklar över till en underordnad enhet efteråt. Följande steg i artikeln om datagatewayer du ställa in openssl på din dator och sedan klona IoT Edge-lagringsplatsen att skapa skript för åtkomst till certifikatet. Sedan kan du gjort en arbetskatalog som vi kallar  **\<WRKDIR >** att lagra certifikaten. Standard-certifikat är avsedda för utveckling och testning, så bara de senaste 30 dagarna. Du ska ha skapat en rotcertifikatutfärdarens certifikat och ett mellanliggande certifikat. 

1. Följ instruktionerna i den [registrera X.509 CA-certifikat till din IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) delen av *Konfigurera X.509 säkerheten i Azure IoT hub*. I avsnittet, kan du utföra följande steg: 

   1. Ladda upp ett rot-CA-certifikat. Om du använder de certifikat som du skapade i den transparenta gatewayartikeln överför  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** som filen med rotcertifikatet. 
   2. Kontrollera att du äger det rot-CA-certifikatet. Du kan kontrollera tillgång med cert-verktygen i \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Följ instruktionerna i den [skapa en X.509-enhet för IoT-hubben](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) delen av *Konfigurera X.509 säkerheten i Azure IoT hub*. I avsnittet, kan du utföra följande steg: 

   1. Lägg till en ny enhet. Ange ett gemener namn för **enhets-ID**, och välj autentiseringstypen **X.509 CA-signerat**. 
   2. Ange en överordnad-enhet. För efterföljande enheter väljer **ange en överordnad enhet** och välj IoT Edge-gatewayenhet som tillhandahåller anslutningen till IoT Hub. 

3. Skapa en certifikatkedja för en underordnad enhet. Använd samma rot-CA-certifikat som du överfört till IoT Hub för att göra den här kedjan. Använd samma gemener enhets-ID som du gav till din enhetsidentitet i portalen.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Kopiera följande filer till valfri katalog i en underordnad enhet: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Du måste referera till dessa filer i löv enhetsprogram som ansluter till IoT Hub. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [säker kopia protoco](https://www.ssh.com/ssh/scp/) att flytta certifikatdatabasen.

Du kan använda den [IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension) att slutföra åtgärden för skapande av samma enhet. I följande exempel skapar en ny IoT-enhet med X.509 CA-signerat autentisering och tilldelar en överordnad-enhet: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Mer information om Azure CLI-kommandon för enheten skapandet och hanteringen av överordnad/underordnad finns Referensinnehåll för [az iot hub-enhetsidentitet](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) kommandon.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Ansluta till IoT Hub via en gateway

Varje Azure IoT SDK: N hanterar X.509-autentisering på olika sätt. Dock samma process som används för att autentisera vanliga IoT-enheter till IoT Hub med X.509-certifikat gäller även för efterföljande enheter. Den enda skillnaden är att du behöver lägga till en pekare till gatewayenheten för att dirigera anslutningen eller offline-scenarier, hantera autentisering för IoT Hub. I allmänhet kan du följa X.509-autentisering likadant för alla IoT Hub-enheter och sedan bara Ersätt värdet för **värdnamn** i anslutningssträngen är värdnamnet för din gatewayenhet. 

I följande avsnitt visas några exempel på olika språk i SDK. 

>[!IMPORTANT]
>Följande exempel visar hur SDK: er för IoT Hub använder certifikat för autentisering av enheter. I en Produktionsdistribution, bör du lagra alla hemligheter som privat eller SAS-nycklar i en säker maskinvara-modul (HSM). 

#### <a name="net"></a>.NET

Ett exempel på en C# programmerar autentisering i IoT Hub med X.509-certifikat, finns i [Konfigurera X.509 säkerheten i Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Några viktiga rader av exemplet finns med här för att demonstrera autentiseringsprocessen.

När du deklarerar värdnamnet för din DeviceClient-instans, använda IoT Edge-gatewayenhet värdnamn. Värdnamnet kan hittas i gateway-enheten config.yaml-filen. 

Om du använder testcertifikat som tillhandahålls av IoT Edge git-lagringsplats att certifikaten är **1234**.

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

Ett exempel på ett program i C autentisering i IoT Hub med X.509-certifikat, finns i C IoT SDK [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) exemplet. Några viktiga rader av exemplet finns med här för att demonstrera autentiseringsprocessen.

När du definierar anslutningssträngen för en underordnad enhet använder IoT Edge-gatewayenhet värdnamnet för den **värdnamn** parametern. Värdnamnet kan hittas i gateway-enheten config.yaml-filen. 

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

Ett exempel på ett Node.js-program autentisering i IoT Hub med X.509-certifikat, finns i Node.js IoT SDK [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) exemplet. Några viktiga rader av exemplet finns med här för att demonstrera autentiseringsprocessen.

När du definierar anslutningssträngen för en underordnad enhet använder IoT Edge-gatewayenhet värdnamnet för den **värdnamn** parametern. Värdnamnet kan hittas i gateway-enheten config.yaml-filen. 

Om du använder testcertifikat som tillhandahålls av IoT Edge git-lagringsplats att certifikaten är **1234**.

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

Ett exempel på ett Python-program autentisering i IoT Hub med X.509-certifikat, finns i Java IoT SDK [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) exemplet. Några viktiga rader av exemplet finns med här för att demonstrera autentiseringsprocessen.

När du definierar anslutningssträngen för en underordnad enhet använder IoT Edge-gatewayenhet värdnamnet för den **värdnamn** parametern. Värdnamnet kan hittas i gateway-enheten config.yaml-filen. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Ett exempel på ett Java-program autentisering i IoT Hub med X.509-certifikat, finns i Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) exemplet. Några viktiga rader av exemplet finns med här för att demonstrera autentiseringsprocessen.

När du definierar anslutningssträngen för en underordnad enhet använder IoT Edge-gatewayenhet värdnamnet för den **värdnamn** parametern. Värdnamnet kan hittas i gateway-enheten config.yaml-filen. 

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

Genom att slutföra den här artikeln bör du ha en IoT Edge-enhet som fungerar som en transparent gateway och en underordnad enhet som registrerats med en IoT-hubb. Därefter måste du konfigurera dina underordnade enheter för att lita på gateway-enheten och skicka meddelanden till den. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).
