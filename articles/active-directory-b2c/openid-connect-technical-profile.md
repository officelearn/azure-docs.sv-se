---
title: Definiera en teknisk profil för OpenID Connect i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för OpenID Connect i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e259a57a9cd6b24362862ffd6cb738157ca912d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332766"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för OpenID Connect i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ger stöd för [OpenID](https://openid.net/2015/04/17/openid-connect-certification-program/) Connect-protokollidentitetsprovidern. OpenID Connect 1.0 definierar ett identitetslager ovanpå OAuth 2.0 och representerar den senaste tekniken i moderna autentiseringsprotokoll. Med en teknisk profil för OpenID Connect kan du federera med en OpenID Connect-baserad identitetsprovider, till exempel Azure AD. Federerning med en identitetsleverantör tillåter användare att logga in med sina befintliga sociala identiteter eller företagsidentiteter.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `OpenIdConnect`anges till . Protokollet för den tekniska profilen **MSA-OIDC** är `OpenIdConnect`till exempel:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
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

**Elementet OutputClaims** innehåller en lista över anspråk som returneras av OpenID Connect-identitetsprovidern. Du kan behöva mappa namnet på anspråket som definieras i din policy till det namn som definierats i identitetsleverantören. Du kan också inkludera anspråk som inte returneras av identitetsprovidern, så länge du anger `DefaultValue` attributet.

**Elementet OutputClaimsTransformations** kan innehålla en samling **OutputClaimsTransformation-element** som används för att ändra utdataanspråken eller generera nya.

I följande exempel visas de anspråk som returneras av identitetsleverantören för Microsoft-kontot:

- Det **underpåstående** som mappas till **utfärdarenUserId-anspråk.**
- **Namnanspråket** som mappas till **anspråket displayName.**
- **E-postmeddelandet** utan namnmappning.

Den tekniska profilen returnerar också anspråk som inte returneras av identitetsleverantören:

- **Identitetprovider-anspråk** som innehåller namnet på identitetsleverantören.
- **AutentiseringKällanspråk** med standardvärdet **socialIdpAuthentication**.

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
| client_id | Ja | Programidentifieraren för identitetsprovidern. |
| IdTokenAudience | Inga | Publiken i id_token. Om det anges kontrollerar Azure AD `aud` B2C om anspråket i en token som returneras av identitetsprovidern är lika med det som anges i IdTokenAudience-metadata.  |
| Metadata | Ja | En URL som pekar på ett OpenID Connect-konfigurationsdokument för identitetsprovider, som också kallas OpenID-välkänd konfigurationsslutpunkt. URL:en kan `{tenant}` innehålla uttrycket som ersätts med klientnamnet.  |
| authorization_endpoint | Inga | En URL som pekar på en OpenID Connect-identitetsproviderkonfigurationsaduktoriseringsslutpunkt. Värdet för authorization_endpoint metadata har företräde framför `authorization_endpoint` det angivna i openID-den välkända konfigurationsslutpunkten. URL:en kan `{tenant}` innehålla uttrycket som ersätts med klientnamnet. |
| utfärdare | Inga | Den unika identifieraren för en OpenID Connect-identitetsprovider. Värdet för utfärdarmetadata har `issuer` företräde framför det angivna i den välkända konfigurationsslutpunkten OpenID.  Om det anges kontrollerar Azure AD `iss` B2C om anspråket i en token som returneras av identitetsprovidern är lika med det som anges i utfärdarmetadata. |
| ProviderName | Inga | Namnet på identitetsprovidern.  |
| response_types | Inga | Svarstypen enligt OpenID Connect Core 1.0-specifikationen. Möjliga `id_token`värden: `code`, `token`, eller . |
| response_mode | Inga | Den metod som identitetsprovidern använder för att skicka tillbaka resultatet till Azure AD B2C. Möjliga `query`värden: `form_post` , (standard) eller `fragment`. |
| omfång | Inga | Omfattningen av begäran som definieras enligt OpenID Connect Core 1.0-specifikationen. Till `openid`exempel `profile`, `email`och . |
| HttpBinding (Påminn) | Inga | Den förväntade HTTP-bindningen till åtkomsttoken och anspråkstokenslutpunkter. Möjliga `GET` värden: `POST`eller .  |
| ValidTokenIssuerPrefixes | Inga | En nyckel som kan användas för att logga in på var och en av klienterna när du använder en identitetsprovider för flera innehavare, till exempel Azure Active Directory. |
| UsePolicyInRedirectUri | Inga | Anger om en princip ska användas när du skapar omdirigerings-URI:n. När du konfigurerar programmet i identitetsprovidern måste du ange omdirigerings-URI.When you configure your application in the identity provider, you need to specify the redirect URI. Omdirigera URI pekar på Azure AD `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`B2C, .  Om du `false`anger måste du lägga till en omdirigerings-URI för varje princip du använder. Till exempel: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Inga | Anger om en begäran till en extern tjänst ska markeras som ett fel om http-statuskoden finns i intervallet 5xx. Standardvärdet är `false`. |
| UpptäckMetadataByTokenIssuer | Inga | Anger om OIDC-metadata ska identifieras med hjälp av utfärdaren i JWT-token. |
| InkluderaClaimResolvingInClaimsHandling  | Inga | För indata- och utdataanspråk anger om [anspråksresolution](claim-resolver-overview.md) ingår i den tekniska profilen. Möjliga värden: `true` `false`  , eller (standard). Om du vill använda en anspråksmatchningsmatchare `true`i den tekniska profilen ställer du in den på . |

### <a name="ui-elements"></a>Element för användargränssnitt
 
Följande inställningar kan användas för att konfigurera felmeddelandet som visas vid fel. Metadata ska konfigureras i den tekniska profilen OpenID Connect. Felmeddelandena kan [lokaliseras](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesExist | Inga | Meddelandet som ska visas för användaren om ett konto med det angivna användarnamnet inte finns i katalogen. |
| AnvändareMessageIfInvalidPassword | Inga | Meddelandet som ska visas för användaren om lösenordet är felaktigt. |
| UserMessageIfOldPasswordUsed| Inga |  Meddelandet som ska visas för användaren om ett gammalt lösenord används.|

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet **CryptographicKeys** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| client_secret | Ja | Klienthemligheten för identitetsproviderprogrammet. Den kryptografiska nyckeln krävs endast om **response_types** metadata `code`är inställd på . I det här fallet gör Azure AD B2C ett annat anrop för att byta auktoriseringskoden för en åtkomsttoken. Om metadata är `id_token` inställd på kan du utelämna den kryptografiska nyckeln.  |

## <a name="redirect-uri"></a>Omdirigera Uri

När du konfigurerar omdirigerings-URI för din identitetsleverantör anger du `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp`. Se till `{your-tenant-name}` att ersätta med din klients namn. Omdirigerings-URI:n måste vara i alla gemener.

Exempel:

- [Lägga till Microsoft-konto (MSA) som identitetsleverantör med hjälp av anpassade principer](identity-provider-microsoft-account-custom.md)
- [Logga in med Azure AD-konton](identity-provider-azure-ad-single-tenant-custom.md)
- [Tillåt användare att logga in på en Azure AD-identitetsprovider för flera innehavare med hjälp av anpassade principer](identity-provider-azure-ad-multi-tenant-custom.md)
