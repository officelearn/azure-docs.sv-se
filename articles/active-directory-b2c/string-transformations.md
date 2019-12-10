---
title: Transformerings exempel för sträng anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Exempel på sträng anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cb3b10ca67ab283b999e4fff8e3bb79ae3b59745
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950824"
---
# <a name="string-claims-transformations"></a>Transformeringar av sträng anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder sträng anspråks omvandlingar i ett Ramverks schema för identitets miljö i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Jämför två anspråk och Utlös ett undantag om de inte är lika enligt de angivna jämförelse inputClaim1, inputClaim2 och stringComparison.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Första anspråkets typ, som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråkets typ, som ska jämföras. |
| InputParameter | stringComparison | sträng | sträng jämförelse, ett av värdena: ordinal, OrdinalIgnoreCase. |

Omvandlingen av **AssertStringClaimsAreEqual** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationStringsAreNotEqual** -metadata för självkontrollerad teknisk profil styr det fel meddelande som visas för användaren.

![AssertStringClaimsAreEqual-körning](./media/string-transformations/assert-execution.png)

Du kan använda den här anspråks omvandlingen för att se till att två ClaimTypes har samma värde. Annars genereras ett fel meddelande. I följande exempel kontrol leras att **strongAuthenticationEmailAddress** claimType är lika med **e-** postclaimtype. Annars genereras ett fel meddelande.

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Den tekniska profilen för inloggning som inte är **interaktiv** anropar **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** Claims-transformeringen.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självkontrollerade tekniska profilen anropar verifierings **inloggningen-inaktiv** teknisk profil.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Indataparametrar:
  - **stringComparison**: ordinalIgnoreCase
- Resultat: fel utlöst

## <a name="changecase"></a>ChangeCase

Ändrar Skift läget för det angivna anspråket till lägre eller versaler beroende på operatören.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Den ClaimType som ska ändras. |
| InputParameter | toCase | sträng | Ett av följande värden: `LOWER` eller `UPPER`. |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att ändra sträng-ClaimType till lägre eller versaler.

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **e-post**: SomeOne@contoso.com
- Indataparametrar:
    - **toCase**: lägre
- Utgående anspråk:
  - **e-post**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Skapar ett sträng anspråk från den angivna Indataparametern i principen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
|----- | ----------------------- | --------- | ----- |
| InputParameter | värde | sträng | Strängen som ska anges |
| OutputClaim | createdClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, med det angivna värdet i indataparametern. |

Använd den här anspråks omvandlingen för att ange ett sträng värde för ClaimType.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Indataparameter:
    - **värde**: contoso-villkor för tjänsten...
- Utgående anspråk:
    - **createdClaim**: TOS-claimType innehåller "användar villkoren för contoso..." värde.

## <a name="compareclaims"></a>CompareClaims

Avgör om ett sträng anspråk är lika med ett annat. Resultatet är en ny boolesk ClaimType med värdet `true` eller `false`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Första anspråks typen, som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråks typen, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga värden: `EQUAL` eller `NOT EQUAL`. |
| InputParameter | ignoreCase | boolesk | Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs. |
| OutputClaim | outputClaim | boolesk | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att kontrol lera om ett anspråk är lika med ett annat anspråk. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **e-** postanspråket är lika med det **verifierade. e-** postanspråket.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Indataparametrar:
    - **operator**: inte lika med
    - **ignoreCase**: sant
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Anger om ett anspråks värde är lika med värdet för indataparametern.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Anspråkets typ, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga värden: `EQUAL` eller `NOT EQUAL`. |
| InputParameter | compareTo | sträng | sträng jämförelse, ett av värdena: ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolesk | Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs. |
| OutputClaim | outputClaim | boolesk | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Du kan använda den här anspråks omvandlingen för att kontrol lera om ett anspråk motsvarar ett värde som du har angett. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **termsOfUseConsentVersion** -anspråket är lika med `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel
- Inmatade anspråk:
    - **inputClaim1**: v1
- Indataparametrar:
    - **compareTo**: v1
    - **operator**: lika med
    - **ignoreCase**: sant
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="createrandomstring"></a>CreateRandomString

Skapar en slumpmässig sträng med slump tals generatorn. Om slump tals generatorn är av typen `integer`kan du ange en start parameter och ett högsta antal. Med en valfri sträng format parameter kan utdata formateras med den, och en valfri base64-parameter anger om utdata är Base64-kodad randomGeneratorType [GUID, Integer] outputClaim (sträng).

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | sträng | Anger det slumpmässiga värde som ska genereras, `GUID` (globalt unikt ID) eller `INTEGER` (ett tal). |
| InputParameter | stringFormat | sträng | Valfritt Formatera det slumpmässiga värdet. |
| InputParameter | base64 | boolesk | Valfritt Omvandla det slumpmässiga värdet till base64. Om sträng format används kodas värdet efter sträng format till base64. |
| InputParameter | maximumNumber | int | Valfritt Endast för `INTEGER` randomGeneratorType. Ange det maximala antalet. |
| InputParameter | dirigeringsrouter  | int | Valfritt Endast för `INTEGER` randomGeneratorType. Ange start värde för det slumpmässiga värdet. Obs! samma utsäde ger samma sekvens med slumpmässiga tal. |
| OutputClaim | outputClaim | sträng | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats. Det slumpmässiga värdet. |

I följande exempel skapas ett globalt unikt ID. Den här anspråks omvandlingen används för att skapa det slumpmässiga UPN-namnet (användar Principens namn).

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Indataparametrar:
    - **randomGeneratorType**: GUID
- Utgående anspråk:
    - **outputClaim**: bc8bedd2-AAA3-411E-bdee-2f1810b73dfc

I följande exempel genereras ett heltals slump värde mellan 0 och 1000. Värdet är formaterat till OTP_ {slump värde}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Indataparametrar:
    - **randomGeneratorType**: heltal
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: falskt
- Utgående anspråk:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formatera ett anspråk enligt den angivna format strängen. Den här omvandlingen använder C# metoden `String.Format`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng |ClaimType som fungerar som sträng format {0} parameter. |
| InputParameter | stringFormat | sträng | Sträng formatet, inklusive parametern {0}. |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att formatera en sträng med en parameter {0}. I följande exempel skapas ett **userPrincipalName**. Alla tekniska profiler för sociala identiteter, till exempel `Facebook-OAUTH` anropar **CreateUserPrincipalName** för att generera ett **userPrincipalName**.

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Indataparametrar:
    - **stringFormat**: cpim_{0}@ {RelyingPartyTenantId}
- Utgående anspråk:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatera två anspråk enligt den angivna format strängen. Den här omvandlingen använder C# metoden **String. format** .

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng | ClaimType som fungerar som sträng format {0} parameter. |
| InputClaim | inputClaim | sträng | ClaimType som fungerar som sträng format {1} parameter. |
| InputParameter | stringFormat | sträng | Sträng formatet, inklusive parametrarna {0} och {1}. |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att formatera en sträng med två parametrar, {0} och {1}. I följande exempel skapas ett **DisplayName** med det angivna formatet:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: Johan
    - **inputClaim2**: Fernando
- Indataparametrar:
    - **stringFormat**: {0} {1}
- Utgående anspråk:
    - **outputClaim**: Johan Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Söker efter ett objekt från en **begränsnings** samling för anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | sträng | Det anspråk som innehåller den text som ska slås upp i **restrictionValueClaim** -anspråk med **begränsnings** samlingen.  |
| OutputClaim | restrictionValueClaim | sträng | Det anspråk som innehåller **begränsnings** samlingen. När anspråks omvandlingen har anropats innehåller värdet för det här anspråket värdet för det valda objektet. |

Följande exempel söker efter beskrivningen av fel meddelandet baserat på fel nyckeln. **ResponseMsg** -anspråket innehåller en samling fel meddelanden som ska visas för slutanvändaren eller som skickas till den förlitande parten.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Transformationen Claims söker efter texten i objektet och returnerar dess värde. Om begränsningen lokaliseras med hjälp av `<LocalizedCollection>`, returnerar omvandling av anspråk det lokaliserade värdet.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **mapFromClaim**: B2C_V1_90001
- Utgående anspråk:
    - **restrictionValueClaim**: du kan inte logga in eftersom du är en mindre.

## <a name="lookupvalue"></a>LookupValue

Leta upp ett anspråks värde från en lista med värden baserat på värdet för ett annat anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | sträng | Det anspråk som innehåller uppslags värdet |
| InputParameter | |sträng | Insamling av indataparametrar. |
| InputParameter | errorOnFailedLookup | boolesk | Kontrollerar om ett fel returneras när det inte finns någon matchande sökning. |
| OutputClaim | inputParameterId | sträng | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats. Värdet för det matchande ID: t. |

I följande exempel söker du efter domän namnet i en av indataparametrar-samlingarna. Omvandlingen av anspråk söker upp domän namnet i identifieraren och returnerar dess värde (ett program-ID).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputParameterId**: test.com
- Indataparametrar:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: falskt
- Utgående anspråk:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Rensa värdet för ett angivet anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | sträng | Anspråkets värde ska vara NULL. |

Använd den här anspråks omvandlingen för att ta bort onödiga data från egenskaps uppsättningen anspråk. Därför kommer sessionens cookie att vara mindre. I följande exempel tas värdet för `TermsOfService` anspråks typen bort.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **outputClaim**: Välkommen till contoso-appen. Om du fortsätter att söka efter och använda den här webbplatsen, samtycker du till att följa och vara bindande för följande villkor...
- Utgående anspråk:
    - **outputClaim**: null

## <a name="parsedomain"></a>ParseDomain

Hämtar domän delen av en e-postadress.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | sträng | Den ClaimType som innehåller e-postadressen. |
| OutputClaim | domän | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats – domänen. |

Använd den här anspråks omvandlingen för att parsa domän namnet efter användarens @-symbol. Detta kan vara till hjälp när du tar bort personligt identifierbar information (PII) från gransknings data. Följande anspråks omvandling visar hur du kan parsa domän namnet från ett **e-** postanspråk.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **EmailAddress**: joe@outlook.com
- Utgående anspråk:
    - **domän**: Outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontrollerar att en sträng anspråks-och `matchTo` indataparameter är lika, och anger de utgående anspråken med det värde som finns i `stringMatchMsg` och `stringMatchMsgCode` indataparametrar, tillsammans med jämför resultat för utgående anspråk, som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som ska jämföras. |
| InputParameter | matchTo | sträng | Strängen som ska jämföras med `inputClaim`. |
| InputParameter | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | sträng | Det första värdet som ska anges om strängarna är lika. |
| InputParameter | stringMatchMsgCode | sträng | Det andra värdet som ska anges om strängarna är lika. |
| OutputClaim | outputClaim1 | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `stringMatchMsg` indataparameter. |
| OutputClaim | outputClaim2 | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `stringMatchMsgCode` indataparameter. |
| OutputClaim | stringCompareResultClaim | boolesk | Anspråks typen jämför resultat, som ska anges som `true` eller `false` baserat på jämförelse resultatet. |

Du kan använda den här anspråks omvandlingen för att kontrol lera om ett anspråk motsvarar det värde som du har angett. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **termsOfUseConsentVersion** -anspråket är lika med `v1`. Om ja, ändra värdet till `v2`.

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: v1
- Indataparametrar:
    - **matchTo**: v1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: TOS uppgraderas till v2
- Utgående anspråk:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: TOS uppgraderas till v2
    - **stringCompareResultClaim**: sant

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontrollerar att en sträng anspråks-och `matchTo` indataparameter är lika, och anger de utgående anspråken med det värde som finns i `outputClaimIfMatched` indataparameter, tillsammans med jämförelse av utdata-anspråk, som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | sträng | Anspråks typen, som ska jämföras. |
| InputParameter | matchTo | sträng | Strängen som ska jämföras med inputClaim. |
| InputParameter | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | sträng | Värdet som ska anges om strängarna är lika. |
| OutputClaim | outputClaim | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `outputClaimIfMatched` indataparameter. Eller null, om strängarna inte matchar. |
| OutputClaim | stringCompareResultClaim | boolesk | Anspråks typen jämför resultat, som ska anges som `true` eller `false` baserat på jämförelse resultatet. |

Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **ageGroup** -anspråk är lika med `Minor`. Om ja, returnera värdet till `B2C_V1_90001`.

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **claimToMatch**: mindre
- Indataparametrar:
    - **matchTo**: mindre
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Utgående anspråk:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: sant

