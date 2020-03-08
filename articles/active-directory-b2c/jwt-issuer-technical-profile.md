---
title: Definiera en teknisk profil för en JWT-utfärdare i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för en JSON Web token (JWT)-utfärdare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c23648d70192607b2a5b977dcdd445931e995154
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671784"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för en JWT-token-utfärdare i en Azure Active Directory B2C anpassad princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) genererar flera typer av säkerhetstoken när de bearbetar varje autentiseringspaket. En teknisk profil för en JWT-token utfärdar en JWT-token som returneras tillbaka till det förlitande part programmet. Vanligt vis är den här tekniska profilen det sista Orchestration-steget i användar resan.

## <a name="protocol"></a>Protokoll

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

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | Det anspråk som ska användas som användar identitets anspråk i OAuth2-auktoriseringskod och uppdateringstoken. Som standard ska du ställa in den på `objectId`, om du inte anger en annan typ av SubjectNamingInfo-anspråk. |
| SendTokenResponseBodyWithJsonNumbers | Nej | Ange alltid till `true`. För äldre format där numeriska värden anges som strängar i stället för JSON-tal, anger du `false`. Det här attributet krävs för klienter som har tagit ett beroende på en tidigare implementering som returnerade sådana egenskaper som strängar. |
| token_lifetime_secs | Nej | Livstid för åtkomsttoken. Livs längden för OAuth 2,0 Bearer-token som används för att få åtkomst till en skyddad resurs. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Det största (inklusive) är 86 400 sekunder (24 timmar). |
| id_token_lifetime_secs | Nej | Livstid för ID-token. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Max (inklusive) är sekunder 86 400 (24 timmar). |
| refresh_token_lifetime_secs | Nej | Livstid för token för uppdatering. Den längsta tids period innan en uppdateringstoken kan användas för att hämta en ny åtkomsttoken, om ditt program har beviljats offline_access omfattning. Standardvärdet är 120, 9600 sekunder (14 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 7 776 000 sekunder (90 dagar). |
| rolling_refresh_token_lifetime_secs | Nej | Uppdatera token glidande fönster livs längd. När den här tids perioden har förflutit måste användaren autentiseras på nytt, oberoende av giltighets perioden för den senaste uppdateringstoken som hämtats av programmet. Om du inte vill framtvinga en glidande fönster livs längd ställer du in värdet för allow_infinite_rolling_refresh_token `true`. Standardvärdet är 7 776 000 sekunder (90 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 31 536 000 sekunder (365 dagar). |
| allow_infinite_rolling_refresh_token | Nej | Om det är inställt på `true`, förfaller den glidande tidsintervallen för uppdateringstoken aldrig. |
| IssuanceClaimPattern | Nej | Styr utfärdaren (ISS)-anspråket. Ett av värdena:<ul><li>AuthorityAndTenantGuid – IIS-anspråket inkluderar ditt domän namn, till exempel `login.microsoftonline` eller `tenant-name.b2clogin.com`och klient-ID https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp – IIS-anspråket inkluderar ditt domän namn, till exempel `login.microsoftonline` eller `tenant-name.b2clogin.com`, klient-ID och namnet på den förlitande parten. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Standardvärde: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Nej | Kontrollerar värdet för `acr` anspråk.<ul><li>Ingen-Azure AD B2C utfärdar inte ACR-anspråket</li><li>PolicyId – det `acr` anspråk innehåller princip namnet</li></ul>Alternativen för att ange det här värdet är TFP (Trust Framework policy) och ACR (Authentication context Reference). Du rekommenderas att ange värdet TFP för att ange värdet, se till att `<Item>` med `Key="AuthenticationContextReferenceClaimPattern"` finns och att värdet är `None`. Lägg till `<OutputClaims>` objekt i principen för förlitande part, Lägg till det här elementet `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Kontrol lera också att principen innehåller anspråks typen `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| Nej | Identifieraren för en användar resa som ska utföras under [uppdateringen av en åtkomstbegäran](authorization-code-flow.md#4-refresh-the-token) POST till `/token` slut punkten. |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

CryptographicKeys-elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_secret | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera JWT-token. Det här är den `B2C_1A_TokenSigningKeyContainer` nyckel som du har [samarbetat med för att komma igång med anpassade principer](custom-policy-get-started.md). |
| issuer_refresh_token_key | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att kryptera uppdateringstoken. Du har konfigurerat `B2C_1A_TokenEncryptionKeyContainer` nyckel i [Kom igång med anpassade principer](custom-policy-get-started.md) |














