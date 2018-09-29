---
title: StringCollection anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: StringCollection anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 924da8c92bad1c5345d5d1833723a0c4e8ad7d29
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432672"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda anspråksomvandlingar för sträng samling av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Lägger till ett sträng-anspråk till ett nytt stringCollection anspråk. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Objekt | sträng | ClaimType som ska läggas till utdata-anspråket. |
| InputClaim | samling | stringCollection | [Valfritt] Om anges anspråkstransformering kopieras objekt från den här samlingen och lägger till objektet i slutet av samlingen utdataanspråket. |
| outputClaim | samling | stringCollection | ClaimTypes som genereras när den här ClaimsTransformation har anropats. |

Använd detta omvandling för att lägga till en sträng till en ny eller befintlig stringCollection av anspråk. Används ofta i en **AAD-UserWriteUsingAlternativeSecurityId** tekniska profilen. Innan ett nytt sociala konto skapas **CreateOtherMailsFromEmail** anspråkstransformering läser ClaimType och lägger till värdet till den **otherMails** ClaimType. 

Följande anspråkstransformering lägger till den **e-post** ClaimType till **otherMails** ClaimType.

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

- Inkommande anspråk:
    - **samlingen**: [”someone@outlook.com”]
    - **objektet**”:admin@contoso.com”
- Utgående anspråk: 
    - **samlingen**: [”someone@outlook.com” ”,admin@contoso.com”]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Lägger till en strängparameter till ett nytt stringCollection anspråk. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | stringCollection | [Valfritt] Om anges anspråkstransformering kopieras objekt från den här samlingen och lägger till objektet i slutet av samlingen utdataanspråket. |
| Indataparametrar | Objekt | sträng | Värdet som ska läggas till utdata-anspråket. |
| outputClaim | samling | stringCollection | ClaimTypes som skapas när den här ClaimsTransformation har anropats. |

Använd detta omvandling för att lägga till ett strängvärde till en ny eller befintlig stringCollection av anspråk. I följande exempel läggs en konstant e-postadress (admin@contoso.com) till den **otherMails** anspråk. 

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

- Inkommande anspråk:
    - **samlingen**: [”someone@outlook.com”]
- Indataparametrar 
    - **objektet**”:admin@contoso.com”
- Utgående anspråk:
    - **samlingen**: [”someone@outlook.com” ”,admin@contoso.com”]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hämtar det första objektet från mängden strängen. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | stringCollection | ClaimTypes som används av anspråkstransformering för att hämta objektet. |
| outputClaim | extractedItem | sträng | ClaimTypes som genereras när den här ClaimsTransformation har anropats. Det första objektet i samlingen. |

I följande exempel läser den **otherMails** anspråk och returnerar det första objektet i den **e-post** anspråk. 

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

- Inkommande anspråk:
    - **samlingen**: [”someone@outlook.com” ”,someone@contoso.com”]
- Utgående anspråk: 
    - **extractedItem**”:someone@outlook.com”

