---
title: Transformerings exempel för StringCollection-anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för StringCollection-anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982304"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du kan använda anspråks omvandlingar för identitets samling i ett Ramverks schema för identitets miljö i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Lägger till ett sträng anspråk till ett nytt stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | sträng | Den ClaimType som ska läggas till i utgående anspråk. |
| InputClaim | samlingen | stringCollection | Valfritt Om det här alternativet anges kopierar anspråks omvandlingen objekten från den här samlingen och lägger till objektet i slutet av utmatnings samlingens anspråk. |
| OutputClaim | samlingen | stringCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. |

Använd den här anspråks omvandlingen för att lägga till en sträng till en ny eller befintlig stringCollection. Den används ofta i en **AAD-UserWriteUsingAlternativeSecurityId** teknisk profil. Innan ett nytt socialt konto skapas läser **CreateOtherMailsFromEmail** Claims-omvandlingen claimType och lägger till värdet i **otherMails** -claimType.

Följande påstående-omvandling lägger till **e-** **otherMails** för claimType.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: ["someone@outlook.com"]
  - **objekt**: "admin@contoso.com"
- Utgående anspråk:
  - **samling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Lägger till en sträng parameter till ett nytt stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samlingen | stringCollection | Valfritt Om det här alternativet anges kopierar anspråks omvandlingen objekten från den här samlingen och lägger till objektet i slutet av utmatnings samlingens anspråk. |
| InputParameter | item | sträng | Det värde som ska läggas till i utgående anspråk. |
| OutputClaim | samlingen | stringCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. |

Använd den här anspråks omvandlingen för att lägga till ett sträng värde till en ny eller befintlig stringCollection. I följande exempel läggs en konstant e-postadress (admin@contoso.com) till i **otherMails** -anspråket.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: ["someone@outlook.com"]
- Indataparametrar
  - **objekt**: "admin@contoso.com"
- Utgående anspråk:
  - **samling**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hämtar det första objektet från den angivna sträng samlingen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samlingen | stringCollection | ClaimTypes som används av anspråks omvandlingen för att hämta objektet. |
| OutputClaim | extractedItem | sträng | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. Det första objektet i samlingen. |

I följande exempel läses **otherMails** -anspråket och det första objektet returneras till **e-** postanspråket.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: ["someone@outlook.com", "someone@contoso.com"]
- Utgående anspråk:
  - **extractedItem**: "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Kontrollerar om en StringCollection-anspråks typ innehåller ett-element

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Anspråks typen som ska genomsökas. |
|InputParameter|item|sträng|Det värde som ska genomsökas.|
|InputParameter|ignoreCase|sträng|Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs.|
| OutputClaim | outputClaim | boolean | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. En boolesk indikator om samlingen innehåller en sådan sträng |

Följande exempel kontrollerar om anspråks typen `roles` stringCollection innehåller värdet **administratör**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **inputClaim**: ["Reader", "author", "admin"]
- Indataparametrar:
    - **objekt**: "admin"
    - **ignoreCase**: "true"
- Utgående anspråk:
    - **outputClaim**: "true"


