---
title: Telefonnummeranspråksomvandlingar i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad principreferens för omvandling av telefonnummeranspråk i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183947"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definiera omvandlingar av telefonnummeranspråk i Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller referenser och exempel för hur du använder telefonnummeranspråksomvandlingarna i Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information om anspråksomvandlingar i allmänhet finns i [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>KonverteraPhoneNumberClaimToString

Konverterar `phoneNumber` en datatyp `string` till en datatyp.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Den ClaimType som ska konverteras till en sträng. |
| OutputClaim | telefonNumberString | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

I det här exemplet konverteras mobilnumnumber-anspråket med en värdetyp av `phoneNumber` `string`till ett mobiltelefonanspråk med en värdetyp av .

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **phoneNumber**: +11234567890 (phoneNumber)
- Utdataanspråk:
  - **phoneNumberString**: +11234567890 (sträng)


## <a name="convertstringtophonenumberclaim"></a>KonverteraStringToPhoneNumberClaim

Den här anspråksomvandlingen validerar formatet på telefonnumret. Om den är i ett giltigt format ändrar du det till ett standardformat som används av Azure AD B2C. Om det angivna telefonnumret inte är i ett giltigt format returneras ett felmeddelande.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | telefonNumberString | sträng |  Strängen anspråk för telefonnumret. Telefonnumret måste vara i internationellt format, komplett med en ledande "+" och landskod. Om inmatningsanspråk `country` anges är telefonnumret i lokalt format (utan landskoden). |
| InputClaim | land | sträng | [Valfritt] Strängen anspråk för landskoden för telefonnumret i ISO3166-format (två bokstäver ISO-3166 landskod). |
| OutputClaim | outputClaim | phoneNumber | Resultatet av denna anspråk omvandling. |

**ConvertStringToPhoneNumberClaim** hävdar omvandlingen utförs alltid från en [teknisk valideringsprofil](validation-technical-profile.md) som anropas av en [självpåförd teknisk profil](self-asserted-technical-profile.md) eller [visningskontroll](display-controls.md). **UserMessageIfClaimsTransformationInvalidPhoneNumber** självsäkra tekniska profilmetadata styr felmeddelandet som presenteras för användaren.

![Diagram över sökväg för körning av felmeddelande](./media/phone-authentication/assert-execution.png)

Du kan använda den här anspråksomvandlingen för att säkerställa att det angivna stränganspråket är ett giltigt telefonnummer. Om inte, genereras ett felmeddelande. I följande exempel kontrolleras att **phoneString** ClaimType verkligen är ett giltigt telefonnummer och returnerar sedan telefonnumret i standardformatet Azure AD B2C. Annars genereras ett felmeddelande.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Den självpåsterade tekniska profilen som anropar den tekniska profilen för validering som innehåller den här anspråksomvandlingen kan definiera felmeddelandet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exempel 1

- Ingående anspråk:
  - **phoneNumberString**: 033 456-7890
  - **land**: DK
- Utdataanspråk:
  - **outputClaim**: +450334567890

### <a name="example-2"></a>Exempel 2

- Ingående anspråk:
  - **telefonNumberString**: +1 (123) 456-7890
- Utdataanspråk:
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>FåNationalNumberAndCountryCodeFromPhoneNumberString

Detta extraherar landskoden och det nationella numret från inmatningsanspråket och genererar eventuellt ett undantag om det angivna telefonnumret inte är giltigt.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | sträng | Strängen anspråk på telefonnumret. Telefonnumret måste vara i internationellt format, komplett med en ledande "+" och landskod. |
| InputParameter | throwExceptionOnFailure | boolean | [Valfritt] En parameter som anger om ett undantag genereras när telefonnumret inte är giltigt. Standardvärdet är falskt. |
| InputParameter | landCodeType (landCodeType) | sträng | [Valfritt] En parameter som anger typen av landskod i utdataanspråket. Tillgängliga värden är **CallingCode** (den internationella samtalskoden för ett land, till exempel +1) eller **ISO3166** (landskoden med två bokstäver i ISO-3166). |
| OutputClaim | nationellaNummer | sträng | Strängen anspråk på det nationella numret på telefonnumret. |
| OutputClaim | countryCode (landsKod) | sträng | Strängen anspråk för landskoden för telefonnumret. |


Om **getnationalnumberandcountryCodeFromPhoneNumberString** anspråk omvandlingen utförs från en [validering teknisk profil](validation-technical-profile.md) som anropas av en [självpåförd teknisk profil](self-asserted-technical-profile.md) eller en åtgärd display [kontroll](display-controls.md#display-control-actions), då **UserMessageIfPhoneNumberParseFailure** självpåförda tekniska profil metadata styr felmeddelandet som presenteras för användaren.

![Diagram över sökväg för körning av felmeddelande](./media/phone-authentication/assert-execution.png)

Du kan använda den här anspråksomvandlingen för att dela upp ett fullständigt telefonnummer i landskoden och det nationella numret. Om det angivna telefonnumret inte är giltigt kan du välja att skicka ett felmeddelande.

I följande exempel försöker du dela upp telefonnumret i nationellt nummer och landskod. Om telefonnumret är giltigt åsidosätts telefonnumret av det nationella numret. Om telefonnumret inte är giltigt genereras inte ett undantag och telefonnumret har fortfarande sitt ursprungliga värde.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Den självpåsterade tekniska profilen som anropar den tekniska profilen för validering som innehåller den här anspråksomvandlingen kan definiera felmeddelandet.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exempel 1

- Ingående anspråk:
  - **phoneNumber**: +49 (123) 456-7890
- Indataparametrar:
  - **throwExceptionOnFailure**: falskt
  - **countryCodeType**: ISO3166
- Utdataanspråk:
  - **medborgareNummer**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>Exempel 2

- Ingående anspråk:
  - **phoneNumber**: +49 (123) 456-7890
- Indataparametrar
  - **throwExceptionOnFailure**: falskt
  - **countryCodeType**: CallingCode
- Utdataanspråk:
  - **medborgareNummer**: 1234567890
  - **countryCode**: +49
