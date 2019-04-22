---
title: Definiera en OpenId Connect tekniska profilen i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en OpenId Connect tekniska profilen i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a13ca362bf08b86297641061992f0820f0b624c5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916775"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OpenId Connect tekniska profilen i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C har stöd för den [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protokollet identitetsprovider. OpenID Connect 1.0 definierar en identity-läger ovanpå OAuth 2.0 och representerar avancerade i moderna autentiseringsprotokoll.  Med OpenId Connect baserat tekniska profilen som du kan federera med en OpenId Connect identitetsprovider, t.ex Azure AD så att du användare kan logga in med sina befintliga sociala eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `OpenIdConnect`. Till exempel protokollet för den **MSA-OIDC** tekniska profilen är `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

Den **OutputClaims** elementet innehåller en lista över anspråk som returneras av identitetsprovider OpenId Connect. Du kan behöva matchar namnet på anspråk som definierats i din princip att namnet som definierats i identitetsprovidern. Du kan även inkludera anspråk som inte returneras av identitetsprovidern, så länge som du ställer in den `DefaultValue` attribut.

Den **OutputClaimsTransformations** element kan innehålla en uppsättning **OutputClaimsTransformation** element som används för att ändra de utgående anspråk eller generera nya.

I följande exempel visar de anspråk som returneras av identitetsprovider Account:

- Den **sub** anspråk som är mappad till den **socialIdpUserId** anspråk.
- Den **namn** anspråk som är mappad till den **displayName** anspråk.
- Den **e-post** utan mappning av användarnamn.

Den tekniska profilen returnerar också anspråk som inte returnerade poskytovatelem identity:

- Den **identityProvider** anspråk som innehåller namnet på identitetsprovidern.
- Den **authenticationSource** anspråk med ett standardvärde på **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program-ID för identitetsprovidern. |
| IdTokenAudience | Nej | Id_token målgrupp. Om anges kontrollerar om token är i ett anspråk som returnerades av identitetsprovidern och är lika med den som angetts i Azure AD B2C. |
| METADATA | Ja | En URL som pekar på en JSON-konfigurationsdokumentet formaterad enligt specifikationen OpenID Connect Discovery kallas även för en slutpunkt för välkända openid-konfiguration. |
| ProviderName | Nej | Namnet på identitetsprovidern. |
| response_types | Nej | Svarstypen enligt OpenID Connect Core 1.0-specifikation. Möjliga värden: `id_token`, `code`, eller `token`. |
| response_mode | Nej | Den metod som identitetsprovidern använder för att skicka resultatet tillbaka till Azure AD B2C. Möjliga värden: `query`, `form_post` (standard), eller `fragment`. |
| omfång | Nej | Omfattning förfrågan anges i enlighet OpenID Connect Core 1.0-specifikation. Till exempel `openid`, `profile`, och `email`. |
| HttpBinding | Nej | Den förväntade HTTP-bindningen till åtkomst-token och anspråk token slutpunkterna. Möjliga värden: `GET` eller `POST`.  |
| ValidTokenIssuerPrefixes | Nej | En nyckel som kan användas för att logga in på var och en av innehavarna som när du använder en identitetsprovider för flera innehavare, till exempel Azure Active Directory. |
| UsePolicyInRedirectUri | Nej | Anger om du vill använda en princip när omdirigeringen-URI. När du konfigurerar ditt program i identitetsprovidern som du behöver ange omdirigerings-URI. Omdirigerings-URI som pekar på Azure AD B2C `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com kan ändras med din klient name.b2clogin.com).  Om du anger `false`, du måste lägga till en omdirigerings-URI för varje princip som du använder. Till exempel: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nej | Anger om en begäran om att en extern tjänst ska markeras som misslyckat om Http-statuskoden är i 5xx-intervallet. Standardvärdet är `false`. |
| DiscoverMetadataByTokenIssuer | Nej | Anger om OIDC-metadata ska identifieras med hjälp av utfärdaren i JWT-token. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Den **CryptographicKeys** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemlighet för identitetsprogram för providern. Den kryptografiska nyckeln krävs endast om den **response_types** metadata är inställd på `code`. Azure AD B2C gör i det här fallet ett annat anrop till byta auktoriseringskod för en åtkomsttoken. Om metadata är inställt på `id_token` du kan utelämna den kryptografiska nyckeln.  |  

## <a name="redirect-uri"></a>Omdirigerings-Uri
 
När du konfigurerar omdirigeringen-URI för din identitetsprovider, ange `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Ersätt **klient** med klientens namn (exempel: contosob2c.onmicrosoft.com) eller den klient-Id. Omdirigerings-URI måste vara i alla gemener.

Om du använder den **b2clogin.com** domänen i stället för **login.microsoftonline.com** se till att använda b2clogin.com i stället för login.microsoftonline.com.

Exempel:

- [Lägg till Microsoft-konto (MSA) som en identitetsprovider med hjälp av anpassade principer](active-directory-b2c-custom-setup-msa-idp.md)
- [Logga in med hjälp av Azure AD-konton](active-directory-b2c-setup-aad-custom.md)
- [Tillåt användare att logga in på en identitetsprovider för flera innehavare Azure AD med hjälp av anpassade principer](active-directory-b2c-setup-commonaad-custom.md)

 














