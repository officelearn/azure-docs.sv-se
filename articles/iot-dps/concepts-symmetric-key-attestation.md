---
title: Azure IoT Hub Device Provisioning-tjänsten – symmetriska nyckelattestering
description: Den här artikeln innehåller en översikt över symmetriska nyckelattestering med hjälp av IoT Device Provisioning-tjänsten.
author: wesmc7777
ms.author: wesmc
ms.date: 08/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2f6e1e1a27e32e567cf0eaa8ff7a99046ed81bbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746255"
---
# <a name="symmetric-key-attestation"></a>Symmetrisk nyckelattestering

Den här artikeln beskriver hur identity attestering när du använder symmetriska nycklar med Device Provisioning-tjänsten. 

Symmetrisk nyckelattestering är en enkel metod för autentisering av en enhet med en instans av Device Provisioning-tjänsten. Den här metoden för attestering representerar en ”Hello world”-upplevelse för utvecklare som är nytt för enhetsetablering eller inte har stränga säkerhetskrav. Enheter attestering med hjälp av en [TPM](concepts-tpm-attestation.md) eller en [X.509-certifikat](concepts-security.md#x509-certificates) är mer säker och bör användas för strängare säkerhetskrav.

Symmetrisk nyckel registreringar tillhandahåller också ett bra sätt för äldre enheter, med funktioner för begränsad säkerhet, ska kunna starta till molnet via Azure IoT. Läs mer på symmetriska nyckelattestering med äldre enheter, [använda symmetriska nycklar med äldre enheter](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Skapa en symmetrisk nyckel

Som standard Device Provisioning-tjänsten skapar nya symmetriska nycklar med en standardlängd på 32 byte när nya registreringar sparas med den **Autogenerera nycklar** alternativ är aktiverat.

![Automatiskt generera symmetriska nycklar](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Du kan också ange din egen symmetriska nycklar för registreringar genom att inaktivera det här alternativet. När du anger din egen symmetriska nycklar måste dina nycklar ha en nyckellängd mellan 16 och 64 byte. Symmetriska nycklar måste också anges i ett giltigt Base64-format.



## <a name="detailed-attestation-process"></a>Detaljerad attesteringsprocessen

Symmetrisk nyckelattestering med Device Provisioning-tjänsten utförs med hjälp av samma [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure) stöds av IoT-hubbar att identifiera enheter. De här säkerhetstoken är [token för signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md). 

SAS-token har en hashade *signatur* som skapas med hjälp av den symmetriska nyckeln. Signaturen återskapas i Device Provisioning-tjänsten att verifiera om en säkerhetstoken som visas under Attesteringen är autentisk eller inte.

SAS-token har följande format:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här följer komponenterna i varje token:

| Värde | Beskrivning |
| --- | --- |
| {signature} |En HMAC-SHA256 signatur-sträng. För enskilda registreringar skapas signaturen med hjälp av den symmetriska nyckeln (primära eller sekundära) för att utföra hash-värdet. För registreringsgrupper för en nyckel som härleds från gruppnyckel registrering att utföra hash-värdet. Hash-värdet utförs på ett meddelande i formatet: `URL-encoded-resourceURI + "\n" + expiry`. **Viktiga**: Nyckeln måste avkodas från base64 innan som används för att utföra HMAC-SHA256-beräkningen. Signaturen resultatet måste dessutom vara URL-kodade. |
| {resourceURI} |URI för profilslutpunkt som kan användas med denna token från och med scope-ID för Device Provisioning Service-instans. Till exempel, `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |UTF8-strängar för antal sekunder sedan epoch 00:00:00 UTC på 1 januari 1970. |
| {URL-encoded-resourceURI} |Lägre fall URL-kodning av gemen resurs-URI |
| {policyName} |Namnet på den princip för delad åtkomst som denna token refererar. Namnet på principen som används vid etableringen av med symmetriska nyckelattestering är **registrering**. |

När en enhet bestyrkande med en enskild registrering, använder enheten den symmetriska nyckel som definierats i posten för enskild registrering för att skapa hashade signaturen för SAS-token.

Kodexempel som skapar en SAS-token, se [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Skapar säkerhetstoken för symmetrisk nyckelattestering stöds av Azure IoT C SDK. Ett exempel med Azure IoT C SDK intyga med en enskild registrering finns i [etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Gruppregistreringar

De symmetriska nycklarna för gruppregistreringar används inte direkt av enheter vid etablering. I stället gruppera enheter som tillhör en registrering med hjälp av en härledd enhetsnyckel etablera. 

Först definieras en unika registrerings-ID för varje enhet bestyrkande med en grupp för registrering. Giltiga tecken registrerings-ID är alfanumeriska gemener och bindestreck (”-”). Den här registrerings-ID ska vara något unikt som identifierar enheten. En äldre enhet kan till exempel inte stöd för många säkerhetsfunktioner. Äldre enhet får bara ha en MAC-adress eller serienummer som är tillgängliga för att identifiera enheten. I så fall kan kan en registrerings-ID bestå av MAC-adress och serienummer som liknar följande:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Det här exakta exemplet används i den [hur du etablerar äldre enheter med symmetriska nycklar](how-to-legacy-device-symm-key.md) artikeln.

När du har definierat en registrerings-ID för enheten, den symmetriska nyckeln för registreringsgruppen används för att beräkna en [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) hash för registrerings-ID för att skapa en härledd enhetsnyckel. Hashing av registrerings-ID kan utföras med följande C#-kod:

```C#
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```C#
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Resulterande enhetsnyckeln används sedan för att generera en SAS-token som ska användas för attestering. Varje enhet i en grupp för registrering krävs för att intyga med en säkerhetstoken som genereras från en unik härledda nyckel. Den symmetriska nyckeln grupp för registrering kan inte användas direkt för attestering.

#### <a name="installation-of-the-derived-device-key"></a>Installation av härledda enhetsnyckeln

Vi rekommenderar enhetsnycklar härleds och installerats i fabriken. Den här metoden garanterar gruppnyckeln ingår aldrig någon programvara som distribuerats till enheten. När enheten har tilldelats en MAC-adress eller serienummer nyckeln härrör och införs i enheten men tillverkaren väljer att lagra den.

Överväg följande diagram som visar en tabell med enhetsnycklar som genererats i en fabrik genom hashning varje enhet registrerings-ID med registrering gruppnyckel (**K**). 

![Enhetsnycklar som tilldelats från en fabrik](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identiteten för varje enhet representeras av registrerings-ID och härledda enhetsnyckel som är installerad på fabriken. Enhetsnyckeln aldrig har kopierats till en annan plats och gruppnyckeln lagras aldrig på en enhet.

Om enhetsnycklar inte är installerade i factory, en [maskinvarusäkerhetsmodul HSM](concepts-security.md#hardware-security-module) bör användas för att på ett säkert sätt lagra enhetens identitet.

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för symmetrisk nyckel attestering, finns i följande artiklar om du vill veta mer:

* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* [Lär dig mer om begreppen i Automatisk etablering](./concepts-auto-provisioning.md)
* [Kom igång med hjälp av Automatisk etablering](./quick-setup-auto-provision.md) 
