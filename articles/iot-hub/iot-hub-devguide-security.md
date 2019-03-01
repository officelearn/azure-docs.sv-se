---
title: Förstå Azure IoT Hub-säkerhet | Microsoft Docs
description: Developer guide – Kontrollera åtkomst till IoT Hub för enhetsappar och backend-appar. Innehåller information om säkerhetstoken och stöd för X.509-certifikat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5d360e0959a61cde763f5aafe62de38f6c03e806
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011185"
---
# <a name="control-access-to-iot-hub"></a>Styra åtkomst till IoT Hub

Den här artikeln beskrivs alternativ för att skydda din IoT-hubb. IoT Hub använder *behörigheter* att bevilja åtkomst till varje IoT hub-slutpunkt. Användarbehörigheter begränsar åtkomsten till en IoT-hubb som baseras på funktionen.

Den här artikeln innehåller:

* De olika behörigheter som du kan bevilja till en enhet eller backend-åtkomst till din IoT-hubb.
* Autentiseringen och token som används för att kontrollera behörigheterna.
* Så här att definiera omfattningen av autentiseringsuppgifter för att begränsa åtkomsten till specifika resurser.
* IoT Hub-stöd för X.509-certifikat.
* Anpassade autentiseringsmekanismer som använder befintliga identitets-enhetsregister eller autentiseringsscheman.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Du måste ha behörighet att komma åt någon av IoT Hub-slutpunkter. Till exempel måste en enhet innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till IoT Hub.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan bevilja [behörigheter](#iot-hub-permissions) på följande sätt:

* **IoT hub-nivå delade åtkomstprinciper**. Principer för delad åtkomst kan ge olika kombinationer av [behörigheter](#iot-hub-permissions). Du kan definiera principer i den [Azure-portalen](https://portal.azure.com), via programmering med hjälp av den [IoT Hub Resource REST API: er](/rest/api/iothub/iothubresource), eller med hjälp av den [az iot hub policy](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. En nyligen skapade IoT-hubb har följande standardprinciper:
  
  | Policy för delad åtkomst | Behörigheter |
  | -------------------- | ----------- |
  | iothubowner | Alla behörigheter |
  | tjänst | **ServiceConnect** behörigheter |
  | enhet | **DeviceConnect** behörigheter |
  | registryRead | **RegistryRead** behörigheter |
  | registryReadWrite | **RegistryRead** och **RegistryWrite** behörigheter |

* **Per enhet säkerhetsreferenser**. Varje IoT-hubb innehåller ett [identitetsregistret](iot-hub-devguide-identity-registry.md) för varje enhet i den här identitetsregister, kan du konfigurera säkerhetsreferenser som beviljar **DeviceConnect** behörigheter som är begränsade till motsvarande enheten slutpunkter.

Till exempel i en typisk IoT-lösning:

* Hanteringskomponenten enheten använder de *registryReadWrite* princip.
* Processor-händelsekomponenten använder den *service* princip.
* Business logic-komponenten för körning enheten använder de *service* princip.
* Enskilda enheter ansluta med hjälp av autentiseringsuppgifter som lagras i IoT-hubbens identitetsregister.

> [!NOTE]
> Se [behörigheter](#iot-hub-permissions) detaljerad information.

## <a name="authentication"></a>Authentication

Azure IoT Hub ger åtkomst till slutpunkterna genom att verifiera en token mot principer för delad åtkomst och identitet säkerhetsreferenser för registret.

Autentiseringsuppgifter för säkerhet, till exempel symmetriska nycklar skickas aldrig över nätverket.

> [!NOTE]
> Azure IoT Hub-resursprovidern säkras via din Azure-prenumeration, eftersom alla leverantörer i den [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

Läs mer om hur du skapar och använder säkerhetstoken [säkerhetstoken i IoT Hub](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokollet ärendets

Varje protokoll som stöds, till exempel MQTT-, AMQP- och HTTPS, transporterar token på olika sätt.

När du använder MQTT CONNECT-paket har deviceId som ClientId, `{iothubhostname}/{deviceId}` i fältet för användarnamn och en SAS-token i lösenordsfältet. `{iothubhostname}` ska vara fullständig CName för IoT hub (till exempel contoso.azure-devices.net).

När du använder [AMQP](https://www.amqp.org/), stöds av IoT Hub [SASL OFORMATERAD](http://tools.ietf.org/html/rfc4616) och [AMQP anspråk-baserade-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Om du använder AMQP anspråk baserade-säkerhet anger standarden hur du överför dessa token.

För SASL OFORMATERAD den **användarnamn** kan vara:

* `{policyName}@sas.root.{iothubName}` Om du använder IoT hub på servernivå token.
* `{deviceId}@sas.{iothubname}` Om du använder enheten-omfattande token.

I båda fallen finns lösenordsfältet token, enligt beskrivningen i [säkerhetstoken i IoT Hub](iot-hub-devguide-security.md#security-tokens).

HTTPS implementerar autentisering genom att inkludera en giltig token i den **auktorisering** huvudet i begäran.

#### <a name="example"></a>Exempel

Användarnamn (DeviceId är skiftlägeskänsligt): `iothubname.azure-devices.net/DeviceId`

Lösenord (du kan generera en SAS-token med den [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) verktyget tilläggskommando CLI [az iot hub generera sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), eller [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Den [Azure IoT SDK: er](iot-hub-devguide-sdks.md) automatiskt generera token när du ansluter till tjänsten. I vissa fall kan stöder Azure IoT SDK: er inte alla protokoll eller alla autentiseringsmetoder.

### <a name="special-considerations-for-sasl-plain"></a>Att tänka på för SASL OFORMATERAD

När du använder SASL OFORMATERAD med AMQP kan kan en klient som ansluter till en IoT hub använda en enskild token för varje TCP-anslutning. När token upphör att gälla TCP-anslutningen kopplas från tjänsten och utlöser en återanslutning. Det här beteendet skadas när inte problematiska för serverdelsappen, för en enhetsapp av följande skäl:

* Gateways ansluter vanligtvis för många enheter. När du använder SASL OFORMATERAD, som de behöver skapa en distinkt TCP-anslutning för varje enhet som ansluter till en IoT hub. Det här scenariot avsevärt ökar användningen av power- och nätverksresurser och ökar svarstiden för varje enhetsanslutning om.

* Resursen begränsad enheter påverkas negativt av ökad användning av resurser för att återansluta efter varje token upphör att gälla.

## <a name="scope-iot-hub-level-credentials"></a>Definiera autentiseringsuppgifter för IoT hub-nivå

Du kan begränsa säkerhetsprinciper för IoT hub-nivå genom att skapa token med en begränsad resurs-URI. Slutpunkt för att skicka meddelanden från enhet till moln från en enhet är till exempel **/devices/ {deviceId} / meddelanden/händelser**. Du kan också använda en IoT hub-nivån delad åtkomstprincip med **DeviceConnect** behörighet för att signera en token vars resourceURI är **/devices/ {deviceId}**. Den här metoden skapar en token som endast kan användas för att skicka meddelanden för enhet **deviceId**.

Den här mekanismen liknar den [Händelsehubbar utgivarprincip](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab), och du kan implementera anpassade autentiseringsmetoder.

## <a name="security-tokens"></a>Säkerhetstoken

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklarna för anslutningen. Dessutom begränsas säkerhetstoken i giltighetstid och omfång. [Azure IoT SDK: er](iot-hub-devguide-sdks.md) automatiskt generera token utan någon specialkonfiguration. Vissa scenarier kräver att du kan skapa och använda säkerhetstoken direkt. Sådana scenarier är:

* Direkt användning av MQTT-, AMQP- eller HTTPS-ytor.

* Implementering av mönstret tokentjänsten, enligt beskrivningen i [anpassad autentisering](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub kan också enheter att autentisera med IoT Hub med hjälp av [X.509-certifikat](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Token säkerhetsstruktur

Du kan använda säkerhets-tokens för att bevilja tid begränsad åtkomst till enheter och tjänster till specifika funktioner i IoT Hub. Om du vill få behörighet att ansluta till IoT Hub, skickar enheter och tjänster säkerhetstoken som har signerats med en delad åtkomst eller symmetrisk nyckel. De här nycklarna lagras med en enhetsidentitet i identitetsregistret.

En token som signerats med en delad åtkomst nyckel ger åtkomst till alla funktioner som är associerade med behörigheter för delad åtkomst-principen. En token som signerats med en enhetsidentitet symmetrisk nyckel endast ger det **DeviceConnect** behörighet för associerade enhetens identitet.

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signature} |En HMAC-SHA256 signatur sträng med formatet: `{URL-encoded-resourceURI} + "\n" + expiry`. **Viktiga**: Nyckeln är avkodas från base64 och används som nyckel för att utföra HMAC-SHA256-beräkningen. |
| {resourceURI} |URI-prefix (efter segment) för slutpunkter som kan användas med denna token från och med värdnamnet för IoT hub (inga protocol). Till exempel, `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-strängar för antal sekunder sedan epoch 00:00:00 UTC på 1 januari 1970. |
| {URL-encoded-resourceURI} |Lägre fall URL-kodning av gemen resurs-URI |
| {policyName} |Namnet på den princip för delad åtkomst som denna token refererar. Inte fram om token som refererar till autentiseringsuppgifter för registrering av enheter. |

**Observera angående prefix**: URI-prefix beräknas efter segment och inte tecken. Till exempel `/a/b` är ett prefix för `/a/b/c` men inte för `/a/bc`.

Följande kodfragment i Node.js visar en funktion som kallas **generateSasToken** som beräknar token från indata `resourceUri, signingKey, policyName, expiresInMins`. I nästa avsnitt förklarar vi hur du initierar olika indata för olika token användningsfall.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Som en jämförelse är motsvarande Python-koden för att generera en säkerhetstoken:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

Funktioner i C# för att generera en säkerhetstoken är:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}

```


> [!NOTE]
> Eftersom token giltighetstid har verifierats på IoT Hub-datorer kan måste drift på klockan på den dator som genererar token vara minimal.

### <a name="use-sas-tokens-in-a-device-app"></a>Använd SAS-token i en app för enheter

Det finns två sätt att hämta **DeviceConnect** behörigheter med IoT Hub med säkerhetstoken: använda en [symmetriska enhetsnyckel från identitetsregistret](#use-a-symmetric-key-in-the-identity-registry), eller Använd en [delade åtkomstnyckeln](#use-a-shared-access-policy).

Kom ihåg att alla funktioner från enheter exponeras avsiktligt på slutpunkter med prefixet `/devices/{deviceId}`.

> [!IMPORTANT]
> Det enda sättet att IoT Hub autentiserar en specifik enhet använder enhetens identitet symmetrisk nyckel. I fall när en princip för delad åtkomst används för åtkomst till enheten funktionerna betrakta lösningen den komponent som utfärdar säkerhetstoken som en betrodd underkomponenten.

Enhet-slutpunkter är (oavsett protocol):

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Skicka meddelanden från enheten till molnet. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ta emot meddelanden från molnet till enheten. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Använda en symmetrisk nyckel i identitetsregistret

När du använder en enhetsidentitet symmetrisk nyckel för att generera en token, namn på princip (`skn`) elementet i token har utelämnats.

En token som skapats för att få åtkomst till alla funktioner i enheten bör till exempel ha följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* nyckel för signeringscertifikatet: alla symmetrisk nyckel för den `{device id}` identitet,
* Inget principnamn
* helst upphör att gälla.

Ett exempel med hjälp av den föregående Node.js-funktionen är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1 skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Det är möjligt att skapa en SAS-token med den [enhetsutforskare](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) verktyget tilläggskommando CLI [az iot hub generera sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token), eller [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Använda en princip för delad åtkomst

När du skapar en token från en princip för delad åtkomst i `skn` fältet med namnet på principen. Den här principen måste ge den **DeviceConnect** behörighet.

Det finns två huvudscenarier för att använda principer för delad åtkomst för åtkomst till funktionerna för enheten:

* [molnet protokollgatewayar](iot-hub-devguide-endpoints.md),
* [token tjänster](iot-hub-devguide-security.md#custom-device-and-module-authentication) används för att implementera anpassade autentiseringsmetoder.

Eftersom princip för delad åtkomst kan potentiellt bevilja åtkomst till ansluter som en enhet, är det viktigt att du använder rätt resurs-URI när du skapar säkerhetstoken. Den här inställningen är särskilt viktigt för token-tjänster som har att definiera omfattningen av token till en specifik enhet med hjälp av resurs-URI. Nu är relevanta för protokollgatewayar som de redan fördelar trafik för alla enheter.

Till exempel en token service med hjälp av skapats i förväg delad åtkomstprincip som kallas **enhet** skulle skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* nyckel för signeringscertifikatet: en av nycklarna för den `device` principen
* Principnamn: `device`,
* helst upphör att gälla.

Ett exempel med hjälp av den föregående Node.js-funktionen är:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1 skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

En protokoll-gateway kan använda samma token för alla enheter som är helt enkelt ställa resurs-URI att `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänstkomponenter

Tjänstkomponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som beviljar behörighet enligt beskrivningen ovan.

Här följer de servicefunktioner som exponeras för slutpunkter:

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices` |Skapa, uppdatera, hämta och ta bort enhetsidentiteter. |
| `{iot hub host name}/messages/events` |Ta emot meddelanden från enheten till molnet. |
| `{iot hub host name}/servicebound/feedback` |Få feedback om meddelanden från molnet till enheten. |
| `{iot hub host name}/devicebound` |Skicka meddelanden från molnet till enheten. |

Till exempel en tjänst som genererar med hjälp av skapats i förväg delad åtkomstprincip som kallas **registryRead** skulle skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices`,
* nyckel för signeringscertifikatet: en av nycklarna för den `registryRead` principen
* Principnamn: `registryRead`,
* helst upphör att gälla.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, vilket skulle ge åtkomst för att läsa alla enhetsidentiteter, skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>X.509-certifikat som stöds

Du kan använda valfri X.509-certifikat för autentisering av en enhet med IoT Hub genom att ladda upp ett tumavtryck för certifikat eller en certifikatutfärdare (CA) till Azure IoT Hub. Autentisering med hjälp av endast certifikattumavtryck verifierar att det presenterade tumavtrycket matchar det konfigurerade tumavtrycket. Autentisering med hjälp av certifikatutfärdare verifierar certifikatkedjan. 

Certifikat som stöds är:

* **Ett befintligt X.509-certifikat**. En enhet kan redan ha ett X.509-certifikat som är kopplade till den. Enheten kan använda det här certifikatet för att autentisera med IoT Hub. Fungerar med tumavtryck eller CA-autentisering. 

* **Certifikatutfärdarsignerat X.509-certifikat**. För att identifiera en enhet och autentisera med IoT Hub, kan du använda ett X.509-certifikat som genereras och signerade av en certifikatutfärdare (CA). Fungerar med tumavtryck eller CA-autentisering.

* **En lokal genereras och självsignerade certifikat för x-509**. En tillverkare eller interna deployer kan generera dessa certifikat och lagra den motsvarande privata nyckel (och certifikatet) på enheten. Du kan använda verktyg som [OpenSSL](https://www.openssl.org/) och [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) utility för detta ändamål. Fungerar bara med tumavtryck för autentisering. 

En enhet kan antingen använda ett X.509-certifikat eller en säkerhetstoken för autentisering, men inte båda.

Mer information om autentisering med hjälp av certifikatutfärdare finns i [autentisering med X.509 CA-certifikat](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrera ett X.509-certifikat för en enhet

Den [Azure IoT Service SDK för C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service) (version 1.0.8+) har stöd för registrering av en enhet som använder ett X.509-certifikat för autentisering. API: er, till exempel import/export av enheter har också stöd för X.509-certifikat.

Du kan också använda CLI-tillägg-kommando [az iot hub-enhetsidentitet](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) Konfigurera X.509-certifikat för enheter.

### <a name="c-support"></a>C\# Support

Den **RegistryManager** klassen innehåller en programmässiga sätt att registrera en enhet. I synnerhet de **AddDeviceAsync** och **UpdateDeviceAsync** metoder kan du registrera och uppdatera en enhet i IoT Hub-identitetsregistret. Dessa två metoder använder en **enhet** instans som indata. Den **enhet** klassen innehåller en **autentisering** egenskap som gör det möjligt att ange primära och sekundära X.509-certifikattumavtryck. Tumavtrycket representerar en SHA256-hash för X.509-certifikat (lagras med hjälp av binär DER-kodning). Du har möjlighet att ange primära tumavtrycket eller ett sekundärt tumavtryck eller båda. Primära och sekundära tumavtryck stöds för att hantera scenarier för förnyelse av certifikat.

Här är ett exempel på C\# kodfragmentet att registrera en enhet med ett X.509-tumavtryck för certifikat:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Använd ett X.509-certifikat under körning

Den [Azure IoT-enhetens SDK för .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device) (version 1.0.11+) stöder användning av X.509-certifikat.

### <a name="c-support"></a>C\# Support

Klassen **DeviceAuthenticationWithX509Certificate** stöder skapandet av **DeviceClient** instanser med ett X.509-certifikat. X.509-certifikatet måste vara i formatet PFX (kallas även för PKCS #12) som innehåller den privata nyckeln.

Här är ett exempel kodfragment:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Anpassad enhet och modulen autentisering

Du kan använda IoT-hubben [identitetsregistret](iot-hub-devguide-identity-registry.md) att konfigurera per-enheten/module säkerhetsreferenser och komma åt kontroll med [token](iot-hub-devguide-security.md#security-tokens). Om en IoT-lösning har redan en anpassad identitet registret och/eller autentisering schema, kan du skapa en *token service* att integrera den här infrastrukturen med IoT Hub. På så sätt kan använda du andra IoT-funktioner i din lösning.

En token service är en anpassad molntjänst. Den använder en IoT-hubb *delad åtkomstprincip* med **DeviceConnect** eller **ModuleConnect** behörighet att skapa *enhet-omfattande* eller *modul-omfattande* token. Dessa token kan en enhet och modulen ska kunna ansluta till din IoT hub.

![Stegen i mönstret token service](./media/iot-hub-devguide-security/tokenservice.png)

Här är de viktigaste anvisningarna för tokentjänsten mönster:

1. Skapa en IoT Hub delad åtkomstprincip med **DeviceConnect** eller **ModuleConnect** behörigheter för din IoT-hubb. Du kan skapa den här principen i den [Azure-portalen](https://portal.azure.com) eller programmässigt. Tokentjänsten som använder den här principen för att signera token skapas.

2. När en enhetsmodulen behöver åtkomst till din IoT-hubb, begär en signerade token från din token-tjänst. Enheten kan autentisera med ditt anpassad identitet register-/ autentiseringsschema fastställa enhetsmodulen /-identiteten som token-tjänsten använder för att skapa en token.

3. Tokentjänsten som returnerar en token. Token har skapats med hjälp av `/devices/{deviceId}` eller `/devices/{deviceId}/module/{moduleId}` som `resourceURI`, med `deviceId` som en enhet som autentiseras eller `moduleId` som modulen som autentiseras. Tokentjänsten som använder princip för delad åtkomst för att konstruera token.

4. Enheten/modulen använder token direkt med IoT hub.

> [!NOTE]
> Du kan använda .NET-klass [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) eller Java-klass [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) att skapa en token i token-tjänsten.

Tokentjänsten som kan ange giltighetstid för token som du vill. När token upphör att gälla, ned IoT-hubben enhetsmodulen /. Enheten/modulen måste sedan begära en ny token från token-tjänsten. En kort förfallotid ökar belastningen på både enhetsmodulen och token-tjänsten.

För en enhet/modul för att ansluta till din hubb, måste du fortfarande lägga till den IoT Hub-identitetsregistret – trots att den använder en token och inte en nyckel för att ansluta. Därför kan du kan fortsätta att använda per enhet/per-zakazuje åtkomstkontroll genom att aktivera eller inaktivera enhetsmodulen/identiteter i den [identitetsregistret](iot-hub-devguide-identity-registry.md). Den här metoden minskar riskerna med att använda token med lång giltighetstid.

### <a name="comparison-with-a-custom-gateway"></a>Jämförelse med en anpassad gateway

Mönstret token service är det rekommenderade sättet att implementera en anpassad identitet registret/autentiseringsschema med IoT Hub. Det här mönstret rekommenderas eftersom IoT Hub fortsätter att hantera de flesta av lösningen trafiken. Om anpassade autentiseringsschemat så sammanflätade med protokollet, du kan kräva en *anpassade gateway* att bearbeta all trafik. Ett exempel på sådant scenario använder [Transport Layer Security (TLS) och i förväg delade nycklar (PSKs)](https://tools.ietf.org/html/rfc4279). Mer information finns i den [protokollgatewayen](iot-hub-protocol-gateway.md) artikeln.

## <a name="reference-topics"></a>Referensämnen:

Du får mer information om hur du styr åtkomst till din IoT-hubb i följande referensavsnitt.

## <a name="iot-hub-permissions"></a>IoT Hub-behörigheter

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till din IoT-hubb.

| Behörighet | Anteckningar |
| --- | --- |
| **RegistryRead** |Ger läsåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistryReadWrite** |Ger Läs- och skrivåtkomst till identitetsregistret. Mer information finns i [identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **ServiceConnect** |Beviljar åtkomst till tjänsten webbservergrupper kommunikation och övervaka slutpunkter i molnet. <br/>Ger behörighet att ta emot meddelanden från enheten till molnet, skicka meddelanden från moln till enhet och hämta motsvarande leverans bekräftelser. <br/>Ger behörighet att hämta leverans bekräftelser för filen laddas upp. <br/>Ger behörighet till åtkomst twins att uppdatera taggar och önskade egenskaper, rapporterade egenskaper att hämta och köra frågor. <br/>Den här behörigheten används av backend-molntjänster. |
| **DeviceConnect** |Beviljar åtkomst till enheten-slutpunkter. <br/>Ger behörighet att skicka meddelanden från enheten till molnet och ta emot meddelanden från molnet till enheten. <br/>Ger behörighet att utföra ladda upp filer från en enhet. <br/>Ger behörighet att ta emot enhetsmeddelanden enhetstvillingens egenskapen och uppdatera enhetstvillingen rapporterade egenskaper. <br/>Ger behörighet att utföra fil laddas upp. <br/>Den här behörigheten används av enheter. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitten i IoT Hub developer guide inkluderar:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körning och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver kvoter och begränsningar beteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enheten och tjänsten SDK: er](iot-hub-devguide-sdks.md) visar en lista över olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT Hub.

* [IoT Hub-frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingar och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du styr åtkomst till IoT Hub, kanske du är intresserad av i följande avsnitt för IoT Hub developer guide:

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

I följande självstudier får IoT Hub om du vill prova några av de koncept som beskrivs i den här artikeln:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Hur du skickar meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Behandla meddelanden från IoT Hub-enhet till molnet](tutorial-routing.md)
