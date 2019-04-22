---
title: Security slutpunkter i IoT Device Provisioning-tjänsten | Microsoft Docs
description: Begrepp - Kontrollera åtkomst till IoT Device Provisioning-tjänsten för backend-appar. Innehåller information om säkerhetstoken.
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495438"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrollera åtkomst till Azure IoT Hub Device Provisioning-tjänsten

Den här artikeln beskrivs alternativ för att skydda IoT Device Provisioning-tjänsten. Provisioning-tjänsten använder *behörigheter* att bevilja åtkomst till varje slutpunkt. Användarbehörigheter begränsar åtkomsten till en tjänstinstans som baseras på funktionen.

Den här artikeln beskrivs:

* De olika behörigheter som du kan bevilja en backend-app för att få åtkomst till din etableringstjänst.
* Autentiseringen och token som används för att kontrollera behörigheterna.

### <a name="when-to-use"></a>När du ska använda detta

Du måste ha behörighet att komma åt någon av etablering Tjänsteslutpunkter. Till exempel måste en backend-app innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till tjänsten.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan bevilja [behörigheter](#device-provisioning-service-permissions) på följande sätt:

* **Delad åtkomst auktoriseringsprinciper**. Principer för delad åtkomst kan ge olika kombinationer av [behörigheter](#device-provisioning-service-permissions). Du kan definiera principer i den [Azure-portalen][lnk-management-portal], eller via programmering med hjälp av den [Device Provisioning Service REST API: er][lnk-resource-provider-apis]. En nyligen skapade etableringstjänst har standardprincipen för följande:

* **provisioningserviceowner**: Princip med alla behörigheter.

> [!NOTE]
> Se [behörigheter](#device-provisioning-service-permissions) detaljerad information.

## <a name="authentication"></a>Authentication

Azure IoT Hub Device Provisioning Service ger åtkomst till slutpunkterna genom att verifiera en token mot principer för delad åtkomst. Autentiseringsuppgifter för säkerhet, till exempel symmetriska nycklar skickas aldrig över nätverket.

> [!NOTE]
> Resursprovidern Device Provisioning-tjänsten skyddas via din Azure-prenumeration, eftersom alla leverantörer i den [Azure Resource Manager][lnk-azure-resource-manager].

Mer information om hur du skapar och använder säkerhetstoken finns i nästa avsnitt.

HTTP är det enda protokollet som stöds och den implementerar autentisering genom att inkludera en giltig token i den **auktorisering** huvudet i begäran.

#### <a name="example"></a>Exempel
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> Den [Azure IoT Device Provisioning Service SDK] [ lnk-sdks] automatiskt generera token när du ansluter till tjänsten.

## <a name="security-tokens"></a>Säkerhetstoken

Device Provisioning-tjänsten använder säkerhetstoken för att autentisera tjänster för att undvika att skicka nycklarna för anslutningen. Dessutom begränsas säkerhetstoken i giltighetstid och omfång. [Azure IoT Device Provisioning Service SDKs] [ lnk-sdks] automatiskt generera token utan någon specialkonfiguration. Vissa scenarier kräver att du kan skapa och använda säkerhetstoken direkt. Sådana scenarier är direkt användning av HTTP-angrepp.

### <a name="security-token-structure"></a>Token säkerhetsstruktur

Du kan använda säkerhetstoken för att bevilja begränsad tid åtkomst för specifika funktioner i IoT Device Provisioning Service-tjänster. Om du vill få behörighet att ansluta till etableringstjänsten skicka tjänster säkerhetstoken som har signerats med en delad åtkomst eller symmetrisk nyckel.

En token som signerats med en delad åtkomst nyckel ger åtkomst till alla funktioner som är associerade med behörigheter för delad åtkomst-principen. 

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signature} |En HMAC-SHA256 signatur sträng med formatet: `{URL-encoded-resourceURI} + "\n" + expiry`. **Viktiga**: Nyckeln är avkodas från base64 och används som nyckel för att utföra HMAC-SHA256-beräkningen.|
| {expiry} |UTF8-strängar för antal sekunder sedan epoch 00:00:00 UTC på 1 januari 1970. |
| {URL-encoded-resourceURI} | Lägre mål-URL-kodning av gemen resurs-URI. URI-prefix (efter segment) för slutpunkter som kan användas med denna token från och med värdnamnet för IoT Device Provisioning-tjänsten (inga protocol). Till exempel `mydps.azure-devices-provisioning.net`. |
| {policyName} |Namnet på den princip för delad åtkomst som denna token refererar. |

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
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Eftersom token giltighetstid har verifierats på IoT Device Provisioning Service-datorer kan måste drift på klockan på den dator som genererar token vara minimal.

### <a name="use-security-tokens-from-service-components"></a>Använda säkerhetstoken från tjänstkomponenter

Tjänstkomponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som beviljar behörighet enligt beskrivningen ovan.

Här följer de servicefunktioner som exponeras för slutpunkter:

| Slutpunkt | Funktioner |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Tillhandahåller åtgärder för registrering av enhet med Device Provisioning-tjänsten. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Tillhandahåller åtgärder för att hantera grupper för registrering av enheter. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Tillhandahåller åtgärder för att hämta och hantera statusen för enhetsregistreringar. |


Exempelvis en tjänst som genereras med hjälp av en skapats i förväg delad åtkomstprincip som kallas **enrollmentread** skulle skapa en token med följande parametrar:

* resurs-URI: `{mydps}.azure-devices-provisioning.net`,
* nyckel för signeringscertifikatet: en av nycklarna för den `enrollmentread` principen
* Principnamn: `enrollmentread`,
* alla time.backn upphör att gälla

![Skapa en princip för delad åtkomst för Device Provisioning-tjänstinstans i portalen][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet, vilket skulle ge åtkomst för att läsa alla registreringsposter, skulle bli:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referensämnen:

Följande referens ger dig mer information om hur du styr åtkomst till din IoT Device Provisioning-tjänsten.

### <a name="device-provisioning-service-permissions"></a>Behörigheter för Device Provisioning-tjänsten

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till IoT Device Provisioning-tjänsten.

| Behörighet | Anteckningar |
| --- | --- |
| **ServiceConfig** |Beviljar åtkomst till att ändra tjänstkonfigurationer. <br/>Den här behörigheten används av backend-molntjänster. |
| **EnrollmentRead** |Ger läsbehörighet till enhetsregistreringar och registreringsgrupper. <br/>Den här behörigheten används av backend-molntjänster. |
| **EnrollmentWrite** |Ger skrivåtkomst till enhetsregistreringar och registreringsgrupper. <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistrationStatusRead** |Beviljar skrivskyddad åtkomst till status på enhetsregistreringen. <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistrationStatusWrite**  |Beviljar borttagningsåtkomst till status på enhetsregistreringen. <br/>Den här behörigheten används av backend-molntjänster. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
