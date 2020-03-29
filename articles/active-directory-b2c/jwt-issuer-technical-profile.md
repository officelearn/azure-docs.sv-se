---
title: Definiera en teknisk profil för en JWT-utfärdare i en anpassad princip
titleSuffix: Azure AD B2C
description: Definiera en teknisk profil för en JWT-utfärdare (JWT) (JWT) i en anpassad princip i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671784"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en teknisk profil för en JWT-tokenutfärdare i en anpassad Azure Active Directory B2C-princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) avger flera typer av säkerhetstoken när den bearbetar varje autentiseringsflöde. En teknisk profil för en JWT-tokenutfärdare avger en JWT-token som returneras tillbaka till det förlitande part-programmet. Vanligtvis är denna tekniska profil det sista orchestration-steget i användarresan.

## <a name="protocol"></a>Protokoll

**Attributet Name** för **protokollelementet** måste `None`anges till . Ange elementet **OutputTokenFormat** till `JWT`.

I följande exempel visas `JwtIssuer`en teknisk profil för:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Indata, utdata och kvarstående anspråk

Elementen **InputClaims**, **OutputClaims**och **PersistClaims** är tomma eller frånvarande. **InutputClaimsTransformations** och **OutputClaimsTransformations** element är också frånvarande.

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | Påståendet som ska användas som användaridentitetsanspråk i OAuth2-auktoriseringskoderna och uppdatera token. Som standard bör du `objectId`ställa in den på , såvida du inte anger en annan SubjectNamingInfo-anspråkstyp. |
| SendTokenResponseBodyWithJsonNumbers | Inga | Alltid inställd `true`på . För äldre format där numeriska värden anges som strängar i `false`stället för JSON-tal anger du till . Det här attributet behövs för klienter som har tagit ett beroende av en tidigare implementering som returnerade sådana egenskaper som strängar. |
| token_lifetime_secs | Inga | Få tillgång till tokenlivstid. Livslängden för OAuth 2.0-innehavartoken som används för att få åtkomst till en skyddad resurs. Standardvärdet är 3 600 sekunder (1 timme). Den minsta (inklusive) är 300 sekunder (5 minuter). Den maximala (inklusive) är 86.400 sekunder (24 timmar). |
| id_token_lifetime_secs | Inga | ID token livstider. Standardvärdet är 3 600 sekunder (1 timme). Den minsta (inklusive) är 300 sekunder (5 minuter). Den maximala (inklusive) är sekunder 86.400 (24 timmar). |
| refresh_token_lifetime_secs | Inga | Uppdatera tokenlivstiderna. Den maximala tidsperioden före vilken en uppdateringstoken kan användas för att hämta en ny åtkomsttoken, om ditt program hade beviljats offline_access-scopet. Standardvärdet är 120 9600 sekunder (14 dagar). Den minsta (inklusive) är 86.400 sekunder (24 timmar). Den maximala (inklusive) är 7.776.000 sekunder (90 dagar). |
| rolling_refresh_token_lifetime_secs | Inga | Uppdatera token skjutfönster livstid. Efter denna tidsperiod förfluten användaren tvingas att återautentisering, oavsett giltighetsperioden för den senaste uppdateringstoken som förvärvats av programmet. Om du inte vill framtvinga en glidande fönsterlivslängd `true`anger du värdet för allow_infinite_rolling_refresh_token till . Standardvärdet är 7 776 000 sekunder (90 dagar). Den minsta (inklusive) är 86.400 sekunder (24 timmar). Den maximala (inklusive) är 31.536.000 sekunder (365 dagar). |
| allow_infinite_rolling_refresh_token | Inga | Om den `true`är inställd på upphör aldrig att uppdatera token glidande fönster livstid. |
| EmissionErClaimPattern | Inga | Kontrollerar emittentens (iss)-anspråk. Ett av värdena:<ul><li>AuthorityAndTenantGuid - Iss-anspråket innehåller ditt `login.microsoftonline` `tenant-name.b2clogin.com`domännamn, till exempel eller\/, och din klientidentifierare https: /login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - Iss-anspråket innehåller ditt `login.microsoftonline` `tenant-name.b2clogin.com`domännamn, till exempel eller , din klientidentifierare och ditt förlitande partepolicynamn. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> Standardvärde: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | Inga | Kontrollerar `acr` anspråksvärdet.<ul><li>Ingen - Azure AD B2C utfärdar inte acr-anspråket</li><li>PolicyId - `acr` anspråket innehåller principnamnet</li></ul>Alternativen för att ange det här värdet är TFP (trust framework policy) och ACR (autentiseringskontextreferens). Det rekommenderas att det här värdet ställs in på `<Item>` TFP, för att ange värdet, se till att det `Key="AuthenticationContextReferenceClaimPattern"` finns med och värdet är `None`. Lägg till `<OutputClaims>` det här elementet i `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`den förlitande partiprincipen. Kontrollera också att din policy innehåller anspråkstypen`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|UppdateraTokenUserJourneyId| Inga | Identifieraren för en användarfärd som ska köras under uppdateringen av `/token` en POST-begäran för [åtkomsttoken](authorization-code-flow.md#4-refresh-the-token) till slutpunkten. |

## <a name="cryptographic-keys"></a>Krypteringsnycklar

Elementet CryptographicKeys innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_secret | Ja | X509-certifikatet (RSA-nyckeluppsättningen) som ska användas för att signera JWT-token. Det här `B2C_1A_TokenSigningKeyContainer` är nyckeln som du konfigurerade i [Komma igång med anpassade principer](custom-policy-get-started.md). |
| issuer_refresh_token_key | Ja | X509-certifikatet (RSA-nyckeluppsättningen) som ska användas för att kryptera uppdateringstoken. Du har `B2C_1A_TokenEncryptionKeyContainer` konfigurerat nyckeln i [Komma igång med anpassade principer](custom-policy-get-started.md) |














