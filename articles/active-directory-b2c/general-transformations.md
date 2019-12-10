---
title: Exempel på generella anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Exempel på generella anspråk för omvandlings IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948911"
---
# <a name="general-claims-transformations"></a>Allmänna anspråks omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder allmänna anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Kontrollerar om **inputClaim** finns eller inte och anger **outputClaim** till true eller false.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Alla | Det ingångs anspråk vars existens måste verifieras. |
| OutputClaim | outputClaim | boolesk | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

Använd den här anspråks omvandlingen för att kontrol lera om det finns ett anspråk eller innehåller något värde. Returvärdet är ett booleskt värde som anger om anspråk finns. Följande exempel kontrollerar om e-postadressen finns.

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

- Inmatade anspråk:
  - **inputClaim**: someone@contoso.com
- Utgående anspråk:
  - **outputClaim**: sant

## <a name="hash"></a>Hash

Hash-värde för den angivna oformaterade texten med salt och en hemlighet. Den hash-algoritm som används är SHA-256.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | överför | sträng | Det inmatade anspråk som ska krypteras |
| InputClaim | våt | sträng | Salt parameter. Du kan skapa ett slumpmässigt värde med hjälp av `CreateRandomString` Claims-omvandling. |
| InputParameter | randomizerSecret | sträng | Pekar på en befintlig Azure AD B2C- **princip nyckel**. Om du vill skapa en ny princip nyckel: i Azure AD B2C klient, under **Hantera**, väljer du **Identity Experience Framework**. Välj **princip nycklar** för att visa de nycklar som är tillgängliga i din klient organisation. Välj **Lägg till**. För **alternativ**väljer du **manuell**. Ange ett namn (prefixet *B2C_1A_* kan läggas till automatiskt.). I rutan **hemlig** text anger du vilken hemlighet du vill använda, till exempel 1234567890. För **nyckel användning**väljer du **signatur**. Välj **Skapa**. |
| OutputClaim | hash | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. Anspråket som kon figurer ATS i `plaintext` inputClaim. |

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

- Inmatade anspråk:
  - **oformaterad text**: MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Utgående anspråk:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
