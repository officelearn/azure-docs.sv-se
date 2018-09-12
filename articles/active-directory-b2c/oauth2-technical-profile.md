---
title: Definiera en OAuth2-tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en OAuth2-tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7f85de79b683ba7b10f5466c4a8042fc0ffdea90
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381651"
---
# <a name="define-a-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OAuth2-tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för identitetsprovidern för OAuth2-protokollet. Detta är det primära protokollet för auktorisering och delegerad autentisering. Mer information finns i den [RFC 6749 The OAuth 2.0 auktorisering Framework](http://tools.ietf.org/html/rfc6749). Med OAuth2 baserat tekniska profilen som du kan federera med en OAuth2 identitetsprovider, till exempel Facebook och Live.com, så att du användare kan logga in med sina befintliga sociala eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `OAuth2`. Till exempel protokollet för den **Facebook-OAUTH** tekniska profilen är `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Inkommande anspråk

Den **InputClaims** och **InputClaimsTransformations** element krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitetsprovider. I följande exempel läggs den **domain_hint** frågesträngparametern med värdet för `contoso.com` till begäran om godkännande.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utdataanspråk

Den **OutputClaims** elementet innehåller en lista över anspråk som returneras av OAuth2 identitetsleverantören. Du kan behöva matchar namnet på anspråk som definierats i din princip att namnet som definierats i identitetsprovidern. Du kan även inkludera anspråk som inte returneras av identitetsleverantören. så länge som den du ställer in den `DefaultValue` attribut.

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

I följande exempel visar de anspråk som returneras av identitetsleverantören. Facebook:

- Den **first_name** anspråk har mappats till den **givenName** anspråk.
- Den **efternamn** anspråk har mappats till den **efternamn** anspråk.
- Den **displayName** anspråk utan Namnmappningen...
- Den **e-post** anspråk utan mappning av användarnamn.

Den tekniska profilen returnerar också anspråk som inte returnerade poskytovatelem identity: 

- Den **identityProvider** anspråk som innehåller namnet på identitetsprovidern.
- Den **authenticationSource** anspråk med ett standardvärde på **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program-ID för identitetsprovidern. |
| IdTokenAudience | Nej | Id_token målgrupp. Om anges kontrollerar om token är i ett anspråk som returnerades av identitetsprovidern och är lika med den som angetts i Azure AD B2C. |
| authorization_endpoint | Ja | URL för auktoriseringsslutpunkt enligt RFC 6749. |
| AccessTokenEndpoint | Ja | URL för tokenslutpunkt enligt RFC 6749. |  
| ClaimsEndpoint | Ja | URL till slutpunkten användaren information enligt RFC 6749. | 
| AccessTokenResponseFormat | Nej | Formatet för åtkomst-token för slutpunkt-anropet. Till exempel Facebook kräver en HTTP GET-metod, men svaret för åtkomst-token är i JSON-format. |
| AdditionalRequestQueryParameters | Nej | Begäran om ytterligare Frågeparametrar. Du kan till exempel vill skicka ytterligare parametrar till din identitetsprovider. Du kan inkludera flera parametrar med kommatecken avgränsare. | 
| ClaimsEndpointAccessTokenName | Nej | Namnet på åtkomst-token frågesträngparametern. Vissa identitetsleverantörer anspråk slutpunkterna stöder hämta HTTP-begäran. I så fall skickas ägartoken med hjälp av en frågesträngsparameter i stället för auktoriseringsrubriken. |
| ClaimsEndpointFormatName | Nej | Namnet på formatet för frågesträngparametern. Du kan till exempel ange namnet som `format` anspråk slutpunkt i den här LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Nej | Värdet för frågesträngparametern för format. Du kan till exempel ange värdet som `json` anspråk slutpunkt i den här LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Nej | Namnet på identitetsprovidern. |
| response_mode | Nej | Den metod som identitetsprovidern använder för att skicka resultatet tillbaka till Azure AD B2C. Möjliga värden: `query`, `form_post` (standard), eller `fragment`. |
| omfång | Nej | Omfattning förfrågan definieras enligt specifikationen OAuth2 identity-providern. Till exempel `openid`, `profile`, och `email`. |
| HttpBinding | Nej | Den förväntade HTTP-bindningen till åtkomst-token och anspråk token slutpunkterna. Möjliga värden: `GET` eller `POST`.  |
| ResponseErrorCodeParamName | Nej | Namnet på den parameter som innehåller det felmeddelande som returneras via HTTP 200 (Ok). |
| ExtraParamsInAccessTokenEndpointResponse | Nej | Innehåller de extra parametrar som kan returneras i svaret från **AccessTokenEndpoint** av vissa identitetsleverantörer. Exempel: svaret från **AccessTokenEndpoint** innehåller en extra parameter som `openid`, vilket är en obligatorisk parameter förutom access_token i en **ClaimsEndpoint** begäran fråga sträng. Flera parameternamn måste vara undantagna och avgränsade med semikolon ',' avgränsare. |
| ExtraParamsInClaimsEndpointRequest | Nej | Innehåller de extra parametrar som kan returneras i de **ClaimsEndpoint** begäran från vissa identitetsleverantörer. Flera parameternamn måste vara undantagna och avgränsade med semikolon ',' avgränsare. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Den **CryptographicKeys** elementet innehåller följande attribut:

| Attribut | Obligatoriskt | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemlighet för identitetsprogram för providern. Den kryptografiska nyckeln krävs endast om den **response_types** metadata är inställd på `code`. Azure AD B2C gör i det här fallet ett annat anrop till byta auktoriseringskod för en åtkomsttoken. Om metadata är inställt på `id_token` du kan utelämna den kryptografiska nyckeln.  |  

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar omdirigerings-URL: en för din identitetsprovider, ange `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Ersätt **klient** med klientens namn (exempel: contosob2c.onmicrosoft.com) och **policyId** med ID för principen (till exempel b2c_1a_policy). Omdirigerings-URI måste vara i alla gemener.

Om du använder den **b2clogin.com** domänen i stället för **login.microsoftonline.com** se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägg till Google + som identitetsprovider OAuth2 anpassade principer](active-directory-b2c-custom-setup-goog-idp.md)













