---
title: Socialt konto anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Socialt konto anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53608654392d7efb73b6dadac14f01a94bb035a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360375"
---
# <a name="social-accounts-claims-transformations"></a>Anspråksomvandlingar för konton i sociala medier

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory (Azure AD) B2C identiteter för socialt konto lagras i en `userIdentities` attribut för en **alternativeSecurityIdCollection** Anspråkstypen. Varje objekt i den **alternativeSecurityIdCollection** anger utfärdaren (namn på identitetsprovider, till exempel facebook.com) och `issuerUserId`, som är unikt användar-ID för utgivaren.

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

Den här artikeln innehåller exempel för att använda anspråksomvandlingar för socialt konto av Identitetsramverk schemat i Azure AD B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Skapar en JSON-representation av användarens alternativeSecurityId egenskap som kan användas i anrop till Azure Active Directory. Mer information finns i [Alternativesecurityids schemat](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | string | ClaimType som anger den unika användaridentifierare som används av den sociala identitetsprovidern. |
| InputClaim | identityProvider | string | ClaimType som anger till exempel facebook.com providernamn socialt konto identitet. |
| OutputClaim | alternativeSecurityId | string | ClaimType som skapas när ClaimsTransformation har anropats. Innehåller information om identiteten för en användare med sociala kontot. Den **utfärdare** är värdet för den `identityProvider` anspråk. Den **issuerUserId** är värdet för den `key` anspråk i base64-format. |

Använd detta anspråk omvandlingen att generera en `alternativeSecurityId` ClaimType. Den används av alla sociala providern tekniska profiler, till exempel `Facebook-OAUTH`. Följande anspråkstransformering tar emot det sociala konto användar-ID och namnet på identitetsprovider. Utdata från den här tekniska profilen är en JSON-sträng-format som kan användas i Azure AD directory services.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **nyckel**: 12334
    - **identityProvider**: Facebook.com
- Utgående anspråk:
    - **alternativeSecurityId**: {”utfärdar”: ”facebook.com”, ”issuerUserId”: "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Lägger till en `AlternativeSecurityId` till en `alternativeSecurityIdCollection` anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Objekt | string | ClaimType som ska läggas till utdata-anspråket. |
| InputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som används av anspråkstransformering om det är tillgängligt i principen. Om anspråkstransformering lägger till den `item` i slutet av samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som genereras när den här ClaimsTransformation har anropats. Den nya samlingen som innehåller båda objekten från indata `collection` och `item`. |

I följande exempel länkas en ny sociala identitet med ett befintligt konto. Länka en ny sociala identitet:
1. I den **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** tekniska profiler utdata användarens **alternativeSecurityIds** anspråk.
1. Be användaren att logga in med något av de identitetsprovidrar som inte är associerade med den här användaren.
1. Med hjälp av den **CreateAlternativeSecurityId** anspråk omvandling, skapa en ny **alternativeSecurityId** Anspråkstyp med namnet `AlternativeSecurityId2`
1. Anropa den **AddItemToAlternativeSecurityIdCollection** anspråk omvandlingen att lägga till den **AlternativeSecurityId2** anspråk i den befintliga **AlternativeSecurityIds** anspråk.
1. Spara den **alternativeSecurityIds** anspråk till användarkontot

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

- Inkommande anspråk:
    - **objektet**: {”utfärdar”: ”facebook.com”, ”issuerUserId”: "MTIzNDU=" }
    - **samlingen**: [{”utfärdar”: ”live.com”, ”issuerUserId”: "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Utgående anspråk:
    - **samlingen**: [{”utfärdar”: ”live.com”, ”issuerUserId”: "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Returnerar lista över certifikatutfärdare från den **alternativeSecurityIdCollection** anspråk till en ny **stringCollection** anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | ClaimType som används för att hämta en lista över identitetsleverantörer (utfärdare). |
| OutputClaim | identityProvidersCollection | stringCollection | ClaimTypes som genereras när den här ClaimsTransformation har anropats. Lista över identitetsprovidrar som associeras med det inkommande anspråket alternativeSecurityIdCollection |

Följande anspråkstransformering läser användaren **alternativeSecurityIds** anspråk och extraherar listan över identitet providernamn kopplade till kontot. Använda utdata **identityProvidersCollection** ska visas för användaren lista över identitetsleverantörer som är kopplade till kontot. Eller filtrera listan över identitetsleverantörer baserat på utdata på sidan identitet providern val av **identityProvidersCollection** anspråk. Så att användaren kan välja för att länka ny sociala identitet som inte redan är associerad med kontot.

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

- Inkommande anspråk:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Utgående anspråk:
    - **identityProvidersCollection**: [”facebook.com”, ”google.com”]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Tar bort en **AlternativeSecurityId** från en **alternativeSecurityIdCollection** anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | ClaimType som innehåller namn på identitetsprovider som ska tas bort från samlingen. |
| InputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som används av anspråkstransformering. Anspråkstransformering tar bort Identityprovidern från samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som genereras när den här ClaimsTransformation har anropats. Den nya samlingen, när Identityprovidern tagits bort från samlingen. |

I följande exempel tar bort länkar för en av sociala med ett befintligt konto. Att Avlänka en sociala identitet:
1. I den **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** tekniska profiler utdata användarens **alternativeSecurityIds** anspråk.
2. Be användaren att välja vilka sociala konto för att ta bort från listan identitetsprovidrarna som är associerade med den här användaren.
3. Anropa ett anspråk omvandling tekniska profilen som anropar den **RemoveAlternativeSecurityIdByIdentityProvider** omvandling som har tagit bort det valda sociala identitet, med hjälp av namn på identitetsprovider av anspråk.
4. Spara den **alternativeSecurityIds** anspråk till användarkontot.

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

- Inkommande anspråk:
    - **identityProvider**: facebook.com
    - **samlingen**: [{”utfärdar”: ”live.com”, ”issuerUserId”: "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Utgående anspråk:
    - **samlingen**: [{”utfärdar”: ”live.com”, ”issuerUserId”: "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
