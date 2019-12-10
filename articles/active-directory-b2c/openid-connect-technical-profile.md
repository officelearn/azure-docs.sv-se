---
title: Definiera en OpenID Connect-teknisk profil i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en OpenID Connect Technical Profile i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fdb925f820cad79fe68e7082f4ed63292a7d9444
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951096"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en OpenID Connect-teknisk profil i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) har stöd för [OpenID Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) Protocol Identity Provider. OpenID Connect 1,0 definierar ett identitets lager ovanpå OAuth 2,0 och representerar status för grafiken i moderna autentiseringsprotokoll. Med en OpenID Connect-teknisk profil kan du federera med en OpenID Connect-baserad identitets leverantör, t. ex. Azure AD. Genom att federera med en identitets leverantör kan användare logga in med sina befintliga sociala identiteter eller företags identiteter.

## <a name="protocol"></a>Protokoll

Namnattributet **för** **protokoll** elementet måste anges till `OpenIdConnect`. Till exempel är protokollet för **MSA-OIDC** teknisk profil `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Inmatade anspråk

Elementen **InputClaims** och **InputClaimsTransformations** krävs inte. Men du kanske vill skicka ytterligare parametrar till din identitets leverantör. I följande exempel läggs parametern **domain_hint** frågesträng till med värdet för `contoso.com` till auktoriseringsbegäran.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Utgående anspråk

**OutputClaims** -elementet innehåller en lista över anspråk som returneras av OpenID Connect Identity Provider. Du kan behöva mappa namnet på det anspråk som definierats i principen till det namn som definierats i identitets leverantören. Du kan också inkludera anspråk som inte returneras av identitets leverantören, så länge som du ställer in `DefaultValue`-attributet.

**OutputClaimsTransformations** -elementet kan innehålla en samling av **OutputClaimsTransformation** -element som används för att ändra de utgående anspråken eller skapa nya.

I följande exempel visas de anspråk som returneras av Microsoft-kontots identitets leverantör:

- Det **under** anspråk som är mappat till **issuerUserId** -anspråket.
- Det **namn** anspråk som är mappat till anspråket **DisplayName** .
- **E-postmeddelandet** utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

- **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
- **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_id | Ja | Program identifieraren för identitets leverantören. |
| IdTokenAudience | Nej | Id_tokenens mål grupp. Om det här alternativet anges kontrollerar Azure AD B2C om token finns i ett anspråk som returneras av identitets leverantören och är lika med det som anges. |
| METADATATJÄNST | Ja | En URL som pekar på ett JSON-konfigurationsobjekt formaterat enligt OpenID Connect Discovery Specification, som även kallas en välkänd OpenID konfigurations slut punkt. |
| ProviderName | Nej | Namnet på identitets leverantören. |
| response_types | Nej | Svars typen enligt OpenID Connect core 1,0-specifikationen. Möjliga värden: `id_token`, `code`eller `token`. |
| response_mode | Nej | Metoden som identitets leverantören använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga värden: `query`, `form_post` (standard) eller `fragment`. |
| omfång | Nej | Omfattningen av begäran som definieras enligt OpenID Connect core 1,0-specifikationen. Till exempel `openid`, `profile`och `email`. |
| HttpBinding | Nej | Den förväntade HTTP-bindningen till åtkomsttoken och slut punkter för anspråks-token. Möjliga värden: `GET` eller `POST`.  |
| ValidTokenIssuerPrefixes | Nej | En nyckel som kan användas för att logga in på varje klient organisation när du använder en identitets leverantör för flera innehavare, till exempel Azure Active Directory. |
| UsePolicyInRedirectUri | Nej | Anger om en princip ska användas vid konstruktion av omdirigerings-URI. När du konfigurerar ditt program i identitets leverantören måste du ange omdirigerings-URI: n. Omdirigerings-URI: n pekar på Azure AD B2C `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`.  Om du anger `false`måste du lägga till en omdirigerings-URI för varje princip som du använder. Till exempel: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nej | Anger om en begäran till en extern tjänst ska markeras som ett haveri om HTTP-statuskoden finns i 5xx-intervallet. Standardvärdet är `false`. |
| DiscoverMetadataByTokenIssuer | Nej | Anger om OIDC metadata ska identifieras med hjälp av utfärdaren i JWT-token. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

**CryptographicKeys** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klient hemligheten för Identity Provider-programmet. Den kryptografiska nyckeln krävs endast om **response_types** metadata har angetts till `code`. I det här fallet gör Azure AD B2C ett annat anrop till Exchange-auktoriseringskod för en åtkomsttoken. Om metadata är inställt på `id_token` kan du utelämna den kryptografiska nyckeln.  |

## <a name="redirect-uri"></a>Omdirigerings-URI

När du konfigurerar en omdirigerings-URI för din identitetsprovider anger du `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Se till att ersätta `{your-tenant-name}` med klient organisationens namn. Omdirigerings-URI: n måste vara i gemener.

Exempel:

- [Lägg till Microsoft-konto (MSA) som en identitets leverantör med anpassade principer](active-directory-b2c-custom-setup-msa-idp.md)
- [Logga in med Azure AD-konton](active-directory-b2c-setup-aad-custom.md)
- [Tillåt att användare loggar in på en Azure AD-identitetsprovider med flera innehavare med anpassade principer](active-directory-b2c-setup-commonaad-custom.md)
