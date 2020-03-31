---
title: Säkerhetsslutpunkter i IoT-enhetsetableringstjänsten | Microsoft-dokument
description: Begrepp - hur du styr åtkomsten till DPS (IoT Device Provisioning Service) för backend-appar. Innehåller information om säkerhetstoken.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 2a7e0932d226b1533c039b8529c2c11de06cf525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79285154"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrollera åtkomst till Azure IoT Hub-etableringstjänst

I den här artikeln beskrivs alternativen för att skydda tjänsten för IoT-enhetsetablering. Etableringstjänsten använder *behörigheter* för att bevilja åtkomst till varje slutpunkt. Behörigheter begränsar åtkomsten till en tjänstinstans baserat på funktioner.

I den här artikeln beskrivs:

* De olika behörigheter som du kan bevilja en serverd-app för att komma åt etableringstjänsten.
* Autentiseringsprocessen och de token som används för att verifiera behörigheter.

### <a name="when-to-use"></a>När du ska använda detta

Du måste ha lämpliga behörigheter för att komma åt någon av slutpunkterna för etableringstjänsten. En serverd-app måste till exempel innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till tjänsten.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan bevilja [behörigheter](#device-provisioning-service-permissions) på följande sätt:

* **Principer för behörighet för delad åtkomst**. Principer för delad åtkomst kan bevilja valfri kombination av [behörigheter](#device-provisioning-service-permissions). Du kan definiera principer i [Azure-portalen][lnk-management-portal]eller programmässigt med hjälp av [REST-API:erna för tjänsten För etablering][lnk-resource-provider-apis]av enhet . En nyskapade etableringstjänst har följande standardprincip:

* **etableringserviceägare**: Princip med alla behörigheter.

> [!NOTE]
> Se [behörigheter](#device-provisioning-service-permissions) för detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub Device Provisioning Service ger åtkomst till slutpunkter genom att verifiera en token mot principerna för delad åtkomst. Säkerhetsreferenser, till exempel symmetriska nycklar, skickas aldrig över kabeln.

> [!NOTE]
> Resursleverantören enhetsetableringstjänst skyddas via din Azure-prenumeration, liksom alla leverantörer i [Azure Resource Manager][lnk-azure-resource-manager].

Mer information om hur du skapar och använder säkerhetstoken finns i nästa avsnitt.

HTTP är det enda protokoll som stöds och implementerar autentisering genom att inkludera en giltig token i huvudet **för auktoriseringsbegäran.**

#### <a name="example"></a>Exempel
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT-enhetsetableringstjänsten SDK:er][lnk-sdks] genererar automatiskt token när du ansluter till tjänsten.

## <a name="security-tokens"></a>Säkerhetstoken

Enhetsetableringstjänsten använder säkerhetstoken för att autentisera tjänster för att undvika att skicka nycklar på kabeln. Dessutom är säkerhetstoken begränsade i tidsgiltighet och omfattning. [Azure IoT Device Provisioning Service SDK:er][lnk-sdks] genererar automatiskt token utan att kräva någon särskild konfiguration. Vissa scenarier kräver att du genererar och använder säkerhetstoken direkt. Sådana scenarier inkluderar direkt användning av HTTP-ytan.

### <a name="security-token-structure"></a>Struktur för säkerhetstoken

Du använder säkerhetstoken för att bevilja tidsbestämmd åtkomst för tjänster till specifika funktioner i IoT-enhetsetableringstjänsten. För att få auktorisering att ansluta till etableringstjänsten måste tjänster skicka säkerhetstoken signerade med antingen en delad åtkomst eller symmetrisk nyckel.

En token som signerats med en delad åtkomstnyckel ger åtkomst till alla funktioner som är associerade med principbehörigheter för delad åtkomst. 

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signatur} |En HMAC-SHA256 signatur sträng `{URL-encoded-resourceURI} + "\n" + expiry`i formuläret: . **Viktigt:** Nyckeln avkodas från base64 och används som nyckel för att utföra HMAC-SHA256-beräkningen.|
| {utgångsdatum} |UTF8 strängar för antal sekunder sedan epoken 00:00:00 UTC den 1 januari 1970. |
| {URL-kodad-resourceURI} | Gemener URL-kodning av den gemene resursen URI. URI-prefix (per segment) för de slutpunkter som kan nås med den här token, med början med värdnamnet för IoT-enhetsetableringstjänsten (inget protokoll). Till exempel `mydps.azure-devices-provisioning.net`. |
| {policyName} |Namnet på den delade åtkomstprincip som den här token refererar till. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Som en jämförelse är motsvarande Python-kod för att generera en säkerhetstoken:

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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Eftersom tokens tidsgiltighet har validerats på IoT Device Provisioning Service-datorer, måste avdriften på klockan för den dator som genererar token vara minimal.

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänstkomponenter

Tjänstkomponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som ger rätt behörigheter som tidigare förklarats.

Här är tjänstfunktionerna som visas på slutpunkterna:

| Slutpunkt | Funktioner |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Tillhandahåller enhetsregistreringsåtgärder med enhetsetableringstjänsten. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Tillhandahåller åtgärder för att hantera enhetsregistreringsgrupper. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Tillhandahåller åtgärder för att hämta och hantera status för enhetsregistreringar. |


Som ett exempel skulle en tjänst som genereras med hjälp av en förskapad princip för delad åtkomst som kallas **enrollmentread** skapa en token med följande parametrar:

* resurs URI: `{mydps}.azure-devices-provisioning.net`,
* signeringsnyckel: en `enrollmentread` av nycklarna till principen,
* principens `enrollmentread`namn: ,
* någon utgångstid.backn

![Skapa en princip för delad åtkomst för tjänstinstansen för enhetsetablering i portalen][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, som skulle ge åtkomst till att läsa alla registreringsposter, skulle bli:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referensämnen:

Följande referensavsnitt ger dig mer information om hur du kontrollerar åtkomsten till tjänsten för etablering av IoT-enhet.

### <a name="device-provisioning-service-permissions"></a>Behörigheter för enhetsetableringstjänst

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till tjänsten för etablering av IoT-enhet.

| Behörighet | Anteckningar |
| --- | --- |
| **ServiceConfig (ServiceConfig)** |Ger åtkomst till ändring av tjänstkonfigurationerna. <br/>Den här behörigheten används av serverdmolntjänster. |
| **RegistreringLäs** |Ger läsbehörighet till enhetsregistreringar och registreringsgrupper. <br/>Den här behörigheten används av serverdmolntjänster. |
| **RegistrationWrite** |Ger skrivåtkomst till enhetsregistreringar och registreringsgrupper. <br/>Den här behörigheten används av serverdmolntjänster. |
| **RegistreringStatusLäs** |Ger läsåtkomst till enhetens registreringsstatus. <br/>Den här behörigheten används av serverdmolntjänster. |
| **AnmälanStatusSkriva**  |Beviljar ta bort åtkomst till enhetens registreringsstatus. <br/>Den här behörigheten används av serverdmolntjänster. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
