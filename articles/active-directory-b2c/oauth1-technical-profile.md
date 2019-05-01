---
title: Definiera en OAuth1 tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en OAuth1 tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7b3d579e9d4ceb92ee961778ba6083292461c144
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699833"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OAuth1 tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för den [OAuth 1.0-protokollet](https://tools.ietf.org/html/rfc5849) identitetsprovider. Den här artikeln beskriver specifika för den tekniska profilen för att interagera med en anspråksprovider som stöder detta standardiserade protokoll. Med en teknisk OAuth1-profil kan du federera med en OAuth1 baserat identitetsprovider, till exempel Twitter. Federera med identitetsprovidern tillåter användare att logga in med sina befintliga sociala eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `OAuth1`. Till exempel protokollet för den **Twitter-OAUTH1** tekniska profilen är `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Inkommande anspråk

Den **InputClaims** och **InputClaimsTransformations** element är tom eller saknas.

## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** elementet innehåller en lista över anspråk som returneras av OAuth1 identitetsleverantören. Du kan behöva matchar namnet på anspråk som definierats i din princip att namnet som definierats i identitetsprovidern. Du kan även inkludera anspråk som inte returneras av identitetsleverantören. så länge som du ställer in den **DefaultValue** attribut.

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

I följande exempel visar de anspråk som returneras av identitetsprovider Twitter:

- Den **user_id** anspråk som är mappad till den **issuerUserId** anspråk.
- Den **screen_name** anspråk som är mappad till den **displayName** anspråk.
- Den **e-post** anspråk utan mappning av användarnamn.

Den tekniska profilen returnerar också anspråk som inte returnerade poskytovatelem identity: 

- Den **identityProvider** anspråk som innehåller namnet på identitetsprovidern.
- Den **authenticationSource** anspråk med ett standardvärde på `socialIdpAuthentication`.

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
| client_id | Ja | Program-ID för identitetsprovidern. |
| ProviderName | Nej | Namnet på identitetsprovidern. |
| request_token_endpoint | Ja | URL för begäran till tokenslutpunkten enligt RFC 5849. |
| authorization_endpoint | Ja | URL för auktoriseringsslutpunkt enligt RFC 5849. |
| access_token_endpoint | Ja | URL för tokenslutpunkt enligt RFC 5849. |
| ClaimsEndpoint | Nej | URL till slutpunkten för användaren information. | 
| ClaimsResponseFormat | Nej | Anspråk svarsformat.|

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Den **CryptographicKeys** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemlighet för identitetsprogram för providern.   | 

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar omdirigerings-URL: en för din identitetsprovider, ange `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Ersätt **klient** med ditt klientnamn (till exempel contosob2c.onmicrosoft.com) och **policyId** med ID för principen (till exempel b2c_1a_policy). Omdirigerings-URI måste vara i alla gemener. Lägg till en omdirigerings-URL för alla principer som använder providern inloggning för identitet. 

Om du använder den **b2clogin.com** domänen i stället för **login.microsoftonline.com** se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägga till Twitter som identitetsprovider OAuth1 med anpassade principer](active-directory-b2c-custom-setup-twitter-idp.md)













