---
title: Datum anspråk omvandlings exempel för anpassade principer
description: Datum anspråk omvandlings exempel för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b831a3175e1dc8b19395d1c923b076ac9428690c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982916"
---
# <a name="date-claims-transformations"></a>Transformeringar av datum anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda datum anspråks omvandlingar av identitets Miljös schema i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Kontrollerar att ett datum-och tids anspråk (sträng data typ) är senare än ett andra datum-och tids anspråk (sträng data typ) och genererar ett undantag.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | sträng | Första anspråkets typ, vilket bör vara senare än det andra anspråket. |
| InputClaim | rightOperand | sträng | Andra anspråkets typ, som bör vara tidigare än det första anspråket. |
| InputParameter | AssertIfEqualTo | boolean | Anger om den här kontrollen ska passas om den vänstra operanden är lika med den högra operanden. |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | Anger om den här kontrollen ska passas om den högra operanden saknas. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Anger antalet millisekunder som ska tillåtas mellan de två datumen gånger för att överväga de tider som är lika med (till exempel för att ta hänsyn till klock skevning). |

Omvandlingen av **AssertDateTimeIsGreaterThan** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md). **DateTimeGreaterThan** -metadata för självkontrollerad teknisk profil styr det fel meddelande som den tekniska profilen presenterar för användaren.

![AssertStringClaimsAreEqual-körning](./media/date-transformations/assert-execution.png)

I följande exempel jämförs `currentDateTime`-anspråk med `approvedDateTime`-anspråket. Ett fel genereras om `currentDateTime` är senare än `approvedDateTime`. Omvandlingen behandlar värden på samma sätt som om de ligger inom 5 minuter (30000 millisekunder).

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

Den tekniska profilen för `login-NonInteractive` verifiering anropar transformeringen för `AssertApprovedDateTimeLaterThanCurrentDateTime`-anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självkontrollerade tekniska profilen anropar verifierings **inloggningen-inaktiv** teknisk profil.

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

- Inmatade anspråk:
    - **leftOperand**: 2018-10-01T15:00:00.0000000 z
    - **rightOperand**: 2018-10-01T14:00:00.0000000 z
- Resultat: fel utlöst

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konverterar en **datum** -claimType till en **datetime** -claimType. Omvandlingen av anspråk konverterar tids formatet och lägger till 12:00:00 AM till datumet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | Den ClaimType som ska konverteras. |
| OutputClaim | outputClaim | Datum/tid | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

I följande exempel demonstreras konverteringen av anspråks `dateOfBirth` (datum data typ) till ett annat anspråk `dateOfBirthWithTime` (dateTime-datatyp).

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

- Inmatade anspråk:
    - **inputClaim**: 2019-06-01
- Utgående anspråk:
    - **outputClaim**: 1559347200 (1 juni 2019 12:00:00 am)

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim 

Konverterar en **datetime** -claimType till en **datum** -claimType. Omvandlingen av anspråk tar bort tids formatet från datumet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Datum/tid | Den ClaimType som ska konverteras. |
| OutputClaim | outputClaim | date | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

Följande exempel visar konverteringen av anspråks `systemDateTime` (dateTime-datatyp) till ett annat anspråk `systemDate` (datum data typ).

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

- Inmatade anspråk:
  - **inputClaim**: 1559347200 (1 juni 2019 12:00:00 am)
- Utgående anspråk:
  - **outputClaim**: 2019-06-01

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Hämta aktuellt UTC-datum och-tid och Lägg till värdet i en ClaimType.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | Datum/tid | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

* Utgående anspråk:
    * **currentDateTime**: 1534418820 (16 augusti 2018 11:27:00 am)

## <a name="datetimecomparison"></a>DateTimeComparison

Avgör om en dateTime är senare, tidigare eller lika med ett annat. Resultatet är en ny boolesk ClaimType boolesk med värdet `true` eller `false`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datum/tid | Första datum/tid för att jämföra om det är tidigare eller senare än det andra dateTime-värdet. Null-värde genererar ett undantag. |
| InputClaim | secondDateTime | Datum/tid | Den andra datum/tid för att jämföra om den är tidigare eller senare än den första dateTime-tiden. Null-värde behandlas som aktuellt datetTime. |
| InputParameter | operator | sträng | Ett av följande värden: samma, senare än eller tidigare än. |
| InputParameter | timeSpanInSeconds | int | Lägg till TimeSpan i första DateTime. |
| OutputClaim | Resultatet | boolean | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

Använd den här anspråks omvandlingen för att avgöra om två ClaimTypes är lika, senare eller tidigare. Du kan till exempel lagra den senaste gången som en användare godkände dina användar villkor (TOS). Efter 3 månader kan du be användaren att komma åt TOS-filen igen.
Om du vill köra anspråks omvandlingen måste du först hämta aktuell dateTime och sedan den senaste gången användaren accepterar TOS.

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

- Inmatade anspråk:
    - **firstDateTime**: 2018-01-01T00:00:00.100000 z
    - **secondDateTime**: 2018-04-01T00:00:00.100000 z
- Indataparametrar:
    - **operator**: senare än
    - **timeSpanInSeconds**: 7776000 (90 dagar)
- Utgående anspråk:
    - **resultat**: true
