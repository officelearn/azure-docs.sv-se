---
title: "Säkerhet slutpunkter i Etableringstjänsten för IoT-enhet | Microsoft Docs"
description: "Begrepp - hur du styr åtkomst till Etableringstjänsten för IoT-enhet för backend-appar. Innehåller information om säkerhetstoken."
services: iot-dps
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-dps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: dkshir,rajeevmv
ms.openlocfilehash: 718fe9b3ca449f8f7b1420080ea75716e8badcf5
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Kontrollera åtkomsten till Azure IoT Hub etablering av tjänst

Den här artikeln beskrivs alternativ för att skydda din etablering tjänst för IoT-enhet. Etablering tjänsten använder *behörigheter* att bevilja åtkomst till varje slutpunkt. Behörigheter som begränsar åtkomsten till en tjänstinstans som baseras på funktionen.

Den här artikeln beskrivs:

* De olika behörigheterna som du kan bevilja en backend-app för att komma åt etablering tjänsten.
* Autentiseringen och token som används för att verifiera behörigheter.

### <a name="when-to-use"></a>När du ska använda detta

Du måste ha behörighet att komma åt etablering Tjänsteslutpunkter. Till exempel måste en backend-app innehålla en token som innehåller säkerhetsreferenser tillsammans med alla meddelanden som skickas till tjänsten.

## <a name="access-control-and-permissions"></a>Åtkomstkontroll och behörigheter

Du kan ge [behörigheter](#device-provisioning-service-permissions) på följande sätt:

* **Delad åtkomst auktoriseringsprinciper**. Principer för delad åtkomst kan ge en kombination av [behörigheter](#device-provisioning-service-permissions). Du kan definiera principer i den [Azure-portalen][lnk-management-portal], eller programmässigt med hjälp av den [enheten etablering Service REST API: er][lnk-resource-provider-apis]. En nyligen skapade etablering tjänst har standardprincipen för följande:

  * **provisioningserviceowner**: princip med alla behörigheter.

> [!NOTE]
> Se [behörigheter](#device-provisioning-service-permissions) detaljerad information.

## <a name="authentication"></a>Autentisering

Azure IoT Hub-enhet Etableringstjänsten ger åtkomst till slutpunkter genom att verifiera en token mot principer för delad åtkomst. Säkerhetsreferenser, till exempel symmetriska nycklar skickas aldrig via kabel.

> [!NOTE]
> Enheten etablering tjänstresursprovider skyddas via Azure-prenumerationen, eftersom alla providrar på den [Azure Resource Manager][lnk-azure-resource-manager].

Mer information om hur du skapar och använder säkerhetstoken finns i nästa avsnitt.

HTTP är det enda protokollet som stöds och den implementerar autentisering genom att inkludera en giltig token i den **auktorisering** huvudet i begäran.

#### <a name="example"></a>Exempel
`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`

> [!NOTE]
> Den [Azure IoT-enhet etablering Service SDK] [ lnk-sdks] automatiskt generera token när du ansluter till tjänsten.

## <a name="security-tokens"></a>Säkerhetstoken
Etablering av tjänst använder säkerhetstoken för att autentisera tjänster för att undvika att skicka nycklar i uppkopplat läge. Dessutom är säkerhetstoken begränsad giltighetstid och omfång. [Azure IoT-enhet etablering Service SDK] [ lnk-sdks] automatiskt generera token utan någon specialkonfiguration. Vissa scenarier behöver du skapa och använda säkerhetstoken direkt. Dessa scenarier som inkluderar direkt användning av HTTP-yta.

### <a name="security-token-structure"></a>Säkerhet token struktur

Du kan använda säkerhetstoken för att ge tid avgränsas åtkomst för specifika funktioner i IoT-enhet etablering Service-tjänster. För att få behörighet att ansluta till tjänsten etablering skicka services säkerhetstoken som signerade med en delad åtkomst eller en symmetrisk nyckel.

En token som signerats med en delad åtkomst viktiga beviljar åtkomst till alla funktioner som är associerade med principen-behörigheter för delad åtkomst. 

Säkerhetstoken har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är de förväntade värdena:

| Värde | Beskrivning |
| --- | --- |
| {signatur} |En HMAC SHA256 signatur sträng med formatet: `{URL-encoded-resourceURI} + "\n" + expiry`. **Viktiga**: nyckeln avkoda från base64 och används som nyckel för att utföra HMAC SHA256-beräkningen.|
| {utgången} |UTF8 strängar för antal sekunder sedan epok 00:00:00 UTC på 1 januari 1970. |
| {URL-kodade-resourceURI} | Lägre fall URL-kodning av gemen resurs-URI. URI-prefix (av segment) slutpunkter som kan användas med denna token som börjar med värdnamnet för IoT-enhet Etableringstjänsten (ingen protocol). Till exempel `mydps.azure-devices-provisioning.net`. |
| {policyName} |Namnet på den princip för delad åtkomst som den här variabeln refererar. |

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

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
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
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Eftersom tokens giltighet verifieras IoT-enhet etablering på datorer i måste drift på klockan på den dator som genererar token vara minimal.


### <a name="use-security-tokens-from-service-components"></a>Använd säkerhetstoken från tjänstkomponenter

Tjänstens komponenter kan bara generera säkerhetstoken med hjälp av principer för delad åtkomst som beviljar behörighet som tidigare förklarats.

Här följer servicefunktioner exponerad på slutpunkter:

| Slutpunkt | Funktioner |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Innehåller enheten registreringsåtgärder med enheten Etableringstjänsten. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Innehåller åtgärder för att hantera registrering enhetsgrupper. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Innehåller åtgärder för att hämta och hantera status för enheten registreringar. |


Exempelvis en tjänst som skapats med en skapats i förväg delad åtkomstprincip som heter **enrollmentread** skulle skapa en token med följande parametrar:

* resurs-URI: `{mydps}.azure-devices-provisioning.net`,
* nyckel för signeringscertifikatet: en av nycklarna för den `enrollmentread` principen
* Principnamn: `enrollmentread`,
* helst upphör att gälla.

![Skapa en princip för delad åtkomst för din DP-instans på portalen][img-add-shared-access-policy]

```nodejs
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Resultatet som skulle ge åtkomst för att läsa alla registreringsposter, är:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Referensinformation:

Följande referensavsnitt ge mer information om hur du styr åtkomst till din IoT-enhet etablering av tjänst.

## <a name="device-provisioning-service-permissions"></a>Enheten Etableringstjänsten behörigheter

I följande tabell visas de behörigheter som du kan använda för att styra åtkomsten till din IoT-enhet etablering av tjänst.

| Behörighet | Anteckningar |
| --- | --- |
| **ServiceConfig** |Ger åtkomst till ändra konfigurationen för tjänsten. <br/>Den här behörigheten används av backend-molntjänster. |
| **EnrollmentRead** |Ger läsbehörighet till enhetsregistrering och registrering av grupper. <br/>Den här behörigheten används av backend-molntjänster. |
| **EnrollmentWrite** |Bevilja skrivbehörighet till enhetsregistreringar och registrering av grupper. <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistrationStatusRead** |Ger skrivskyddad åtkomst till enheten registreringsstatus. <br/>Den här behörigheten används av backend-molntjänster. |
| **RegistrationStatusWrite**  |Ger ta bort åtkomst till enheten registreringsstatus. <br/>Den här behörigheten används av backend-molntjänster. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
