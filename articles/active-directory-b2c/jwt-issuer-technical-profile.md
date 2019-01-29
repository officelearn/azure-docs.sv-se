---
title: Definiera en tekniska profilen för en JWT tokenutfärdare i en anpassad princip i Azure Active Directory B2C | Microsoft Docs
description: Definiera en tekniska profilen för en JWT tokenutfärdare i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: dabffd88965a10fe4feca2b600cfaf5741e2f664
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157915"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiera en tekniska profilen för en JWT tokenutfärdare i en anpassad princip för Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C skickar flera typer av säkerhetstoken när den går igenom varje autentiseringsflödet. En teknisk profil för en JWT-tokenutfärdare genererar en JWT-token som returneras till förlitande part-programmet. Den här tekniska profilen är vanligtvis det sista orkestreringssteget i användarresan.

## <a name="protocol"></a>Protokoll

Den **namn** attributet för den **protokollet** element måste anges till `None`. Ange den **OutputTokenFormat** elementet mot `JWT`.

I följande exempel visas en tekniska profil för `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Indata, utdata och spara anspråk

Den **InputClaims**, **OutputClaims**, och **PersistClaims** element är tom eller saknas. Den **InutputClaimsTransformations** och **OutputClaimsTransformations** element är också.

## <a name="metadata"></a>Metadata

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Ja | Anspråk som ska användas som användar-ID anspråk i OAuth2-auktoriseringskoder och uppdateringstoken. Som standard ska du ange den till `objectId`, såvida du inte anger en annan SubjectNamingInfo Anspråkstypen. | 
| SendTokenResponseBodyWithJsonNumbers | Nej | Alltid inställt `true`. För äldre format där numeriska värden anges som strängar i stället för JSON-nummer, inställt på `false`. Det här attributet krävs för klienter som har tagit ett beroende på en tidigare implementering som returnerade egenskaper, till exempel som strängar. | 
| token_lifetime_secs | Nej | Tokenlivslängder för åtkomst. Livslängden för den OAuth 2.0-ägartoken som användes för att få åtkomst till en skyddad resurs. Standardvärdet är 3 600 sekunder (1 timme). Minst (inklusivt) är 300 sekunder (fem minuter). Högsta (inklusivt) är 86 400 sekunder (24 timmar). | 
| id_token_lifetime_secs | Nej | ID tokenlivslängder. Standardvärdet är 3 600 sekunder (1 timme). Minst (inklusivt) är 300 sekunder (fem minuter). Max (inklusivt) är sekunder 86,400 (24 timmar). | 
| refresh_token_lifetime_secs | Nej | Uppdatera livslängd för token. Den maximala tidsperiod innan vilken en uppdateringstoken kan användas till att skaffa en ny åtkomsttoken om programmet har beviljats offline_access omfånget. Standardvärdet är 120,9600 sekunder (14 dagar). Minimivärdet (inklusivt) är 86 400 sekunder (24 timmar). Max (inklusivt) är 7,776,000 sekunder (90 dagar). | 
| rolling_refresh_token_lifetime_secs | Nej | Uppdatera token livslängd för skjutfönster. När den här tidsperioden har gått ut måste tvingas användaren att autentiseras på nytt, oavsett giltighetsperioden hos den senaste uppdateringstoken som införskaffats av programmet. Om du inte vill att framtvinga en livslängd för skjutfönster, ange värdet för allow_infinite_rolling_refresh_token till `true`. Standardvärdet är 7,776,000 sekunder (90 dagar). Minimivärdet (inklusivt) är 86 400 sekunder (24 timmar). Max (inklusivt) är 31,536,000 sekunder (365 dagar). | 
| allow_infinite_rolling_refresh_token | Nej | Om inställd `true`, uppdateringstoken skjutfönster livslängd upphör aldrig att gälla. |
| IssuanceClaimPattern | Ja | Styr utfärdare (iss)-anspråk. Ett av värdena:<ul><li>AuthorityAndTenantGuid - iss-anspråk innehåller ditt domännamn, t.ex. `login.microsoftonline` eller `tenant-name.b2clogin.com`, och din klient-ID https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - iss-anspråk innehåller ditt domännamn, t.ex. `login.microsoftonline` eller `tenant-name.b2clogin.com`, din klient-ID och namnet på din förlitande part. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Nej | Kontroller i `acr` anspråksvärde.<ul><li>Ingen - Azure AD B2C inte utfärda anspråk för acr</li><li>PolicyId - den `acr` anspråk innehåller namnet på principen</li></ul>Alternativ för det här värdet är TFP (förtroendeprincipen för framework) och ACR (autentisering kontext referens). Rekommenderas TFP att ställa in det här värdet, för att ange värdet, se till att den `<Item>` med den `Key="AuthenticationContextReferenceClaimPattern"` finns och värdet är `None`. Lägg till förlitande part kräva <OutputClaims> objektet, Lägg till elementet `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. Kontrollera också att din princip innehåller Anspråkstypen `<ClaimType Id="trustFrameworkPolicy"> <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>Krypteringsnycklar

CryptographicKeys-elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| issuer_secret | Ja | X509 certifikat (RSA nyckeluppsättning) som ska användas för att signera JWT-token. Det här är den `B2C_1A_TokenSigningKeyContainer` viktiga konfigureras i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Ja | X509 certifikat (RSA nyckeluppsättning) som ska användas för att kryptera uppdateringstoken. Du har konfigurerat den `B2C_1A_TokenEncryptionKeyContainer` nyckeln i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) |














