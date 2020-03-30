---
title: Definiera en teknisk OAuth1-profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk OAuth 1.0-profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7f734844859d44e66bddbc2ddd999659e52f9668
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184085"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk OAuth1-profil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för [OAuth 1.0-protokollidentitetsprovidern.](https://tools.ietf.org/html/rfc5849) I den här artikeln beskrivs detaljerna för en teknisk profil för att interagera med en anspråksprovider som stöder det här standardiserade protokollet. Med en teknisk profil för OAuth1 kan du federera med en OAuth1-baserad identitetsleverantör, till exempel Twitter. Federerning med identitetsleverantören tillåter användare att logga in med sina befintliga sociala identiteter eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `OAuth1`anges till . Till exempel är `OAuth1`protokollet för den tekniska profilen **Twitter-OAUTH1** .

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Ingående anspråk

**Elementen InputClaims** och **InputClaimsTransformations** är tomma eller frånvarande.

## <a name="output-claims"></a>Utgående fordringar

**Elementet OutputClaims** innehåller en lista över anspråk som returneras av OAuth1-identitetsprovidern. Du kan behöva mappa namnet på anspråket som definieras i din policy till det namn som definierats i identitetsleverantören. Du kan också inkludera anspråk som inte returneras av identitetsprovidern så länge du anger **attributet DefaultValue.**

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

I följande exempel visas de anspråk som returneras av Twitter-identitetsleverantören:

- Det **user_id** anspråk som mappas till **emittentenUserId-anspråk.**
- Det **screen_name** anspråk som mappas till **anspråket displayName.**
- **E-postanspråket** utan namnmappning.

Den tekniska profilen returnerar också anspråk som inte returneras av identitetsleverantören:

- **Identitetprovider-anspråk** som innehåller namnet på identitetsleverantören.
- **autentiseringKällanspråket** med `socialIdpAuthentication`standardvärdet .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Programidentifieraren för identitetsprovidern. |
| ProviderName | Inga | Namnet på identitetsprovidern. |
| request_token_endpoint | Ja | URL:en för slutpunkten för begärantoken enligt RFC 5849. |
| authorization_endpoint | Ja | URL:en för auktoriseringsslutpunkten enligt RFC 5849. |
| access_token_endpoint | Ja | URL:en för tokenslutpunkten enligt RFC 5849. |
| ClaimsEndpoint | Inga | Url:en för slutpunkten för användarinformation. |
| ClaimsResponseFormat | Inga | Skadesvarsformatet.|

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemligheten för identitetsproviderprogrammet.   |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar omdirigeringsadressen för din identitetsleverantör anger du `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Se till att ersätta **klienten** med ditt klientnamn (till exempel contosob2c.onmicrosoft.com) och **policyId** med identifieraren för din princip (till exempel b2c_1a_policy). Omdirigerings-URI:n måste vara i alla gemener. Lägg till en omdirigerings-URL för alla principer som använder identitetsproviderns inloggning.

Om du använder **b2clogin.com** domänen i stället för **login.microsoftonline.com** Se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägga till Twitter som OAuth1-identitetsleverantör med hjälp av anpassade principer](identity-provider-twitter-custom.md)













