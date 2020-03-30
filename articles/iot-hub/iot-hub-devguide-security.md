---
title: Förstå Azure IoT Hub-säkerhet | Microsoft-dokument
description: Utvecklarguide – så här styr du åtkomsten till IoT Hub för enhetsappar och backend-appar. Innehåller information om säkerhetstoken och stöd för X.509-certifikat.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 47eae55493c5db281ee1be0f9d32f8f8190fc286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272063"
---
# <a name="control-access-to-iot-hub"></a>Styra åtkomst till IoT Hub

I den här artikeln beskrivs alternativen för att skydda din IoT-hubb. IoT Hub använder *behörigheter* för att bevilja åtkomst till varje IoT-hubbslutpunkt. Behörigheter begränsar åtkomsten till en IoT-hubb baserat på funktioner.

Denna artikel introducerar:

* De olika behörigheter som du kan bevilja en enhet eller backend-app för att komma åt din IoT-hubb.
* Autentiseringsprocessen och de token som används för att verifiera behörigheter.
* Så här begränsar du autentiseringsuppgifter för att begränsa åtkomsten till specifika resurser.
* IoT Hub-stöd för X.509-certifikat.
* Anpassade enhetsautentiseringsmekanismer som använder befintliga enhetsidentitetsregister eller autentiseringsscheman.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Du måste ha rätt behörighet för att komma åt någon av IoT Hub-slutpunkterna. En enhet måste till exempel innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till IoT Hub.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan bevilja [behörigheter](#iot-hub-permissions) på följande sätt:

* **Principer för delad åtkomst på IoT-hubbnivå**. Principer för delad åtkomst kan bevilja valfri kombination av [behörigheter](#iot-hub-permissions). Du kan definiera principer i [Azure-portalen](https://portal.azure.com), programmässigt med hjälp av [IoT Hub Resource REST API:er](/rest/api/iothub/iothubresource)eller använda AZ [iot hub-principen](/cli/azure/iot/hub/policy?view=azure-cli-latest) CLI. En nyligen skapad IoT-hubb har följande standardprinciper:
  
  | Princip för delad åtkomst | Behörigheter |
  | -------------------- | ----------- |
  | iothubowner | All behörighet |
  | tjänst | **ServiceConnect-behörigheter** |
  | enhet | **Behörigheter för DeviceConnect** |
  | registretLäs | **Behörigheter för RegistereditornLäs** |
  | registerLässkriva | **Behörigheter för RegisterLäs** och **Registerwrite** |

* **Säkerhetsreferenser per enhet**. Varje IoT-hubb innehåller ett [identitetsregister](iot-hub-devguide-identity-registry.md) För varje enhet i det här identitetsregistret kan du konfigurera säkerhetsautentiseringsuppgifter som ger **DeviceConnect-behörigheter** begränsade till motsvarande enhetsslutpunkter.

Till exempel i en typisk IoT-lösning:

* Enhetshanteringskomponenten använder *principen registeredreadwrite.*
* Händelseprocessorkomponenten *service* använder tjänstprincipen.
* Affärslogiken för körningsenhet använder *service* tjänstprincipen.
* Enskilda enheter ansluter med autentiseringsuppgifter som lagras i IoT-hubbens identitetsregister.

> [!NOTE]
> Se [behörigheter](#iot-hub-permissions) för detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub beviljar åtkomst till slutpunkter genom att verifiera en token mot principerna för delad åtkomst och säkerhetsautentiseringsuppgifter för identitetsregistret.

Säkerhetsreferenser, till exempel symmetriska nycklar, skickas aldrig över kabeln.

> [!NOTE]
> Azure IoT Hub-resursleverantören skyddas via din Azure-prenumeration, liksom alla leverantörer i [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Mer information om hur du skapar och använder säkerhetstoken finns i [IoT Hub-säkerhetstoken](iot-hub-devguide-security.md#security-tokens).

### <a name="protocol-specifics"></a>Protokollspecifiken

Varje protokoll som stöds, till exempel MQTT, AMQP och HTTPS, transporterar token på olika sätt.

När du använder MQTT har CONNECT-paketet deviceId `{iothubhostname}/{deviceId}` som ClientId, i fältet Användarnamn och en SAS-token i fältet Lösenord. `{iothubhostname}`bör vara det fullständiga CName för IoT-hubben (till exempel contoso.azure-devices.net).

När IoT Hub använder [AMQP](https://www.amqp.org/)stöder IoT Hub [SASL PLAIN](https://tools.ietf.org/html/rfc4616) och [AMQP-anspråksbaserad säkerhet](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Om du använder AMQP-anspråksbaserad säkerhet anger standarden hur dessa token ska överföras.

För SASL PLAIN kan **användarnamnet** vara:

* `{policyName}@sas.root.{iothubName}`om du använder token på IoT-hubbnivå.
* `{deviceId}@sas.{iothubname}`om du använder enhetsscoped token.

I båda fallen innehåller lösenordsfältet token, enligt beskrivningen i [IoT Hub-säkerhetstoken](iot-hub-devguide-security.md#security-tokens).

HTTPS implementerar autentisering genom att inkludera en giltig token i tillägget **Auktoriseringsbegäran.**

#### <a name="example"></a>Exempel

Användarnamn (DeviceId är skiftlägeskänsligt):`iothubname.azure-devices.net/DeviceId`

Lösenord (Du kan generera en SAS-token med [enhetsutforskaren,](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) kommandot CLI-tillägg [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)eller [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> [Azure IoT SDK:er](iot-hub-devguide-sdks.md) genererar automatiskt token när du ansluter till tjänsten. I vissa fall stöder Azure IoT-SDK:erna inte alla protokoll eller alla autentiseringsmetoder.

### <a name="special-considerations-for-sasl-plain"></a>Särskilda överväganden för SASL PLAIN

När du använder SASL PLAIN med AMQP kan en klient som ansluter till en IoT-hubb använda en enda token för varje TCP-anslutning. När token upphör att gälla kopplas TCP-anslutningen från tjänsten och utlöser en återanslutning. Det här beteendet är skadligt för en enhetsapp även om det inte är problematiskt för en backend-app:

* Gateways ansluter vanligtvis för många enheter. När du använder SASL PLAIN måste de skapa en distinkt TCP-anslutning för varje enhet som ansluter till en IoT-hubb. Det här scenariot ökar avsevärt förbrukningen av ström och nätverksresurser och ökar svarstiden för varje enhetsanslutning.

* Resursbegränsade enheter påverkas negativt av den ökade användningen av resurser för att återansluta efter varje tokenförfallodatum.

## <a name="scope-iot-hub-level-credentials"></a>Autentiseringsuppgifter för scope-IoT-hubbnivå

Du kan begränsa säkerhetsprinciper på IoT-hubbnivå genom att skapa token med en begränsad resurs-URI. Slutpunkten för att skicka meddelanden från enhet till moln från en enhet är till exempel **/devices/{deviceId}/messages/events**. Du kan också använda en princip för delad åtkomst på IoT-hubbnivå med **DeviceConnect-behörigheter** för att signera en token vars resourceURI är **/devices/{deviceId}**. Den här metoden skapar en token som endast kan kan skickas för att skicka meddelanden för **deviceId**.

Den här mekanismen liknar [utgivarprincipen för eventhubbar](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)och gör att du kan implementera anpassade autentiseringsmetoder.

## <a name="security-tokens"></a>Säkerhetstoken

IoT Hub använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar på kabeln. Dessutom är säkerhetstoken begränsade i tidsgiltighet och omfattning. [Azure IoT SDK:er](iot-hub-devguide-sdks.md) genererar automatiskt token utan att kräva någon särskild konfiguration. Vissa scenarier kräver att du genererar och använder säkerhetstoken direkt. Sådana scenarier omfattar:

* Direkt användning av MQTT-, AMQP- eller HTTPS-ytorna.

* Implementeringen av tokentjänstmönstret, som förklaras i [Anpassad enhetsautentisering](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub gör det också möjligt för enheter att autentisera med IoT Hub med [X.509-certifikat](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Struktur för säkerhetstoken

Du använder säkerhetstoken för att bevilja tidsbestängd åtkomst till enheter och tjänster till specifika funktioner i IoT Hub. För att få auktorisering för att ansluta till IoT Hub måste enheter och tjänster skicka säkerhetstoken signerade med antingen en delad åtkomst eller symmetrisk nyckel. Dessa nycklar lagras med en enhetsidentitet i identitetsregistret.

En token som signerats med en delad åtkomstnyckel ger åtkomst till alla funktioner som är associerade med principbehörigheter för delad åtkomst. En token som signerats med en enhetsidentitets symmetriska nyckel ger endast **DeviceConnect-behörigheten** för den associerade enhetsidentiteten.

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signatur} |En HMAC-SHA256 signatur sträng `{URL-encoded-resourceURI} + "\n" + expiry`i formuläret: . **Viktigt:** Nyckeln avkodas från base64 och används som nyckel för att utföra HMAC-SHA256-beräkningen. |
| {resourceURI} |URI-prefix (per segment) för slutpunkter som kan nås med den här token, med början med värdnamnet för IoT-hubben (inget protokoll). Till exempel, `myHub.azure-devices.net/devices/device1` |
| {utgångsdatum} |UTF8 strängar för antal sekunder sedan epoken 00:00:00 UTC den 1 januari 1970. |
| {URL-kodad-resourceURI} |Gemener URL-kodning av den gemena resursen URI |
| {policyName} |Namnet på den delade åtkomstprincip som den här token refererar till. Frånvarande om token refererar till enhetsregisterautentiseringsuppgifter. |

**Anmärkning på prefix:** URI-prefixet beräknas per segment och inte efter tecken. Till `/a/b` exempel är ett `/a/b/c` prefix `/a/bc`för men inte för .

Följande Node.js-kodavsnitt visar en funktion som kallas **generateSasToken** som `resourceUri, signingKey, policyName, expiresInMins`beräknar token från indata . I nästa avsnitt beskrivs hur du initierar de olika indata för de olika tokenanvändningsfallen.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Som en jämförelse är motsvarande Python-kod för att generera en säkerhetstoken:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

Följande är installationsanvisningarna för förutsättningarna.

[!INCLUDE [Iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]


Funktionerna i C# för att generera en säkerhetstoken är:

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
> Eftersom tokens tidsgiltighet har validerats på IoT Hub-datorer måste driften på klockan för den dator som genererar token vara minimal.

### <a name="use-sas-tokens-in-a-device-app"></a>Använda SAS-token i en enhetsapp

Det finns två sätt att hämta **DeviceConnect-behörigheter** med IoT Hub med säkerhetstoken: använd en [symmetrisk enhetsnyckel från identitetsregistret](#use-a-symmetric-key-in-the-identity-registry)eller använd en [delad åtkomstnyckel](#use-a-shared-access-policy).

Kom ihåg att alla funktioner som är tillgängliga från enheter `/devices/{deviceId}`exponeras av design på slutpunkter med prefix .

> [!IMPORTANT]
> Det enda sättet att IoT Hub autentiserar en viss enhet är att använda symmetrisk knapp för enhetsidentitet. I de fall en princip för delad åtkomst används för att komma åt enhetsfunktioner måste lösningen betrakta komponenten som utfärdar säkerhetstoken som en betrodd underkomponent.

De enhetsvända slutpunkterna är (oavsett protokoll):

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Skicka meddelanden från enhet till moln. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ta emot meddelanden från molnet till enheten. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Använda en symmetrisk nyckel i identitetsregistret

När du använder en enhetsidentitets symmetriska nyckel`skn`för att generera en token utelämnas elementet policyName ( ).

En token som skapats för att komma åt alla enhetsfunktioner bör till exempel ha följande parametrar:

* resurs URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* Signeringsnyckel: alla `{device id}` symmetriska nyckel för identiteten.
* Inget policynamn.
* utgångstiden.

Ett exempel med funktionen föregående nod.js skulle vara:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Det är möjligt att generera en SAS-token med [verktyget för enhetsutforskaren,](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) kommandot CLI-tillägg [az iot hub generate-sas-token](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token)eller [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Använda en princip för delad åtkomst

När du skapar en token från en `skn` princip för delad åtkomst anger du fältet till namnet på principen. Den här principen måste bevilja **Behörigheten DeviceConnect.**

De två huvudscenarierna för att använda principer för delad åtkomst för att komma åt enhetsfunktioner är:

* [molnprotokoll gateways](iot-hub-devguide-endpoints.md),
* [tokentjänster](iot-hub-devguide-security.md#custom-device-and-module-authentication) som används för att implementera anpassade autentiseringsscheman.

Eftersom principen för delad åtkomst potentiellt kan ge åtkomst till att ansluta som vilken enhet som helst, är det viktigt att använda rätt resurs-URI när du skapar säkerhetstoken. Den här inställningen är särskilt viktig för tokentjänster, som måste begränsa token till en viss enhet med hjälp av resursen URI. Den här punkten är mindre relevant för protokollgateways eftersom de redan förmedlar trafik för alla enheter.

Ett exempel skapar en tokentjänst med den förskapade principen för delad åtkomst som kallas **enhet** en token med följande parametrar:

* resurs URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* signeringsnyckel: en `device` av nycklarna till principen,
* principens `device`namn: ,
* utgångstiden.

Ett exempel med funktionen föregående nod.js skulle vara:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som ger åtkomst till alla funktioner för device1, skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

En protokollgateway kan använda samma token för alla `myhub.azure-devices.net/devices`enheter som helt enkelt anger resursen URI till .

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänstkomponenter

Tjänstkomponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som ger rätt behörigheter som tidigare förklarats.

Här är tjänstfunktionerna som visas på slutpunkterna:

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices` |Skapa, uppdatera, hämta och ta bort enhetsidentiteter. |
| `{iot hub host name}/messages/events` |Ta emot meddelanden från enhet till moln. |
| `{iot hub host name}/servicebound/feedback` |Få feedback för meddelanden från molnet till enheten. |
| `{iot hub host name}/devicebound` |Skicka meddelanden från molnet till enheten. |

Ett exempel skapar en tjänst som genererar med den förskapade principen för delad åtkomst som kallas **registryRead** en token med följande parametrar:

* resurs URI: `{IoT hub name}.azure-devices.net/devices`,
* signeringsnyckel: en `registryRead` av nycklarna till principen,
* principens `registryRead`namn: ,
* utgångstiden.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som skulle ge tillgång till läsa alla enhetsidentiteter, skulle bli:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>X.509-certifikat som stöds

Du kan använda alla X.509-certifikat för att autentisera en enhet med IoT Hub genom att ladda upp antingen ett certifikattumavtryck eller en certifikatutfärdare till Azure IoT Hub. Autentisering med hjälp av certifikattumavtryck verifierar att det presenterade tumavtrycket matchar det konfigurerade tumavtrycket. Autentisering med certifikatutfärdare validerar certifikatkedjan. Hursomhelst, TLS handslag kräver enheten att ha ett giltigt certifikat och privat nyckel. Se TLS-specifikationen för mer information, till exempel: [RFC 5246 - TLS-protokollversionen (Transport Layer Security) Version 1.2](https://tools.ietf.org/html/rfc5246/).

Certifikat som stöds omfattar:

* **Ett befintligt X.509-certifikat**. En enhet kanske redan har ett X.509-certifikat kopplat till den. Enheten kan använda det här certifikatet för att autentisera med IoT Hub. Fungerar med antingen tumavtryck eller CA-autentisering. 

* **CA-signerat X.509-certifikat**. Om du vill identifiera en enhet och autentisera den med IoT Hub kan du använda ett X.509-certifikat som genererats och signerats av en certifikatutfärdare. Fungerar med antingen tumavtryck eller CA-autentisering.

* **Ett självgenererat och självsignerat X-509-certifikat**. En enhetstillverkare eller intern deployer kan generera dessa certifikat och lagra motsvarande privata nyckel (och certifikat) på enheten. Du kan använda verktyg som [OpenSSL](https://www.openssl.org/) och [Windows SelfSignedCertificate-verktyget](/powershell/module/pkiclient/new-selfsignedcertificate) för detta ändamål. Fungerar bara med autentisering av tumavtryck. 

En enhet kan antingen använda ett X.509-certifikat eller en säkerhetstoken för autentisering, men inte båda.

Mer information om autentisering med certifikatutfärdaren finns i [Enhetsautentisering med X.509 CA-certifikat](iot-hub-x509ca-overview.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrera ett X.509-certifikat för en enhet

[Azure IoT Service SDK för C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (version 1.0.8+) stöder registrering av en enhet som använder ett X.509-certifikat för autentisering. Andra API:er, till exempel import/export av enheter, stöder också X.509-certifikat.

Du kan också använda kommandot CLI extension [az iot hub device-identity](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) för att konfigurera X.509-certifikat för enheter.

### <a name="c-support"></a>C\# Stöd

**Klassen RegistryManager** tillhandahåller ett programmatiskt sätt att registrera en enhet. I synnerhet med metoderna **AddDeviceAsync** och **UpdateDeviceAsync** kan du registrera och uppdatera en enhet i IoT Hub-identitetsregistret. Dessa två metoder tar en **enhetsinstans** som indata. Klassen **Device** innehåller en **autentiseringsegenskap** som gör att du kan ange primära och sekundära X.509-certifikattumavtryck. Tumavtrycket representerar en SHA256-hash i X.509-certifikatet (lagras med binär DER-kodning). Du kan välja att ange ett primärt tumavtryck eller ett sekundärt tumavtryck eller båda. Primära och sekundära tumavtryck stöds för att hantera scenarier för certifikatöverrullning.

Här är ett\# exempel C-kodavsnitt för att registrera en enhet med hjälp av ett X.509-certifikat tumavtryck:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Använda ett X.509-certifikat under körningsåtgärder

[Azure IoT-enheten SDK för .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (version 1.0.11+) stöder användning av X.509-certifikat.

### <a name="c-support"></a>C\# Stöd

Klassen **DeviceAuthenticationWithX509Certificate** stöder skapandet av **DeviceClient-instanser** med ett X.509-certifikat. X.509-certifikatet måste finnas i PFX-formatet (kallas även PKCS #12) som innehåller den privata nyckeln.

Här är ett exempel kodavsnitt:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Anpassad enhets- och modulautentisering

Du kan använda IoT [Hub-identitetsregistret](iot-hub-devguide-identity-registry.md) för att konfigurera säkerhetsautentiseringsuppgifter för per enhet/modul och åtkomstkontroll med hjälp av [token](iot-hub-devguide-security.md#security-tokens). Om en IoT-lösning redan har ett anpassat identitetsregister och/eller autentiseringsschema kan du överväga att skapa en *tokentjänst* för att integrera den här infrastrukturen med IoT Hub. På så sätt kan du använda andra IoT-funktioner i din lösning.

En tokentjänst är en anpassad molntjänst. Den använder en IoT Hub *delad åtkomstprincip* med **DeviceConnect-** eller **ModuleConnect-behörigheter** för att skapa *enhetsscoped* eller *modul-scoped* token. Dessa token gör det möjligt för en enhet och modul att ansluta till din IoT-hubb.

![Steg i tokentjänstmönstret](./media/iot-hub-devguide-security/tokenservice.png)

Här är de viktigaste stegen i tokentjänstmönstret:

1. Skapa en princip för delad åtkomst i IoT Hub med **Behörigheterna DeviceConnect** eller **ModuleConnect** för din IoT-hubb. Du kan skapa den här principen i [Azure-portalen](https://portal.azure.com) eller programmässigt. Tokentjänsten använder den här principen för att signera de token den skapar.

2. När en enhet/modul behöver komma åt din IoT-hubb begär den en signerad token från din tokentjänst. Enheten kan autentisera med ditt anpassade identitetsregister/autentiseringsschema för att fastställa enhets-/modulidentiteten som tokentjänsten använder för att skapa token.

3. Tokentjänsten returnerar en token. Token skapas med `/devices/{deviceId}` `/devices/{deviceId}/module/{moduleId}` hjälp `resourceURI`av `deviceId` eller som , `moduleId` med som enheten autentiseras eller som modulen autentiseras. Tokentjänsten använder principen delad åtkomst för att konstruera token.

4. Enheten/modulen använder token direkt med IoT-hubben.

> [!NOTE]
> Du kan använda klassen .NET [SharedAccessSignatureBuilder](https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx) eller Java-klassen [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) för att skapa en token i tokentjänsten.

Tokentjänsten kan ange tokenförfallodatum som önskat. När token upphör att gälla avbryts IoT-hubben enhets-/modulanslutningen. Sedan måste enheten/modulen begära en ny token från tokentjänsten. En kort utgångstid ökar belastningen på både enheten/modulen och tokentjänsten.

För att en enhet/modul ska kunna ansluta till hubben måste du ändå lägga till den i IoT Hub-identitetsregistret – även om den använder en token och inte en nyckel för att ansluta. Därför kan du fortsätta att använda åtkomstkontroll per enhet/per modul genom att aktivera eller inaktivera enhets-/modulidentiteter i [identitetsregistret](iot-hub-devguide-identity-registry.md). Den här metoden minskar riskerna med att använda token med långa utgångstider.

### <a name="comparison-with-a-custom-gateway"></a>Jämförelse med en anpassad gateway

Tokentjänstmönstret är det rekommenderade sättet att implementera ett anpassat identitetsregister/autentiseringsschema med IoT Hub. Det här mönstret rekommenderas eftersom IoT Hub fortsätter att hantera större delen av lösningstrafiken. Men om det anpassade autentiseringsschemat är så sammanflätat med protokollet kan du behöva en *anpassad gateway* för att bearbeta all trafik. Ett exempel på ett sådant scenario är att använda [TLS (Transport Layer Security) och fördelade nycklar](https://tools.ietf.org/html/rfc4279). Mer information finns i artikeln [om protokollgateway.](iot-hub-protocol-gateway.md)

## <a name="reference-topics"></a>Referensämnen:

Följande referensavsnitt ger dig mer information om hur du kontrollerar åtkomsten till din IoT-hubb.

## <a name="iot-hub-permissions"></a>IoT Hub-behörigheter

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till din IoT-hubb.

| Behörighet | Anteckningar |
| --- | --- |
| **RegistryLäs** |Ger läsåtkomst till identitetsregistret. Mer information finns i [Identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **RegisterLässkriva** |Ger läs- och skrivåtkomst till identitetsregistret. Mer information finns i [Identitetsregistret](iot-hub-devguide-identity-registry.md). <br/>Den här behörigheten används av backend-molntjänster. |
| **ServiceConnect** |Ger åtkomst till molntjänstinriktade kommunikations- och övervakningsslutpunkter. <br/>Ger behörighet att ta emot meddelanden från enhet till moln, skicka meddelanden från molnet till enheten och hämta motsvarande leveransbekräftelser. <br/>Ger behörighet att hämta leveransbekräftelser för filöverföringar. <br/>Ger behörighet att komma åt tvillingar för att uppdatera taggar och önskade egenskaper, hämta rapporterade egenskaper och köra frågor. <br/>Den här behörigheten används av backend-molntjänster. |
| **DeviceConnect (Enhetsanslutning)** |Ger åtkomst till enhetsvända slutpunkter. <br/>Ger behörighet att skicka meddelanden från enhet till moln och ta emot meddelanden från molnet till enheten. <br/>Ger behörighet att utföra filöverföring från en enhet. <br/>Ger behörighet att ta emot enhetstvilling önskade egenskapsmeddelanden och uppdatera enhetstvillingrapporterade egenskaper. <br/>Ger behörighet att utföra filöverföringar. <br/>Den här behörigheten används av enheter. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensavsnitt i utvecklarhandboken för IoT Hub är:

* [IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsningsbeteenden som gäller för IoT Hub-tjänsten.

* [Azure IoT-enhet och tjänst SDK visar](iot-hub-devguide-sdks.md) de olika språk SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub-frågespråket](iot-hub-devguide-query-language.md) beskriver det frågespråk som du kan använda för att hämta information från IoT Hub om dina enhetstvillingar och jobb.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information om IoT Hub-stöd för MQTT-protokollet.

* [RFC 5246 - TLS-protokollversionen (Transport Layer Security) version 1.2](https://tools.ietf.org/html/rfc5246/) innehåller mer information om TLS-autentisering.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du styr access-IoT Hub kan du vara intresserad av följande IoT Hub-utvecklarguideavsnitt:

* [Använda enhetstvillingar för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)
* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)
* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill prova några av de begrepp som beskrivs i den här artikeln läser du följande IoT Hub-självstudier:

* [Komma igång med Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Så här skickar du meddelanden från molnet till enheten med IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Så här bearbetar du IoT Hub-meddelanden från enhet till moln](tutorial-routing.md)
