---
title: Definiera en teknisk profil för en JWT-token-utfärdare i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en teknisk profil för en JWT-token-utfärdare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5b8f299826c5688eb80eaea11ffc3b2b5176297
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959669"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för en JWT-token-utfärdare i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken när de bearbetar varje autentiseringspaket. En teknisk profil för en JWT-token utfärdar en JWT-token som returneras tillbaka till det förlitande part programmet. Vanligt vis är den här tekniska profilen det sista Orchestration-steget i användar resan.

## <a name="protocol"></a>Protocol

Namnattributet **för** **protokoll** elementet måste anges till `None`. Ange **OutputTokenFormat** -elementet till `JWT`.

I följande exempel visas en teknisk profil för `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Indata, utdata och beständiga anspråk

**InputClaims**-, **OutputClaims**-och **PersistClaims** -elementen är tomma eller saknas. **InutputClaimsTransformations** -och **OutputClaimsTransformations** -element saknas också.

## <a name="metadata"></a>Metadata

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | Det anspråk som ska användas som användar identitets anspråk i OAuth2-auktoriseringskod och uppdateringstoken. Som standard ska du ställa in den på `objectId`, om du inte anger en annan SubjectNamingInfo anspråks typ. |
| SendTokenResponseBodyWithJsonNumbers | Nej | Ange alltid till `true`. För äldre format där numeriska värden anges som strängar i stället för JSON-tal, anger du `false`. Det här attributet krävs för klienter som har tagit ett beroende på en tidigare implementering som returnerade sådana egenskaper som strängar. |
| token_lifetime_secs | Nej | Livstid för åtkomsttoken. Livs längden för OAuth 2,0 Bearer-token som används för att få åtkomst till en skyddad resurs. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Det största (inklusive) är 86 400 sekunder (24 timmar). |
| id_token_lifetime_secs | Nej | Livstid för ID-token. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Max (inklusive) är sekunder 86 400 (24 timmar). |
| refresh_token_lifetime_secs | Nej | Livstid för token för uppdatering. Den längsta tids period innan en uppdateringstoken kan användas för att hämta en ny åtkomsttoken, om ditt program har beviljats offline_access-omfånget. Standardvärdet är 120, 9600 sekunder (14 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 7 776 000 sekunder (90 dagar). |
| rolling_refresh_token_lifetime_secs | Nej | Uppdatera token glidande fönster livs längd. När den här tids perioden har förflutit måste användaren autentiseras på nytt, oberoende av giltighets perioden för den senaste uppdateringstoken som hämtats av programmet. Om du inte vill framtvinga en glidande fönster livs längd ställer du in värdet för allow_infinite_rolling_refresh_token på `true`. Standardvärdet är 7 776 000 sekunder (90 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 31 536 000 sekunder (365 dagar). |
| allow_infinite_rolling_refresh_token | Nej | Om värdet är inställt på `true` går tids perioden för glidande tidsintervall aldrig ut. |
| IssuanceClaimPattern | Ja | Styr utfärdaren (ISS)-anspråket. Ett av värdena:<ul><li>AuthorityAndTenantGuid – IIS-anspråket innehåller ditt domän namn, till exempel `login.microsoftonline` eller `tenant-name.b2clogin.com` och klient-ID: t https: \//login. microsoftonline. com/00000000-0000-0000-0000-000000000000/v 2.0/</li><li>AuthorityWithTfp – IIS-anspråket inkluderar ditt domän namn, till exempel `login.microsoftonline` eller `tenant-name.b2clogin.com`, ditt klient-ID och namnet på den förlitande partens princip. https: \//login. microsoftonline. com/TFP/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-eller-Sign-in/v 2.0/</li></ul> |
| AuthenticationContextReferenceClaimPattern | Nej | Kontrollerar värdet för `acr`-anspråk.<ul><li>Ingen-Azure AD B2C utfärdar inte ACR-anspråket</li><li>PolicyId-anspråket `acr` innehåller princip namnet</li></ul>Alternativen för att ange det här värdet är TFP (Trust Framework policy) och ACR (Authentication context Reference). Vi rekommenderar att du anger värdet TFP för att ange värdet, se till att `<Item>` med `Key="AuthenticationContextReferenceClaimPattern"` finns och att värdet är `None`. I principen för förlitande part lägger du till `<OutputClaims>` objekt, lägger till det här elementet `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Kontrol lera också att principen innehåller anspråks typen `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

CryptographicKeys-elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| issuer_secret | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera JWT-token. Detta är den `B2C_1A_TokenSigningKeyContainer`-nyckel som du [har samarbetat i kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att kryptera uppdateringstoken. Du konfigurerade `B2C_1A_TokenEncryptionKeyContainer`-nyckeln i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) |














