---
title: Exempel på StringCollection-anspråk för identitets upplevelse Framework-schemat för Azure Active Directory B2C | Microsoft Docs
description: Exempel på StringCollection-anspråk för identitets upplevelse Framework-schemat för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9add75b8922fe958fc348fb2a6dd48a7b300eade
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063316"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du kan använda anspråks omvandlingar för identitets samling i ett Ramverks schema för identitets miljö i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Lägger till ett sträng anspråk till ett nytt stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | sträng | Den ClaimType som ska läggas till i utgående anspråk. |
| InputClaim | samling | stringCollection | Valfritt Om det här alternativet anges kopierar anspråks omvandlingen objekten från den här samlingen och lägger till objektet i slutet av utmatnings samlingens anspråk. |
| OutputClaim | samling | stringCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. |

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
| InputClaim | samling | stringCollection | Valfritt Om det här alternativet anges kopierar anspråks omvandlingen objekten från den här samlingen och lägger till objektet i slutet av utmatnings samlingens anspråk. |
| InputParameter | item | sträng | Det värde som ska läggas till i utgående anspråk. |
| OutputClaim | samling | stringCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. |

Använd den här anspråks omvandlingen för att lägga till ett sträng värde till en ny eller befintlig stringCollection. I följande exempel läggs en konstant e-postadressadmin@contoso.com() till i **otherMails** -anspråket.

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
| InputClaim | samling | stringCollection | ClaimTypes som används av anspråks omvandlingen för att hämta objektet. |
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

