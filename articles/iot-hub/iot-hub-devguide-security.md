---
title: Förstå Azure IoT Hub säkerheten | Microsoft Docs
description: Utvecklare guide - hur du styr åtkomst till IoT-hubb för appar för enheter och backend-appar. Innehåller information om säkerhetstoken och stöd för X.509-certifikat.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 45631e70-865b-4e06-bb1d-aae1175a52ba
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2018
ms.author: dobett
ms.openlocfilehash: d1f9d1a9163eee0f3a6c3b418e5e8d4fec0581de
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="control-access-to-iot-hub"></a>Styra åtkomst till IoT Hub

Den här artikeln beskrivs alternativ för att skydda din IoT-hubb. IoT-hubb använder *behörigheter* att bevilja åtkomst till varje IoT hub-slutpunkt. Behörigheter som begränsar åtkomsten till en IoT-hubb som baseras på funktionen.

Den här artikeln innehåller:

* De olika behörigheterna som du kan bevilja en enhet eller backend-app åtkomst till din IoT-hubb.
* Autentiseringen och token som används för att verifiera behörigheter.
* Så här scope autentiseringsuppgifter för att begränsa åtkomsten till specifika resurser.
* IoT-hubb stöd för X.509-certifikat.
* Anpassade autentiseringsmekanismer som använder befintliga enhetens identitet register eller autentiseringsscheman.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Du måste ha behörighet att komma åt IoT-hubb-slutpunkter. Till exempel måste en enhet innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till IoT-hubb.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan ge [behörigheter](#iot-hub-permissions) på följande sätt:

* **IoT-hubb-nivå delade åtkomstprinciper**. Principer för delad åtkomst kan ge en kombination av [behörigheter](#iot-hub-permissions). Du kan definiera principer i den [Azure-portalen][lnk-management-portal], eller programmässigt med hjälp av den [IoT-hubb resursprovidern REST API: er][lnk-resource-provider-apis]. En nyligen skapade IoT-hubben har standardprinciperna för följande:

  * **iothubowner**: princip med alla behörigheter.
  * **tjänsten**: princip med **ServiceConnect** behörighet.
  * **enheten**: princip med **DeviceConnect** behörighet.
  * **registryRead**: princip med **RegistryRead** behörighet.
  * **registryReadWrite**: princip med **RegistryRead** och RegistryWrite behörigheter.
  * **Per enhet säkerhetsreferenser**. Varje IoT-hubb innehåller en [identitetsregistret][lnk-identity-registry]. Du kan konfigurera säkerhetsreferenser som ger för varje enhet i den här identitetsregistret **DeviceConnect** behörigheter som är begränsade till motsvarande enhet slutpunkter.

Till exempel i en typisk IoT-lösningen:

* Komponenten device management använder den *registryReadWrite* princip.
* Processorkomponent händelse använder den *service* princip.
* Komponenten körning enheten business logic använder den *service* princip.
* Enskilda enheter ansluter med autentiseringsuppgifter som lagras i IoT-hubben identitetsregistret.

> [!NOTE]
> Se [behörigheter](#iot-hub-permissions) detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT-hubb ger åtkomst till slutpunkter genom att verifiera en token mot principer för delad åtkomst och identitet säkerhetsreferenser för registret.

Säkerhetsreferenser, till exempel symmetriska nycklar skickas aldrig via kabel.

> [!NOTE]
> Azure IoT Hub-resursprovidern skyddas via Azure-prenumerationen, eftersom alla providrar på den [Azure Resource Manager][lnk-azure-resource-manager].

Mer information om hur du skapar och använder säkerhetstoken finns [IoT-hubb säkerhetstoken][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Protokollet programvarukrav

Varje protokoll som stöds, till exempel MQTT, AMQP och HTTPS, transport token på olika sätt.

När du använder MQTT CONNECT-paket har deviceId som ClientId, `{iothubhostname}/{deviceId}` i fältet för användarnamn och en SAS-token i fältet lösenord. `{iothubhostname}` ska vara fullständig CName för IoT-hubb (till exempel contoso.azure-devices.net).

När du använder [AMQP][lnk-amqp], IoT-hubb stöder [SASL OFORMATERAD] [ lnk-sasl-plain] och [AMQP anspråk baserade-säkerhet] [ lnk-cbs].

Om du använder AMQP anspråk baserade-säkerhet anger standarden så att överföra dessa token.

För SASL OFORMATERAD den **användarnamn** kan vara:

* `{policyName}@sas.root.{iothubName}` Om du använder token för IoT-hubb-nivå.
* `{deviceId}@sas.{iothubname}` Om du använder enheten omfång token.

I båda fallen lösenordsfältet innehåller token som beskrivs i [IoT-hubb säkerhetstoken][lnk-sas-tokens].

HTTPS implementerar autentisering genom att inkludera en giltig token i den **auktorisering** huvudet i begäran.

#### <a name="example"></a>Exempel

Användarnamn (DeviceId är skiftlägeskänsligt): `iothubname.azure-devices.net/DeviceId`

Lösenord (generera SAS-token med den [enheten explorer] [ lnk-device-explorer] verktyget): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Den [Azure IoT SDK] [ lnk-sdks] automatiskt generera token när du ansluter till tjänsten. I vissa fall stöder inte Azure IoT-SDK: er alla protokoll eller alla autentiseringsmetoder.

### <a name="special-considerations-for-sasl-plain"></a>Att tänka på SASL OFORMATERAD

När du använder SASL OFORMATERAD med AMQP, kan en klient som ansluter till en IoT-hubb använda en enda token för varje TCP-anslutning. När token upphör att gälla TCP-anslutningen kopplas från tjänsten och utlöser en återanslutning. Det här beteendet skadas när det inte problematisk för en backend-app för en enhetsapp av följande skäl:

* Gateway ansluter vanligtvis för många enheter. När du använder SASL OFORMATERAD behöva de skapa en distinkt TCP-anslutning för varje enhet som ansluter till en IoT-hubb. Det här scenariot avsevärt ökar förbrukningen av ström och nätverksresurser och ökar svarstiden för varje enhetsanslutning.
* Begränsad resurs enheter påverkas negativt av ökad användning av resurser för att återansluta efter varje token upphör att gälla.

## <a name="scope-iot-hub-level-credentials"></a>Autentiseringsuppgifter för scope IoT hub-nivå

Du kan definiera säkerhetsprinciper för IoT-hubb-nivå genom att skapa token med en skyddad resurs-URI. Slutpunkten för att skicka meddelanden från enhet till moln från en enhet är till exempel **/devices/ {deviceId} / meddelanden/händelser**. Du kan också använda en IoT-hubb-nivå delad åtkomstprincip med **DeviceConnect** behörighet för att signera en token vars resourceURI är **/devices/ {deviceId}**. Den här metoden skapar en token som endast kan användas för att skicka meddelanden för enheten **deviceId**.

Den här mekanismen liknar den [Händelsehubbar utgivarprincip][lnk-event-hubs-publisher-policy], och du kan implementera anpassade autentiseringsmetoder.

## <a name="security-tokens"></a>Säkerhetstoken

IoT-hubb använder säkerhetstoken för att autentisera enheter och tjänster för att undvika att skicka nycklar i uppkopplat läge. Dessutom är säkerhetstoken begränsad giltighetstid och omfång. [Azure IoT-SDK] [ lnk-sdks] automatiskt generera token utan någon specialkonfiguration. Vissa scenarier behöver du skapa och använda säkerhetstoken direkt. Sådana scenarier är:

* Direkt användning av de MQTT, AMQP eller HTTPS.
* Implementeringen av tokentjänsten mönster, enligt beskrivningen i [anpassad enhetsautentisering][lnk-custom-auth].

IoT-hubb kan också enheter att autentisera med IoT-hubb med [X.509-certifikat][lnk-x509].

### <a name="security-token-structure"></a>Säkerhet token struktur

Du kan använda säkerhetstoken för att ge tid begränsas åtkomsten till enheter och tjänster till specifika funktioner i IoT-hubb. För att få behörighet att ansluta till IoT-hubb, skickar enheter och tjänster säkerhetstoken som signerade med en delad åtkomst eller en symmetrisk nyckel. Dessa nycklar lagras med en enhetsidentitet i identitetsregistret.

En token som signerats med en delad åtkomst viktiga beviljar åtkomst till alla funktioner som är associerade med principen-behörigheter för delad åtkomst. En token som signerats med en enhetsidentitet symmetrisk nyckel endast ger det **DeviceConnect** för associerade enhetens identitet.

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signatur} |En HMAC SHA256 signatur sträng med formatet: `{URL-encoded-resourceURI} + "\n" + expiry`. **Viktiga**: nyckeln avkoda från base64 och används som nyckel för att utföra HMAC SHA256-beräkningen. |
| {resourceURI} |URI-prefix (av segment) slutpunkter som kan användas med denna token som börjar med värdnamnet för IoT-hubb (ingen protocol). Till exempel, `myHub.azure-devices.net/devices/device1` |
| {utgången} |UTF8 strängar för antal sekunder sedan epok 00:00:00 UTC på 1 januari 1970. |
| {URL-kodade-resourceURI} |Lägre fall URL-kodning av gemen resurs-URI |
| {policyName} |Namnet på den princip för delad åtkomst som den här variabeln refererar. Inte fram om avser token enhetsregistret autentiseringsuppgifter. |

**Observera på prefixet**: URI: N prefix beräknas av segment och inte av tecken. Till exempel `/a/b` är ett prefix för `/a/b/c` men inte för `/a/bc`.

Node.js följande utdrag visar en funktion kallad **generateSasToken** som beräknar token från indata `resourceUri, signingKey, policyName, expiresInMins`. I nästa avsnitt innehåller information om hur du initierar olika indata för olika token användningsfall.

```nodejs
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

    // Construct autorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Som en jämförelse är likvärdiga Python-kod för att generera en säkerhetstoken

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
> Eftersom tokens giltighet verifieras på IoT-hubb datorer måste drift på klockan på den dator som genererar token vara minimal.

### <a name="use-sas-tokens-in-a-device-app"></a>Använd SAS-token i en enhetsapp

Det finns två sätt att hämta **DeviceConnect** behörigheter med IoT-hubb med säkerhetstoken: använda en [symmetriska enhetsnyckel från identitetsregistret](#use-a-symmetric-key-in-the-identity-registry), eller Använd en [delade åtkomstnyckeln](#use-a-shared-access-policy).

Kom ihåg att alla funktioner som är tillgängliga från enheter exponeras avsiktligt på slutpunkter med prefixet `/devices/{deviceId}`.

> [!IMPORTANT]
> Det enda sättet att IoT-hubb autentiserar en enhet använder enheten identitet symmetrisk nyckel. I fall när en princip för delad åtkomst för att komma åt enheten funktioner lösningen måste ta hänsyn till den komponent som utfärdar säkerhetstoken som en betrodd underkomponenten.

Enheten riktade slutpunkter är (oavsett protocol):

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Skicka meddelanden från enhet till moln. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Ta emot meddelanden moln till enhet. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Använd en symmetrisk nyckel i identitetsregistret

När du använder en enhetsidentitet symmetrisk nyckel för att generera en token av principnamn (`skn`) elementet för token har utelämnats.

Till exempel måste en token som skapats för att få åtkomst till alla funktioner i enheten ha följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* nyckel för signeringscertifikatet: en symmetrisk nyckel för den `{device id}` identitet,
* Inga Principnamn
* helst upphör att gälla.

Ett exempel med hjälp av funktionen föregående Node.js är:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Det resultat som ger åtkomst till alla funktioner för device1 skulle vara:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Det är möjligt att generera en SAS-token med hjälp av .NET [enheten explorer] [ lnk-device-explorer] verktyg eller plattformsoberoende, Python-baserade [i IoT-tillägg för Azure CLI 2.0] [ lnk-IoT-extension-CLI-2.0] kommandoradsverktyg.

### <a name="use-a-shared-access-policy"></a>Använda en princip för delad åtkomst

När du skapar en token från en princip för delad åtkomst i `skn` till namnet på principen. Den här principen måste ge den **DeviceConnect** behörighet.

De två huvudsakliga för att använda principer för delad åtkomst för åtkomst till enheten funktionerna är:

* [molnet protokollet gateways][lnk-endpoints],
* [token services] [ lnk-custom-auth] används för att implementera anpassade autentiseringsscheman.

Eftersom princip för delad åtkomst kan potentiellt bevilja åtkomst till Anslut som alla enheter, är det viktigt att du använder rätt resurs-URI när du skapar säkerhetstoken. Den här inställningen är särskilt viktigt för token-tjänster med att definiera omfattningen av token till en specifik enhet med hjälp av resurs-URI. Den här platsen är relevanta för protokollet gateways som de redan fördelar trafik för alla enheter.

Exempelvis en token tjänst med det skapats i förväg delad åtkomstprincip som heter **enhet** skulle skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* nyckel för signeringscertifikatet: en av nycklarna för den `device` principen
* Principnamn: `device`,
* helst upphör att gälla.

Ett exempel med hjälp av funktionen föregående Node.js är:

```nodejs
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Det resultat som ger åtkomst till alla funktioner för device1 skulle vara:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

En protocol-gateway kan använda samma token för alla enheter som bara ange resurs-URI för `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Använd säkerhetstoken från tjänstkomponenter

Tjänstens komponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som beviljar behörighet som tidigare förklarats.

Här är servicefunktioner exponerad på slutpunkter:

| Slutpunkt | Funktioner |
| --- | --- |
| `{iot hub host name}/devices` |Skapa, uppdatera, hämta och ta bort enheten identiteter. |
| `{iot hub host name}/messages/events` |Ta emot meddelanden från enhet till moln. |
| `{iot hub host name}/servicebound/feedback` |Ta emot feedback för moln till enhet meddelanden. |
| `{iot hub host name}/devicebound` |Skicka meddelanden moln till enhet. |

Som exempel, en tjänst som genereras med hjälp av i förväg skapade delad åtkomstprincip som heter **registryRead** skulle skapa en token med följande parametrar:

* resurs-URI: `{IoT hub name}.azure-devices.net/devices`,
* nyckel för signeringscertifikatet: en av nycklarna för den `registryRead` principen
* Principnamn: `registryRead`,
* helst upphör att gälla.

```nodejs
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet som skulle ge åtkomst för att läsa alla enheten identiteter, är:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Stöds X.509-certifikat

Du kan använda alla X.509-certifikat för autentisering av en enhet med IoT-hubben genom att överföra ett certifikat-tumavtryck eller en certifikatutfärdare (CA) till Azure IoT Hub. Autentisering med hjälp av endast certifikattumavtryck verifierar att presenterades tumavtryck matchar det konfigurerade tumavtrycket. Autentisering med hjälp av certifikatutfärdare verifierar certifikatkedjan. 

Certifikat som stöds är:

* **Ett befintligt X.509-certifikat**. En enhet kanske redan har ett X.509-certifikat som är kopplade till den. Enheten kan använda certifikatet för att autentisera med IoT-hubben. Fungerar med tumavtryck eller CA-autentisering. 
* **X.509-certifikat som signerats av Certifikatutfärdaren**. För att identifiera en enhet och autentisera med IoT-hubb, kan du använda ett X.509-certifikat som genereras och signerade av en certifikatutfärdare (CA). Fungerar med tumavtryck eller CA-autentisering.
* **En egen genereras och självsignerade certifikat för X-509**. En enhetstillverkaren eller interna deployer kan generera dessa certifikat och lagra motsvarande privata nyckel (och certifikat) på enheten. Du kan använda verktyg som [OpenSSL] [ lnk-openssl] och [Windows SelfSignedCertificate] [ lnk-selfsigned] utility för detta ändamål. Fungerar bara med tumavtryck för autentisering. 

En enhet kan antingen använda ett X.509-certifikat eller en säkerhetstoken för autentisering, men inte båda.

Mer information om autentisering med hjälp av certifikatutfärdaren finns [grundläggande förståelse för X.509-certifikat](iot-hub-x509ca-concept.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Registrera ett X.509-certifikat för en enhet

Den [Azure IoT Service SDK för C#] [ lnk-service-sdk] (version 1.0.8+) har stöd för registrering av en enhet som använder ett X.509-certifikat för autentisering. Andra API: er som import och export av enheter har också stöd för X.509-certifikat.

### <a name="c-support"></a>C\# stöd

Den **RegistryManager** klassen innehåller en programmässiga sättet att registrera en enhet. I synnerhet de **AddDeviceAsync** och **UpdateDeviceAsync** metoder kan du registrera och uppdatera en enhet i IoT-hubb identitetsregistret. Dessa två metoder ta en **enhet** instansen som indata. Den **enhet** klassen innehåller en **autentisering** egenskap som kan du ange primära och sekundära X.509-certifikattumavtryck. Certifikatets tumavtryck representerar en SHA256-hash för X.509-certifikat (som lagras med hjälp av binär kodning DER). Du har möjlighet att ange tumavtryck för en primär eller sekundär tumavtryck eller båda. Primära och sekundära tumavtryck stöds för att hantera scenarier för förnyelse av certifikat.

Här följer ett exempel på C\# kodfragmentet att registrera en enhet med ett X.509-tumavtryck för certifikat:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Använd ett X.509-certifikat under körning

Den [Azure IoT-enhet SDK för .NET] [ lnk-client-sdk] (version 1.0.11+) stöder användning av X.509-certifikat.

### <a name="c-support"></a>C\# stöd

Klassen **DeviceAuthenticationWithX509Certificate** stöder skapandet av **DeviceClient** instanser med hjälp av ett X.509-certifikat. X.509-certifikatet måste vara i formatet PFX (kallas även PKCS #12) som innehåller den privata nyckeln.

Här är ett kodfragment som exempel:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Anpassad autentisering

Du kan använda IoT-hubben [identitetsregistret] [ lnk-identity-registry] att konfigurera per enhet säkerhetsreferenser och komma åt styra med hjälp av [token][lnk-sas-tokens]. Om det finns redan en anpassad identitet registret och/eller autentisering schemat för en IoT-lösning, kan du överväga att skapa en *token service* att integrera infrastrukturen med IoT-hubben. På så sätt kan använda du andra IoT-funktioner i din lösning.

En token service är en anpassad molntjänst. Den använder en IoT-hubb *delad åtkomstprincip* med **DeviceConnect** behörighet att skapa *enheten omfång* token. Dessa token kan en enhet för att ansluta till din IoT-hubb.

![Steg för tokentjänsten mönster][img-tokenservice]

Här följer Huvudsteg för tokentjänsten mönster:

1. Skapa en IoT-hubb delad åtkomstprincip med **DeviceConnect** behörigheter för din IoT-hubb. Du kan skapa den här principen i den [Azure-portalen] [ lnk-management-portal] eller programmässigt. Tokentjänsten som använder den här principen för att signera token skapas.
1. När en enhet behöver åtkomst till din IoT-hubb, begär en signerade token från token-tjänsten. Enheten kan autentisera med din anpassade identiteten registret-/ autentiseringsschema att fastställa enhetens identitet som tokentjänsten som används för att skapa token.
1. Tokentjänsten som returnerar en token. Token som skapas med hjälp av `/devices/{deviceId}` som `resourceURI`, med `deviceId` som den enhet som autentiseras. Token tjänsten använder princip för delad åtkomst för att skapa token.
1. Enheten använder token direkt med IoT-hubben.

> [!NOTE]
> Du kan använda .NET-klass [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] eller Java-klass [IotHubServiceSasToken] [ lnk-java-sas] att skapa en token i din token-tjänsten.

Tjänsten token kan ange token upphör att gälla efter behov. När token upphör att gälla ned IoT-hubben enhet. Enheten måste sedan begära en ny token från token-tjänsten. En kort förfallotid ökar belastningen på både enheten och tjänsten token.

För en enhet kan ansluta till din hubb, måste du ändå lägga till identitetsregistret IoT-hubb – även om enheten använder en token och inte en enhet för att ansluta. Därför kan du kan fortsätta att använda per enhet åtkomstkontroll genom att aktivera eller inaktivera enheten identiteter i den [identitetsregistret][lnk-identity-registry]. Den här metoden minskar riskerna med att använda token med lång upphör att gälla.

### <a name="comparison-with-a-custom-gateway"></a>Jämförelse med en anpassad gateway

Tokentjänsten mönstret är det rekommenderade sättet att implementera en anpassad identitet registret/autentiseringsschema med IoT-hubben. Det här mönstret rekommenderas eftersom IoT-hubb fortsätter att hantera de flesta av lösningen trafiken. Om anpassade autentiseringsschemat så sammanflätade med protokollet, du kan dock kräva en *anpassade gateway* bearbeta all trafik. Ett exempel på en sådan situation använder[Transport Layer Security (TLS) och i förväg delade nycklar (PSKs)][lnk-tls-psk]. Mer information finns i [protokollgatewayen] [ lnk-protocols] artikel.

## <a name="reference-topics"></a>Referensinformation:

Följande referensavsnitt ge mer information om hur du styr åtkomst till din IoT-hubb.

## <a name="iot-hub-permissions"></a>Behörigheter för IoT-hubb

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till din IoT-hubb.

| Behörighet | Anteckningar |
| --- | --- |
| **RegistryRead** |Ger läsbehörighet till identitetsregistret. Mer information finns i [identitetsregistret][lnk-identity-registry]. <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistryReadWrite** |Ger Läs- och skrivåtkomst till identitetsregistret. Mer information finns i [identitetsregistret][lnk-identity-registry]. <br/>Den här behörigheten används av backend-molntjänster. |
| **ServiceConnect** |Ger åtkomst till cloud service-kommunikation och övervakning av slutpunkter. <br/>Ger behörighet att ta emot meddelanden från enhet till moln, skicka meddelanden moln till enhet och hämta motsvarande leverans bekräftelser. <br/>Ger behörighet att hämta leverans bekräftelser för filen filöverföringar. <br/>Ger behörighet att komma åt enheten twins för att uppdatera taggar och önskade egenskaper, hämta rapporterade egenskaper och köra frågor. <br/>Den här behörigheten används av backend-molntjänster. |
| **DeviceConnect** |Ger åtkomst till enheten riktade slutpunkter. <br/>Ger behörighet att skicka meddelanden från enhet till moln och ta emot meddelanden moln till enhet. <br/>Ger behörighet att utföra Filöverföring från en enhet. <br/>Ger behörighet att ta emot enheten dubbla önskad egenskapen meddelanden och uppdatera enheten dubbla rapporterade egenskaper. <br/>Ger behörighet att utföra filen filöverföringar. <br/>Den här behörigheten används av enheter. |

## <a name="additional-reference-material"></a>Ytterligare referensmaterialet

Andra referensavsnitten i utvecklarhandboken för IoT-hubben är:

* [IoT-hubbslutpunkter] [ lnk-endpoints] beskriver de olika slutpunkter som varje IoT-hubb visar för körning och hanteringsåtgärder.
* [Begränsning och kvoter] [ lnk-quotas] beskriver kvoter och begränsning beteenden som tillämpas på tjänsten IoT-hubb.
* [Azure IoT-enheten och tjänsten SDK] [ lnk-sdks] Listar olika språk SDK: er som du kan använda när du utvecklar appar för både enheten och tjänsten som interagerar med IoT-hubben.
* [IoT-hubb frågespråket] [ lnk-query] beskriver frågespråk som du kan använda för att hämta information från IoT-hubb om enheten twins och jobb.
* [Stöd för IoT-hubb MQTT] [ lnk-devguide-mqtt] ger mer information om stöd för IoT-hubb för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du styr åtkomst IoT-hubb, kan du är intresserad av i följande avsnitt för IoT-hubb developer-guide:

* [Använd twins för enheten för att synkronisera tillstånd och konfigurationer][lnk-devguide-device-twins]
* [Anropa en metod som är direkt på en enhet][lnk-devguide-directmethods]
* [Schema-jobb på flera enheter][lnk-devguide-jobs]

Om du vill testa vissa av de begrepp som beskrivs i den här artikeln finns följande IoT-hubb kurser:

* [Kom igång med Azure IoT-hubb][lnk-getstarted-tutorial]
* [Hur du skickar meddelanden moln till enhet med IoT-hubb][lnk-c2d-tutorial]
* [Bearbeta meddelanden från enhet till moln IoT-hubb][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth._iot_hub_service_sas_token
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-IoT-extension-CLI-2.0]: https://github.com/Azure/azure-iot-cli-extension

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
