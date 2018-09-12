---
title: Heltal anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Heltal anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381715"
---
# <a name="integer-claims-transformations"></a>Heltal anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda anspråksomvandlingar heltal av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Konverterar en lång datatyp till datatypen string.

| Objekt | TransformationClaimType | Datatyp | OBS! |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | lång | ClaimType att konvertera till en sträng. |
| outputClaim | outputClaim | sträng | ClaimType som skapas när den här ClaimsTransformation har anropats. |

I det här exemplet på `numericUserId` anspråk med en värdetyp långa konverteras till en `UserId` anspråk med en värdetyp av strängen.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim**: 12334 (long)
- Utgående anspråk: 
    - **outputClaim**: ”12334” (sträng)

