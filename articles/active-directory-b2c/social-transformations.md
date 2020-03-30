---
title: Exempel på omvandling av sociala kontoanspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Exempel på omvandling av sociala kontoanspråk för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183762"
---
# <a name="social-accounts-claims-transformations"></a>Sociala konton hävdar omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) `userIdentities` lagras sociala kontoidentiteter i ett attribut av en **alternativSecurityIdCollection-anspråkstyp.** Varje objekt i **alternativeSecurityIdCollection** anger utfärdaren (identitetsproviderns namn, till `issuerUserId`exempel facebook.com) och , som är en unik användaridentifierare för utfärdaren.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Den här artikeln innehåller exempel på hur du använder omvandlingar av sociala kontoanspråk i Identity Experience Framework-schemat i Azure AD B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>SkapaAlternativeSecurityId

Skapar en JSON-representation av användarens alternativeSecurityId-egenskap som kan användas i anropen till Azure Active Directory. Mer information finns i [AlternativeSecurityId-schemat.](https://docs.microsoft.com/graph/api/resources/alternativesecurityid)

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | sträng | Den ClaimType som anger den unika användaridentifieraren som används av den sociala identitetsprovidern. |
| InputClaim | identitetProvider | sträng | Den ClaimType som anger namnet på den sociala kontoidentitetsleverantören, till exempel facebook.com. |
| OutputClaim | alternativeSecurityId | sträng | Den ClaimType som produceras efter att ClaimsTransformation har anropats. Innehåller information om identiteten på en användare av ett socialt konto. **Emittenten** är fordrans `identityProvider` värde. **UtfärdarenUserId** är värdet av `key` anspråket i base64-format. |

Använd den här anspråksomvandlingen för att generera en `alternativeSecurityId` ClaimType. Det används av alla sociala identitetsprovider `Facebook-OAUTH`tekniska profiler, till exempel . Följande anspråksomvandling tar emot användarens sociala konto-ID och identitetsproviderns namn. Utdata för den här tekniska profilen är ett JSON-strängformat som kan användas i Azure AD-katalogtjänster.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **nyckel**: 12334
    - **identitetProvider**: Facebook.com
- Utdataanspråk:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Lägger `AlternativeSecurityId` till `alternativeSecurityIdCollection` ett anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | objekt | sträng | Den ClaimType som ska läggas till i utdataanspråket. |
| InputClaim | samling | alternativeSecurityIdCollection | De ClaimTypes som används av anspråksomvandlingen om de är tillgängliga i principen. Om det tillhandahålls lägger `item` anspråksomvandlingen till i slutet av samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | De ClaimTypes som produceras efter att den här ClaimsTransformation har anropats. Den nya samlingen som innehåller både `collection` `item`objekt från indata och . |

I följande exempel länkas en ny social identitet till ett befintligt konto. Så här länkar du en ny social identitet:
1. I de tekniska profilerna **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** skriver användarens **alternativeSecurityIds-anspråk.**
1. Be användaren att logga in med en av de identitetsleverantörer som inte är associerade med den här användaren.
1. Med hjälp av transformeringen av **CreateAlternativeSecurityId-anspråk** skapar du en ny **alternativSecurityId-anspråkstyp** med ett namn på`AlternativeSecurityId2`
1. Anropa **anspråkstransformeringen AddItemToAlternativeSecurityIdCollection** för att lägga till **alternativesecurityId2-anspråket** i det befintliga **AlternativeSecurityIds-anspråket.**
1. Bevara **alternativeSecurityIds-anspråk** på användarkontot

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **punkt**: { "emittent": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **samling**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Utdataanspråk:
    - **samling**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Returnerar lista över utfärdare från **alternativeSecurityIdCollection-anspråket** till ett nytt **stringCollection-anspråk.**

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Den ClaimType som ska användas för att hämta listan över identitetsleverantörer (utfärdare). |
| OutputClaim | identitetProvidersSamla | strängInsamling | De ClaimTypes som produceras efter att den här ClaimsTransformation har anropats. Lista över identitetsleverantörer som associerar med det alternativa indataanspråket För säkerhetsinmatning |

Följande anspråksomvandling läser **användarens alternativeSecurityIds-anspråk** och extraherar listan över identitetsprovidernamn som är associerade med kontot. Använd **utdataidentitetProvidersCollection** för att visa användaren listan över identitetsleverantörer som är associerade med kontot. Eller filtrera listan över identitetsleverantörer på urvalssidan för **identitetsproviders.** Så kan användaren välja att länka ny social identitet som inte redan är associerad med kontot.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Ingående anspråk:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Utdataanspråk:
    - **identitetProvidersSamverkan**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>Ta bortAlternativeSecurityIdByIdentityProvider

Tar bort en **AlternativeSecurityId** från ett **alternativeSecurityIdCollection-anspråk.**

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identitetProvider | sträng | Den ClaimType som innehåller identitetsproviderns namn som ska tas bort från samlingen. |
| InputClaim | samling | alternativeSecurityIdCollection | De ClaimTypes som används av anspråksomvandlingen. Anspråksomvandlingen tar bort identitetenProvider från samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | De ClaimTypes som produceras efter att den här ClaimsTransformation har anropats. Den nya samlingen, efter identitetprovider bort från samlingen. |

I följande exempel tas en av den sociala identiteten bort från ett befintligt konto. Så här tar du bort länken till en social identitet:
1. I de tekniska profilerna **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** skriver användarens **alternativeSecurityIds-anspråk.**
2. Be användaren att välja vilket socialt konto som ska tas bort från de identitetsleverantörer som är associerade med den här användaren.
3. Anropa en teknisk profil för anspråksomvandling som anropar anspråksomvandlingen **RemoveAlternativeSecurityIdByIdentityProvider,** som tog bort den valda sociala identiteten med hjälp av identitetsproviderns namn.
4. Spara **alternativeSecurityIds-anspråk** på användarkontot.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **identitetProvider**: facebook.com
    - **samling**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Utdataanspråk:
    - **samling**: [ { "emittent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
