---
title: Allmänna anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Allmänna anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4e28dff6235e869c9275a8b0ba8d80252a9ea792
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167384"
---
# <a name="general-claims-transformations"></a>Allmän anspråksomvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda allmänna anspråksomvandlingar av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Kontrollerar om den **inputClaim** finns eller inte och anger **outputClaim** till true eller false i enlighet med detta.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Alla | Det inkommande anspråket vars förekomst behöver verifieras. |
| OutputClaim | outputClaim | boolesk | ClaimType som skapas när den här ClaimsTransformation har anropats. |

Använd detta omvandling för att kontrollera om ett anspråk finns eller innehåller ett värde av anspråk. Returvärdet är ett booleskt värde som anger om anspråket finns. Följande exempel kontrollerar om e-postadressen finns.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **inputClaim**: someone@contoso.com
- Utgående anspråk: 
    - **outputClaim**: true

## <a name="hash"></a>Hash

Hash-den angivna oformaterad text med hjälp av saltet och en hemlighet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | sträng | Inkommande anspråk som ska krypteras |
| InputClaim | salt | sträng | Parametern salt. Du kan skapa ett slumpmässigt värde, med hjälp av `CreateRandomString` omvandling av anspråk. |
| InputParameter | randomizerSecret | sträng | Pekar på en befintlig Azure AD B2C **Principnycklar**. Skapa ett nytt lösenord: I din Azure AD B2C-klient väljer **B2C-Inställningar > Identitetsramverk**. Välj **Principnycklar** att visa de nycklar som är tillgängliga i din klient. Välj **Lägg till**. För **alternativ**väljer **manuell**. Ange ett namn (prefixet B2C_1A_ kan läggas till automatiskt.). I rutan hemliga anger du eventuella hemlighet som du vill använda, till exempel 1234567890. Nyckelanvändning, Välj **hemlighet**. Välj **Skapa**. |
| OutputClaim | Hash | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats. Det anspråk som konfigurerats i den `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **plaintext**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Utgående anspråk: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



