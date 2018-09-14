---
title: Datum-anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Datum anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ff96b9a63e7340788ef2474ce9934145c184e1e1
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542777"
---
# <a name="date-claims-transformations"></a>Datum anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda anspråk datumtransformeringar av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Kontrollerar att ett datum och tid anspråk (strängdatatypen) är större än ett andra datum och tid Anspråkstypen (sträng data) och utlöser ett undantag.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | sträng | Typ första anspråk som ska vara större än det andra anspråket. |
| InputClaim | rightOperand | sträng | Typ av andra anspråk, vilket ska vara mindre än det första anspråket. |
| Indataparametrar | AssertIfEqualTo | boolesk | Anger om kontrollen ska skicka om den vänstra operanden är lika med den högra operanden. |
| Indataparametrar | AssertIfRightOperandIsNotPresent | boolesk | Anger om kontrollen ska skicka om högeroperanden saknas. |
| Indataparametrar | TreatAsEqualIfWithinMillseconds | int | Anger antalet millisekunder att tillåta mellan två datum-tid att tänka på tiderna som är lika med (till exempel till konto för klocka skeva). |

Den **AssertDateTimeIsGreaterThan** anspråkstransformering utförs alltid från en [teknisk verifieringsprofil](validation-technical-profile.md) som anropas av en [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md). Den **DateTimeGreaterThan** självkontrollerad tekniska profilens metadata styr det felmeddelande som den tekniska profilen som visas för användaren.

![AssertStringClaimsAreEqual körning](./media/date-transformations/assert-execution.png)

I följande exempel jämförs det `currentDateTime` anspråk med den `approvedDateTime` anspråk. Ett fel genereras om `currentDateTime` är större än `approvedDateTime`. Transformeringen behandlar värden som lika om ändringarna är inom 5 minuter (30000 millisekunder) skillnaden.

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

Den `login-NonInteractive` verifiering tekniska profilen anrop den `AssertApprovedDateTimeLaterThanCurrentDateTime` omvandling av anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den tekniska profilen självkontrollerad anropar verifieringen **inloggning utan interaktivitet** tekniska profilen.

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

- Inkommande anspråk:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Resultat: Fel som utlöstes


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Konverterar en **datum** ClaimType till en **DateTime** ClaimType. Anspråkstransformering konverterar en tid-format och lägger till 12:00:00 AM i datumet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | datum | ClaimType som ska konverteras. |
| outputClaim | outputClaim | Datum/tid | ClaimType som skapas när den här ClaimsTransformation har anropats. |

Exemplet nedan visar konvertering av anspråket `dateOfBirth` (datum datatyp) till en annan anspråk `dateOfBirthWithTime` (datatypen dateTime).

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

- Inkommande anspråk:
    - **inputClaim**: 2019-06-01
- Utgående anspråk:
    - **outputClaim**: 1559347200 (juni 1 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Hämta den aktuella UTC-datum och tid och lägga till värdet i en ClaimType.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | CURRENTDATETIMEN | Datum/tid | ClaimType som skapas när den här ClaimsTransformation har anropats. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

* Utgående anspråk:
    * **CURRENTDATETIMEN**: 1534418820 (augusti 16 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Avgöra om ett datum/tid är större, mindre eller lika med en annan. Resultatet är ett nytt booleskt ClaimType booleskt värde med ett värde för SANT eller FALSKT.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | Datum/tid | Den första datum/tid att jämföra. Null-värde genereras ett undantag. |
| InputClaim | secondDateTime | Datum/tid | Den andra datum/tid att slutföra. Null-värde behandlar som aktuella datetTime. |
| Indataparametrar | Operator | sträng | Något av följande värden: samma, senare än eller tidigare än. |
| Indataparametrar | timeSpanInSeconds | int | Lägg till timespan i första datum/tid. |
| outputClaim | Resultatet | boolesk | ClaimType som skapas när den här ClaimsTransformation har anropats. |

Använd detta anspråk omvandlingen att fastställa om två ClaimTypes är lika med, större eller mindre från varandra. Du kan till exempel lagra den senaste gången en användare som godkänt dina villkor av tjänster (TOS). När 3 månader kan du be användaren att få åtkomst till förklaringar igen.
Om du vill köra anspråksomvandling, måste du först hämta den aktuella datumet/tiden och senaste gången användaren godkänner även förklaringar.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="greater than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inkommande anspråk:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Indataparametrar:
    - **operatorn**: är större än
    - **timeSpanInSeconds**: 7776000 (90 dagar)
- Utgående anspråk: 
    - **resultatet**: true

