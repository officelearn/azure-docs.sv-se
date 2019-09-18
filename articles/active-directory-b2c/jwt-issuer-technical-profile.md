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
ms.openlocfilehash: 47c5f9a364f4968784cea96a09e938906f39ef4f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064118"
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
| issuer_refresh_token_user_identity_claim_type | Ja | Det anspråk som ska användas som användar identitets anspråk i OAuth2-auktoriseringskod och uppdateringstoken. Som standard bör du ställa in det på `objectId`, om du inte anger en annan SubjectNamingInfo-anspråks typ. |
| SendTokenResponseBodyWithJsonNumbers | Nej | Ställ alltid in `true`på. För äldre format där numeriska värden anges som strängar i stället för JSON-tal, anges `false`till. Det här attributet krävs för klienter som har tagit ett beroende på en tidigare implementering som returnerade sådana egenskaper som strängar. |
| token_lifetime_secs | Nej | Livstid för åtkomsttoken. Livs längden för OAuth 2,0 Bearer-token som används för att få åtkomst till en skyddad resurs. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Det största (inklusive) är 86 400 sekunder (24 timmar). |
| id_token_lifetime_secs | Nej | Livstid för ID-token. Standardvärdet är 3 600 sekunder (1 timme). Minimivärdet (inklusive) är 300 sekunder (5 minuter). Max (inklusive) är sekunder 86 400 (24 timmar). |
| refresh_token_lifetime_secs | Nej | Livstid för token för uppdatering. Den längsta tids period innan en uppdateringstoken kan användas för att hämta en ny åtkomsttoken, om ditt program har beviljats offline_access-omfånget. Standardvärdet är 120, 9600 sekunder (14 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 7 776 000 sekunder (90 dagar). |
| rolling_refresh_token_lifetime_secs | Nej | Uppdatera token glidande fönster livs längd. När den här tids perioden har förflutit måste användaren autentiseras på nytt, oberoende av giltighets perioden för den senaste uppdateringstoken som hämtats av programmet. Om du inte vill framtvinga en glidande fönster livs längd ställer du in värdet för allow_infinite_rolling_refresh_token på `true`. Standardvärdet är 7 776 000 sekunder (90 dagar). Minimivärdet (inklusive) är 86 400 sekunder (24 timmar). Det största (inklusive) är 31 536 000 sekunder (365 dagar). |
| allow_infinite_rolling_refresh_token | Nej | Om det är `true`inställt på, förfaller den glidande tidsintervallen för uppdateringstoken aldrig. |
| IssuanceClaimPattern | Ja | Styr utfärdaren (ISS)-anspråket. Ett av värdena:<ul><li>AuthorityAndTenantGuid – IIS-anspråket inkluderar ditt domän namn, `login.microsoftonline` till `tenant-name.b2clogin.com`exempel eller, och klient-ID: t https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp – IIS-anspråket inkluderar ditt domän namn, `login.microsoftonline` till `tenant-name.b2clogin.com`exempel eller, ditt klient-ID och namnet på den förlitande partens princip. [https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/](https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/ )</li></ul> |
| AuthenticationContextReferenceClaimPattern | Nej | `acr` Kontrollerar anspråk svärdet.<ul><li>Ingen-Azure AD B2C utfärdar inte ACR-anspråket</li><li>PolicyId- `acr` anspråket innehåller princip namnet</li></ul>Alternativen för att ange det här värdet är TFP (Trust Framework policy) och ACR (Authentication context Reference). Vi rekommenderar att du ställer in värdet på TFP, för att ange värdet, se `<Item>` `Key="AuthenticationContextReferenceClaimPattern"` till att det finns och att värdet `None`är. Lägg till `<OutputClaims>` objekt i principen för den förlitande parten, Lägg `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`till det här elementet. Kontrol lera också att principen innehåller anspråks typen`<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |

## <a name="cryptographic-keys"></a>Kryptografiska nycklar

CryptographicKeys-elementet innehåller följande attribut:

| Attribut | Obligatorisk | Beskrivning |
| --------- | -------- | ----------- |
| issuer_secret | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att signera JWT-token. Det här är `B2C_1A_TokenSigningKeyContainer` den nyckel som du har [samarbetat med för att komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). |
| issuer_refresh_token_key | Ja | X509-certifikatet (RSA-nyckel uppsättning) som används för att kryptera uppdateringstoken. Du konfigurerade `B2C_1A_TokenEncryptionKeyContainer` nyckeln i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) |














