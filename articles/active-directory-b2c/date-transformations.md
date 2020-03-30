---
title: Exempel på omvandling av datumanspråk för anpassade principer
description: Exempel på datumanspråksomvandling för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c02ac9392d6f3f95deef38ff86250e96dfb76d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476696"
---
# <a name="date-claims-transformations"></a>Omvandling av datum för anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder datumansningsomvandlingarna av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Kontrollerar att ett datum- och tidsanspråk (strängdatatyp) är senare än ett andra datum- och tidsanspråk (strängdatatyp) och genererar ett undantag.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | sträng | Första fordrans typ, som bör vara senare än det andra kravet. |
| InputClaim | rightOperand | sträng | Andra fordran typ, som bör vara tidigare än den första fordran. |
| InputParameter | AssertIfEqualTo | boolean | Anger om det här påståendet ska passera om den vänstra operaden är lika med den högra operaden. |
| InputParameter | BekräftaIfRightOperandIsNotPresent | boolean | Anger om det här påståendet ska passera om rätt operand saknas. |
| InputParameter | TreatAsEqualIfWithinMillseconds TreatAsEqualIfWithinMillseconds TreatAsEqualIfWithinMillseconds TreatA | int | Anger antalet millisekunder som ska tillåta mellan de två datumtiderna att betrakta tiderna som lika (till exempel för att ta hänsyn till klocksnedställning). |

**AssertDateTimeIsGreaterThan-anspråksomvandlingen** utförs alltid från en [teknisk valideringsprofil](validation-technical-profile.md) som anropas av en [självpåförd teknisk profil](self-asserted-technical-profile.md). **DateTimeGreaterT** självpåförda tekniska profilmetadata styr felmeddelandet som den tekniska profilen presenterar för användaren. Felmeddelandena kan [lokaliseras](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual utförande](./media/date-transformations/assert-execution.png)

I följande exempel `currentDateTime` jämförs `approvedDateTime` anspråket med anspråket. Ett fel genereras `currentDateTime` om `approvedDateTime`det är senare än . Omformningen behandlar värden som lika om de är inom 5 minuter (30000 millisekunder) skillnad.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Den `login-NonInteractive` tekniska profilen `AssertApprovedDateTimeLaterThanCurrentDateTime` för validering anropar anspråksomvandlingen.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självsäkra tekniska profilen anropar den tekniska profilen för **valideringslogisan-NonInteractive.**

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **leftOperand**: 2020-03-01T15:00:00.0000000Z
    - **rightOperand**: 2020-03-01T14:00:00.0000000Z
- Resultat: Fel uppstod

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konverterar en **datum** claimtype till en **DateTime** ClaimType. Anspråksomvandlingen konverterar tidsformatet och lägger till 12:00:00 till datumet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | Den ClaimType som ska konverteras. |
| OutputClaim | outputClaim | Datetime | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

I följande exempel visas konverteringen `dateOfBirth` av anspråket (datumdatatyp) till ett annat anspråk `dateOfBirthWithTime` (dateTime-datatyp).

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputClaim**: 2020-15-03
- Utdataanspråk:
    - **outputClaim**: 2020-15-03T00:00:00.0000000Z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Konverterar en **DateTime** ClaimType till ett **datum** ClaimType. Anspråksomvandlingen tar bort tidsformatet från datumet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Datetime | Den ClaimType som ska konverteras. |
| OutputClaim | outputClaim | date | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

I följande exempel visas konverteringen `systemDateTime` av anspråket (dateTime-datatyp) till ett annat anspråk `systemDate` (datumdatatyp).

```XML
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **inputClaim**: 2020-15-03T11:34:22.0000000Z
- Utdataanspråk:
  - **outputClaim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Hämta aktuellt UTC-datum och -tid och lägg till värdet i en ClaimType.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | Datetime | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

* Utdataanspråk:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000Z

## <a name="datetimecomparison"></a>DatumTidsfördämning

Ta reda på om ett dateTime är senare, tidigare eller lika med ett annat. Resultatet är en ny booleska ClaimType `true` boolean med värdet eller `false`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datetime | Det första datumetTid för att jämföra om det är tidigare eller senare än det andra dateTime. Null-värdet genererar ett undantag. |
| InputClaim | secondDateTime | Datetime | Det andra datumetTid för att jämföra om det är tidigare eller senare än det första dateTime. Null-värde behandlas som aktuell datetTime. |
| InputParameter | operator | sträng | Ett av följande värden: samma, senare än eller tidigare än. |
| InputParameter | timeSpanInSeconds | int | Lägg till tidsspannet till den första datetime. |
| OutputClaim | resultat | boolean | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. |

Använd den här anspråksomvandlingen för att avgöra om två ClaimTypes är lika, senare eller tidigare än varandra. Du kan till exempel lagra den senaste gången en användare accepterade dina användarvillkor. Efter 3 månader kan du be användaren att komma åt TOS igen.
Om du vill köra anspråksomvandlingen måste du först hämta aktuellt dateTime och även den senaste gången användaren accepterar tos-systemet.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **firstDateTime**: 2020-01-01T00:00:00.100000Z
    - **secondDateTime**: 2020-04-01T00:00:00.100000Z
- Indataparametrar:
    - **operatör:** senare än
    - **timeSpanInSeconds**: 7776000 (90 dagar)
- Utdataanspråk:
    - **resultat**: sant
