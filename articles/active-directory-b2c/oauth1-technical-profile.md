---
title: Definiera en OAuth1 teknisk profil i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en OAuth1 teknisk profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 97fa5757f8b77e29545f6d6f6b885334c7b526f1
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063996"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OAuth1 teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för [OAuth 1,0-protokollets](https://tools.ietf.org/html/rfc5849) identitets leverantör. I den här artikeln beskrivs de olika kraven för en teknisk profil för att interagera med en anspråks leverantör som stöder detta standardiserade protokoll. Med en OAuth1 teknisk profil kan du federera med en OAuth1-baserad identitets leverantör, t. ex. Twitter. Genom att federera med identitets leverantören kan användarna logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="protocol"></a>Protocol

Namnattributet **för** **protokoll** elementet måste anges till `OAuth1`. Till exempel är `OAuth1`protokollet för den tekniska profilen **Twitter-OAUTH1** .

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

**InputClaims** -och **InputClaimsTransformations** -elementen är tomma eller saknas.

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av OAuth1 Identity Provider. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också inkludera anspråk som inte returneras av identitets leverantören så länge du ställer in attributet **DefaultValue** .

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Twitter-identitets leverantören:

- **User_id** -anspråket som är mappat till **issuerUserId** -anspråket.
- **Screen_name** -anspråket som mappas till **visnings kravet DisplayName** .
- **E-** postanspråk utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet `socialIdpAuthentication`.

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

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program identifieraren för identitets leverantören. |
| ProviderName | Nej | Namnet på identitets leverantören. |
| request_token_endpoint | Ja | URL: en för token för begäran som enligt RFC 5849. |
| authorization_endpoint | Ja | URL: en för Authorization-slutpunkten enligt RFC 5849. |
| access_token_endpoint | Ja | URL: en för token-slutpunkten enligt RFC 5849. |
| ClaimsEndpoint | Nej | URL till användar informations slut punkten. |
| ClaimsResponseFormat | Nej | Format för anspråk svar.|

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klient hemligheten för Identity Provider-programmet.   |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar omdirigerings-URL: en för din `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`identitetsprovider anger du. Se till att ersätta **klient organisationen** med klient namnet (till exempel contosob2c.onmicrosoft.com) och **policyId** med identifieraren för principen (till exempel b2c_1a_policy). Omdirigerings-URI: n måste vara i gemener. Lägg till en omdirigerings-URL för alla principer som använder identitets leverantörs inloggningen.

Om du använder **b2clogin.com** -domänen i stället för **login.microsoftonline.com** ska du se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägg till Twitter som en OAuth1 identitets leverantör genom att använda anpassade principer](active-directory-b2c-custom-setup-twitter-idp.md)













