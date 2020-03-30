---
title: Definiera en teknisk OAuth2-profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk OAuth2-profil i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 86ec7a5745a58546faf6f0ff15d6dc5f452baa88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184051"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk OAuth2-profil i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för OAuth2-protokollidentitetsprovidern. OAuth2 är det primära protokollet för auktorisering och delegerad autentisering. Mer information finns i [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749). Med en teknisk OAuth2-profil kan du federera med en OAuth2-baserad identitetsleverantör, till exempel Facebook. Federerning med en identitetsleverantör tillåter användare att logga in med sina befintliga sociala identiteter eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `OAuth2`anges till . Protokollet för den tekniska profilen **Facebook-OAUTH** är `OAuth2`till exempel:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>Ingående anspråk

**Elementen InputClaims** och **InputClaimsTransformations** krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitetsleverantör. I följande exempel läggs **parametern domain_hint** frågesträng `contoso.com` med värdet för till auktoriseringsbegäran.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående fordringar

**Elementet OutputClaims** innehåller en lista över anspråk som returneras av OAuth2-identitetsprovidern. Du kan behöva mappa namnet på anspråket som definieras i din policy till det namn som definierats i identitetsleverantören. Du kan också inkludera anspråk som inte returneras av identitetsprovidern så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

I följande exempel visas de anspråk som returneras av Facebook-identitetsleverantören:

- Anspråket **first_name** mappas till **påståendet givenName.**
- Det **last_name** påståendet mappas till **efternamnet.**
- **DisplayName-anspråket** utan namnmappning.
- **E-postanspråket** utan namnmappning.

Den tekniska profilen returnerar också anspråk som inte returneras av identitetsleverantören:

- **Identitetprovider-anspråk** som innehåller namnet på identitetsleverantören.
- **AutentiseringKällanspråk** med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Programidentifieraren för identitetsprovidern. |
| IdTokenAudience | Inga | Publiken i id_token. Om det anges kontrollerar Azure AD B2C om token finns i ett anspråk som returneras av identitetsprovidern och är lika med den angivna. |
| authorization_endpoint | Ja | URL:en för auktoriseringsslutpunkten enligt RFC 6749. |
| AccessTokenEndpoint | Ja | URL:en för tokenslutpunkten enligt RFC 6749. |
| ClaimsEndpoint | Ja | Url:en för slutpunkten för användarinformation enligt RFC 6749. |
| AccessTokenResponseFormat | Inga | Formatet för slutpunktsanropet för åtkomsttoken. Facebook kräver till exempel en HTTP GET-metod, men åtkomsttokensvaret är i JSON-format. |
| YtterligareRequestQueryParameters | Inga | Ytterligare frågeparametrar för begäran. Du kanske till exempel vill skicka ytterligare parametrar till din identitetsleverantör. Du kan inkludera flera parametrar med kommae avgränsare. |
| ClaimsEndpointAccessTokenName | Inga | Namnet på parametern access token query string. Vissa identitetsleverantörers anspråksslutpunkter stöder GET HTTP-begäran. I det här fallet skickas innehavartoken med hjälp av en frågesträngparameter i stället för auktoriseringshuvudet. |
| ClaimsEndpointFormatName | Inga | Namnet på parametern formatfrågesträng. Du kan till exempel ange `format` namnet som i `https://api.linkedin.com/v1/people/~?format=json`den här LinkedIn-anspråksslutpunkten . |
| ClaimsEndpointFormat | Inga | Värdet för formatfrågesträngparametern. Du kan till exempel ange `json` värdet som i `https://api.linkedin.com/v1/people/~?format=json`den här LinkedIn-anspråksslutpunkten . |
| ProviderName | Inga | Namnet på identitetsprovidern. |
| response_mode | Inga | Den metod som identitetsprovidern använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga `query`värden: `form_post` , (standard) eller `fragment`. |
| omfång | Inga | Omfattningen av begäran som definieras enligt OAuth2 identitetsprovider specifikation. Till `openid`exempel `profile`, `email`och . |
| HttpBinding (Påminn) | Inga | Den förväntade HTTP-bindningen till åtkomsttoken och anspråkstokenslutpunkter. Möjliga `GET` värden: `POST`eller .  |
| SvarskodParamNamn | Inga | Namnet på parametern som innehåller felmeddelandet returnerade via HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Inga | Innehåller de extra parametrar som kan returneras i svaret från **AccessTokenEndpoint** av vissa identitetsleverantörer. Svaret från **AccessTokenEndpoint** innehåller till exempel en `openid`extra parameter, till exempel , som är en obligatorisk parameter förutom access_token i en frågasträng för begäran om **begäran om begäran om anspråksbegäran.** Flera parameternamn ska skickas och avgränsas med kommatecknet '', avgränsare. |
| ExtraParamsInClaimsEndpointRequest | Inga | Innehåller de extra parametrar som kan returneras i begäran om **ClaimsEndpoint** av vissa identitetsleverantörer. Flera parameternamn ska skickas och avgränsas med kommatecknet '', avgränsare. |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |
| LösJsonPathsInJsonTokens  | Inga | Anger om den tekniska profilen löser JSON-sökvägar. Möjliga värden: `true` `false` , eller (standard). Använd dessa metadata för att läsa data från ett kapslat JSON-element. I ett [OutputClaim](technicalprofiles.md#outputclaims) `PartnerClaimType` anger du det JSON-banelement som du vill mata ut. Till exempel: `firstName.localized` `data.0.to.0.email`eller .|

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemligheten för identitetsproviderprogrammet. Den kryptografiska nyckeln krävs endast om **response_types** metadata `code`är inställd på . I det här fallet gör Azure AD B2C ett annat anrop för att byta auktoriseringskoden för en åtkomsttoken. Om metadata är `id_token`inställd på kan du utelämna den kryptografiska nyckeln. |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar omdirigeringsadressen för din identitetsleverantör anger du `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Se till att ersätta **klienten** med din klients namn (till exempel contosob2c.onmicrosoft.com) och **policyId** med identifieraren för din princip (till exempel b2c_1a_policy). Omdirigerings-URI:n måste vara i alla gemener.

Om du använder **b2clogin.com** domänen i stället för **login.microsoftonline.com** Se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägga till Google+ som en OAuth2-identitetsleverantör med hjälp av anpassade principer](identity-provider-google-custom.md)













