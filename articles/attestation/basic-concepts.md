---
title: Grundläggande koncept för Azure-attestering
description: Grundläggande begrepp för Azure-attestering.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 95f59b73682e461a350410b38e3a021226cd7db6
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748696"
---
# <a name="basic-concepts"></a>Grundläggande begrepp

Nedan visas några grundläggande begrepp som rör Microsoft Azure attestering.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON Web token](https://jwt.io/) (JWT) är en öppen standard [RFC7519](https://tools.ietf.org/html/rfc7519) -metod för att på ett säkert sätt överföra information mellan parter som ett JavaScript Object Notation-objekt (JSON). Den här informationen kan verifieras och vara betrodd eftersom den har signerats digitalt. JWTs kan signeras med ett hemligt eller ett offentligt/privat nyckel par.

## <a name="json-web-key-jwk"></a>JSON-webbnyckel (JWK)

[JSON-webbnyckeln](https://tools.ietf.org/html/rfc7517) (JWK) är en JSON-datastruktur som representerar en kryptografisk nyckel. Den här specifikationen definierar också en JWK set JSON-datastruktur som representerar en uppsättning JWKs.

## <a name="attestation-provider"></a>Attesterings leverantör

Attesterings leverantören tillhör Azure Resource Provider med namnet Microsoft. attestering. Resurs leverantören är en tjänst slut punkt som tillhandahåller REST-avtal för Azure-attestering och distribueras med hjälp av [Azure Resource Manager](../azure-resource-manager/management/overview.md). Varje attesterings leverantör följer en speciell princip som kan upptäckas. 

Attesterings leverantörer skapas med en standard princip för varje TEE-typ (Observera att VBS enklaven inte har någon standard princip). Se [exempel på en attesterings princip](policy-examples.md) för mer information om standard principen för SGX.

### <a name="regional-default-provider"></a>Regional standard leverantör

Azure-attestering tillhandahåller en standardprovider i varje region. Kunder kan välja att använda standardprovidern för attestering eller skapa egna leverantörer med anpassade principer. Standard leverantörerna är tillgängliga för alla Azure AD-användare och principen som är associerad med en standardprovider kan inte ändras.

| Region | Attesterings-URI | 
|--|--|
| Storbritannien, södra | `https://shareduks.uks.attest.azure.net` | 
| USA, östra 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| Central US | `https://sharedcus.cus.attest.azure.net` | 
| East US| `https://sharedeus.eus.attest.azure.net` | 
| Kanada, centrala | `https://sharedcac.cac.attest.azure.net` | 

## <a name="attestation-request"></a>Begäran om attestering

Begäran om attestering är ett serialiserat JSON-objekt som skickas av klient programmet till attesterings leverantören. Objektet Request för SGX-enklaven har två egenskaper: 
- "QUOTE" – värdet för egenskapen "QUOTE" är en sträng som innehåller en Base64URL-kodad representation av attesterings offerten
- "EnclaveHeldData" – värdet för egenskapen "EnclaveHeldData" är en sträng som innehåller en Base64URL-kodad representation av enklaven hålls data.

Azure-attesteringen validerar det tillhandahållna "QUOTE" från TEE och ser till att SHA256-hashen för de tillhandahållna enklaven hålls data uttrycks i de första 32 byten i fältet reportData i offerten. 

## <a name="attestation-policy"></a>Attesteringsprincip

Attesterings policyn används för att bearbeta attesterings beviset och kan konfigureras av kunder. Kärnan i Azure-attestering är en princip motor som bearbetar anspråk som utgör beviset. Principer används för att avgöra om Azure-attesteringen ska utfärda en attesterings-token baserat på bevis (eller inte) och därmed påteckna attesteringen (eller inte). Därför leder det till att det inte finns någon JWT-token som utfärdas för att Miss lyckas med att skicka alla principer.

Om standard principen för TEE i attesterings leverantören inte uppfyller behoven kan kunderna skapa anpassade principer i någon av de regioner som stöds av Azure-attestering. Princip hantering är en viktig funktion som kunder har fått av Azure-attestering. Principer är TEE och kan användas för att identifiera enclaves eller lägga till anspråk till utdataporten eller modifiera anspråk i en utmatnings-token. 

Se [exempel på en attesterings princip](policy-examples.md) för standard princip innehåll och exempel.

## <a name="benefits-of-policy-signing"></a>Fördelar med princip signering

En attesterings princip är vad som i slut änden bestämmer om en attesterings-token utfärdas av Azure-attestering. Principen avgör också vilka anspråk som ska genereras i attesterings-token. Det är därför mycket viktigt att principen som utvärderas av tjänsten faktiskt är den princip som skrivits av administratören och inte har manipulerats eller ändrats av externa entiteter. 

Förtroende modellen definierar auktoriserings modellen för attesterings leverantören för att definiera och uppdatera principen.  Två modeller stöds – en baserad på Azure AD-auktorisering och en baserad på innehav av kund hanterade kryptografiska nycklar (kallas isolerad modell).  Isolerad modell gör det möjligt för Azure-attestering att se till att den policy som har skickats inte har ändrats.

I isolerad modell skapar administratören en attesterings-provider som anger en uppsättning betrodda signerings-X. 509-certifikat i en fil. Administratören kan sedan lägga till en signerad princip till attesterings leverantören. När attesteringen bearbetas verifierar Azure-attestering signaturen för principen med hjälp av den offentliga nyckeln som representeras av antingen "JwK" eller "X5C"-parametern i rubriken.  Azure-attesteringen kommer också att kontrol lera om den offentliga nyckeln i begär ande huvudet finns i listan över betrodda signerings certifikat som är associerade med attesteringsservern. På så sätt kan den förlitande parten (Azure-attestering) lita på en princip som signerats med de X. 509-certifikat som den känner till. 

Se [exempel på princip signerings certifikat](policy-signer-examples.md) för exempel.

## <a name="attestation-token"></a>Attesterings-token

Svar från Azure-attestering är en JSON-sträng vars värde innehåller JWT. Azure-attesteringen paketerar anspråk och genererar ett signerat JWT. Signerings åtgärden utförs med ett självsignerat certifikat med mottagar namn som matchar AttestUri-elementet för attesteringsservern.

Get OpenID metadata-API: et returnerar ett OpenID konfigurations svar som anges i [OpenID Connect Discovery-protokollet](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). API: et hämtar metadata om signerings certifikaten som används av Azure-attestering.

Exempel på JWT som genereras för en SGX-enklaven:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Anspråk som "EXP", "IAT", "ISS", "NBF" definieras av [JWT RFC](https://tools.ietf.org/html/rfc7517) och återstående genereras av Azure-attestering. Mer information finns i [anspråk som utfärdats av Azure-attestering](claim-sets.md) .

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Azure-attesteringen behåller sina data i Azure Storage för att skydda kund information. Azure Storage tillhandahåller kryptering av data i vila när de är skrivna i Data Center och dekrypterar dem för att kunderna ska kunna komma åt dem. Krypteringen sker med hjälp av en Microsoft-hanterad krypterings nyckel. 

Förutom att skydda data i Azure Storage utnyttjar Azure attestering också Azure Disk Encryption (ADE) för att kryptera virtuella datorer i tjänsten. För Azure-attestering som körs i en enklaven i Azures miljö för konfidentiella data behandling stöds inte ADE-tillägget för närvarande. I sådana fall, för att förhindra att data lagras i minnet, är växlings filen inaktive rad. 

Inga kund uppgifter sparas på de lokala Azure attesterings instansens lokala hård diskar.


## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)
