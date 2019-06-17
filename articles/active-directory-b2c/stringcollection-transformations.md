---
title: StringCollection anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: StringCollection anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98453daeb34d093b49cdcc636f68c3d7ae017126
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512443"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda anspråksomvandlingar för sträng samling av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Lägger till ett sträng-anspråk till ett nytt stringCollection anspråk. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Objekt | string | ClaimType som ska läggas till utdata-anspråket. |
| InputClaim | Samling | stringCollection | [Valfritt] Om anges anspråkstransformering kopieras objekt från den här samlingen och lägger till objektet i slutet av samlingen utdataanspråket. |
| OutputClaim | Samling | stringCollection | ClaimTypes som genereras när den här ClaimsTransformation har anropats. |

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
  - **item**: "admin@contoso.com"
- Utgående anspråk: 
  - **samlingen**: [”someone@outlook.com” ”,admin@contoso.com”]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Lägger till en strängparameter till ett nytt stringCollection anspråk. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Samling | stringCollection | [Valfritt] Om anges anspråkstransformering kopieras objekt från den här samlingen och lägger till objektet i slutet av samlingen utdataanspråket. |
| InputParameter | Objekt | string | Värdet som ska läggas till utdata-anspråket. |
| OutputClaim | Samling | stringCollection | ClaimTypes som skapas när den här ClaimsTransformation har anropats. |

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
  - **item**: "admin@contoso.com"
- Utgående anspråk:
  - **samlingen**: [”someone@outlook.com” ”,admin@contoso.com”]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hämtar det första objektet från mängden strängen. 

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Samling | stringCollection | ClaimTypes som används av anspråkstransformering för att hämta objektet. |
| OutputClaim | extractedItem | string | ClaimTypes som genereras när den här ClaimsTransformation har anropats. Det första objektet i samlingen. |

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

