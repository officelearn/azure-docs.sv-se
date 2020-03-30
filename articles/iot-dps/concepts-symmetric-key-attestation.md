---
title: Etablerande tjänst för Azure IoT Hub Device - Symmetrisk nyckelbevaring
description: Den här artikeln innehåller en begreppsmässig översikt över symmetrisk nyckeltysst med hjälp av DPS (IoT Device Provisioning Service).
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 0e3d343c0a68dd527e4e8e8d23e5b3843a216a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271517"
---
# <a name="symmetric-key-attestation"></a>Symmetrisk nyckelattestering

I den här artikeln beskrivs identitetsintygsprocessen när du använder symmetriska nycklar med enhetsetableringstjänsten. 

Symmetrisk nyckeltysstation är en enkel metod för att autentisera en enhet med en enhetsetableringstjänstinstans. Den här attestation-metoden representerar en "Hello world"-upplevelse för utvecklare som är nya för enhetsetablering eller inte har strikta säkerhetskrav. Enhetsintyg med hjälp av ett [TPM-](concepts-tpm-attestation.md) eller [X.509-certifikat](concepts-security.md#x509-certificates) är säkrare och bör användas för strängare säkerhetskrav.

Symmetriska nyckelregistreringar är också ett utmärkt sätt för äldre enheter, med begränsad säkerhetsfunktionalitet, att bootstrap till molnet via Azure IoT. Mer information om symmetrisk nyckeltysstation med äldre enheter finns i [Så här använder du symmetriska nycklar med äldre enheter](how-to-legacy-device-symm-key.md).


## <a name="symmetric-key-creation"></a>Symmetrisk nyckelskapande

Som standard skapar enhetsetableringstjänsten nya symmetriska nycklar med en standardlängd på 32 byte när nya registreringar sparas med alternativet **Generera automatiskt nycklar** aktiverat.

![Generera symmetriska nycklar automatiskt](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

Du kan också ange dina egna symmetriska nycklar för registreringar genom att inaktivera det här alternativet. När du anger dina egna symmetriska nycklar måste nycklarna ha en nyckellängd mellan 16 byte och 64 byte. Dessutom måste symmetriska nycklar anges i giltigt Base64-format.



## <a name="detailed-attestation-process"></a>Detaljerad attestation process

Symmetrisk nyckeltysstation med enhetsetableringstjänsten utförs med samma [säkerhetstoken som](../iot-hub/iot-hub-devguide-security.md#security-token-structure) stöds av IoT-hubbar för att identifiera enheter. Dessa säkerhetstoken är [SAS-token (Shared Access Signature).](../service-bus-messaging/service-bus-sas.md) 

SAS-token har en *hash-signatur* som skapas med hjälp av den symmetriska nyckeln. Signaturen återskapas av enhetsetableringstjänsten för att kontrollera om en säkerhetstoken som presenteras under attesteringen är äkta eller inte.

SAS-token har följande formulär:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Här är komponenterna i varje token:

| Värde | Beskrivning |
| --- | --- |
| {signatur} |En HMAC-SHA256 signatursträng. För enskilda registreringar skapas den här signaturen med hjälp av den symmetriska nyckeln (primär eller sekundär) för att utföra hashen. För registreringsgrupper används en nyckel som härleds från registreringsgruppnyckeln för att utföra hash-värdet. Hash utförs på ett meddelande i `URL-encoded-resourceURI + "\n" + expiry`formuläret: . **Viktigt**: Nyckeln måste avkodas från base64 innan den används för att utföra HMAC-SHA256-beräkningen. Signaturresultatet måste också url-kodas. |
| {resourceURI} |URI för registreringsslutpunkten som kan nås med den här token, med början i scope-ID för instansen Enhetsetableringstjänst. Till exempel, `{Scope ID}/registrations/{Registration ID}` |
| {utgångsdatum} |UTF8 strängar för antal sekunder sedan epoken 00:00:00 UTC den 1 januari 1970. |
| {URL-kodad-resourceURI} |Gemener URL-kodning av den gemena resursen URI |
| {policyName} |Namnet på den delade åtkomstprincip som den här token refererar till. Det principnamn som används vid etablering med symmetrisk nyckeltysststation är **registrering**. |

När en enhet intygar med en enskild registrering använder enheten den symmetriska nyckeln som definierats i den enskilda registreringsposten för att skapa den hashade signaturen för SAS-token.

Kodexempel som skapar en SAS-token finns i [Säkerhetstokens](../iot-hub/iot-hub-devguide-security.md#security-token-structure).

Att skapa säkerhetstoken för symmetrisk nyckeltysststation stöds av Azure IoT C SDK. Ett exempel med Azure IoT C SDK för att intyga med en enskild registrering finns i [Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md).


## <a name="group-enrollments"></a>Gruppregistreringar

De symmetriska nycklarna för gruppregistreringar används inte direkt av enheter vid etablering. I stället enheter som tillhör en registreringsgruppsetablering med hjälp av en härledd enhetsnyckel. 

Först definieras ett unikt registrerings-ID för varje enhet som intygar med en registreringsgrupp. Giltiga tecken för registrerings-ID är gemener alfanumeriska och streck ('-'). Det här registrerings-ID:t ska vara något unikt som identifierar enheten. En äldre enhet kanske till exempel inte stöder många säkerhetsfunktioner. Den äldre enheten kanske bara har en MAC-adress eller serienummer tillgängligt för att identifiera den enheten på ett unikt sätt. I så fall kan ett registrerings-ID bestå av MAC-adressen och serienumret som liknar följande:

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Det här exakta exemplet används i artikeln [Hur du etablerar äldre enheter med hjälp av symmetriska nycklar.](how-to-legacy-device-symm-key.md)

När ett registrerings-ID har definierats för enheten används den symmetriska nyckeln för registreringsgruppen för att beräkna en [HMAC-SHA256-hash](https://wikipedia.org/wiki/HMAC) av registrerings-ID för att skapa en härledd enhetsnyckel. Hashing av registrerings-ID kan utföras med följande C# kod:

```csharp
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

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

Den resulterande enhetsnyckeln används sedan för att generera en SAS-token som ska användas för attestering. Varje enhet i en registreringsgrupp måste intygas med hjälp av en säkerhetstoken som genereras från en unik härledd nyckel. Den symmetriska nyckeln för registreringsgruppen kan inte användas direkt för attestering.

#### <a name="installation-of-the-derived-device-key"></a>Installation av den härledda enhetsnyckeln

Helst enhetsnycklarna härleds och installeras i fabriken. Den här metoden garanterar att gruppnyckeln aldrig ingår i någon programvara som distribueras till enheten. När enheten tilldelas en MAC-adress eller serienummer kan nyckeln härledas och injiceras i enheten, men tillverkaren väljer att lagra den.

Tänk på följande diagram som visar en tabell över enhetsnycklar som genereras i en fabrik genom att hashing varje enhetsregistrerings-ID med gruppregistreringsnyckeln (**K**). 

![Enhetsnycklar som tilldelats från en fabrik](./media/concepts-symmetric-key-attestation/key-diversification.png)

Identiteten på varje enhet representeras av registrerings-ID och härledd enhetsnyckel som är installerad på fabriken. Enhetsnyckeln kopieras aldrig till en annan plats och gruppnyckeln lagras aldrig på en enhet.

Om enhetsnycklarna inte är installerade i fabriken bör en [maskinvarusäkerhetsmodul HSM](concepts-security.md#hardware-security-module) användas för att lagra enhetens identitet på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för Symmetric Key attestation, kolla in följande artiklar för att lära dig mer:

* [Snabbstart: Etablera en simulerad enhet med symmetriska nycklar](quick-create-simulated-device-symm-key.md)
* [Lär dig mer om begreppen i automatisk etablering](./concepts-auto-provisioning.md)
* [Komma igång med automatisk etablering](./quick-setup-auto-provision.md) 
