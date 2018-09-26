---
title: Sträng-anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C | Microsoft Docs
description: Sträng anspråk omvandling exempel för den identiteten upplevelse Framework Schema för Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b731b280b3e97076014f609571766a07a3dde1ea
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159898"
---
# <a name="string-claims-transformations"></a>Sträng anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel för att använda anspråksomvandlingar sträng av Identitetsramverk schemat i Azure Active Directory (Azure AD) B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Jämför två anspråk och genererar ett undantagsfel om de inte är lika med enligt angivna jämförelse inputClaim1, inputClaim2 och stringComparison.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Typ första anspråk som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråkets typ som ska jämföras. |
| Indataparametrar | stringComparison | sträng | sträng däremot ett av värdena: ordningstal, OrdinalIgnoreCase. |

Den **AssertStringClaimsAreEqual** anspråkstransformering utförs alltid från en [teknisk verifieringsprofil](validation-technical-profile.md) som anropas av en [lokal verifieringsvillkor tekniska profilen](self-asserted-technical-profile.md). Den **UserMessageIfClaimsTransformationStringsAreNotEqual** självkontrollerad tekniska profilens metadata styr det felmeddelande som visas för användaren.

![AssertStringClaimsAreEqual körning](./media/string-transformations/assert-execution.png)

Du kan använda detta anspråk omvandling kontrollera, två ClaimTypes har samma värde. Annars genereras ett felmeddelande. I följande exempel kontrollerar att den **strongAuthenticationEmailAddress** ClaimType är lika med **e-post** ClaimType. Annars genereras ett felmeddelande. 

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


Den **inloggning utan interaktivitet** verifiering tekniska profilen anrop den **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** omvandling av anspråk.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den tekniska profilen självkontrollerad anropar verifieringen **inloggning utan interaktivitet** tekniska profilen.

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

- Inkommande anspråk:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
 - Indataparametrar:
    - **stringComparison**: ordinalIgnoreCase
- Resultat: Fel som utlöstes

## <a name="changecase"></a>ChangeCase 

Ändrar fallet med det angivna anspråket till lägre eller versal beroende på vilken operator.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | ClaimType som ska ändras. |
| Indataparametrar | toCase | sträng | Något av följande värden: `LOWER` eller `UPPER`. |
| outputClaim | outputClaim | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats. |

Använd den här anspråksomvandling för att ändra en sträng ClaimType till lägre eller versal.  

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

- Inkommande anspråk:
    - **e-post**: SomeOne@contoso.com
- Indataparametrar:
    - **toCase**: lägre
- Utgående anspråk:
    - **e-post**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

Skapar ett sträng-anspråk från den tillhandahållna Indataparametern i principen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
|----- | ----------------------- | --------- | ----- |
| Indataparametrar | värde | sträng | Strängen som ska anges |
| outputClaim | createdClaim | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats med värdet som anges i indataparametern. |

Använd detta anspråk omvandlingen att ange en sträng ClaimType värde.

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

- Indataparametern:
    - **värdet**: Contoso användningsvillkor...
- Utgående anspråk:
    - **createdClaim**: The förklaringar ClaimType innehåller ”Contoso användningsvillkor...”-värde.

## <a name="compareclaims"></a>CompareClaims

Avgör om ett sträng-anspråk är lika med en annan. Resultatet är en ny booleskt ClaimType med värdet `true` eller `false`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Först Anspråkstyp, som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråk som ska jämföras. |
| Indataparametrar | Operator | sträng | Möjliga värden: `Equal` eller `Not Equal`. |
| Indataparametrar | ignoreCase | boolesk | Anger om den här jämförelsen bör Ignorera skiftläge med strängar som jämförs. |
| outputClaim | outputClaim | boolesk | ClaimType som skapas när detta omvandling av anspråk har anropats. |

Använd detta omvandling och kontrollera om ett anspråk är lika med en annan anspråk av anspråk. Till exempel följande anspråk omvandling kontrollerar om värdet för den **e-post** anspråk är lika med den **Verified.Email** anspråk.

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

- Inkommande anspråk:
    - **inputClaim1**: someone@contoso.com
    - **inputClaim2**: someone@outlook.com
- Indataparametrar:
    - **operatorn**: inte lika med
    - **ignoreCase**: true
- Utgående anspråk:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Anger om ett anspråksvärde är lika med värdet för indataparametern.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Anspråkets typ som ska jämföras. |
| Indataparametrar | Operator | sträng | Möjliga värden: `Equal` eller `Not Equal`. |
| Indataparametrar | compareTo | sträng | sträng däremot ett av värdena: ordningstal, OrdinalIgnoreCase. |
| Indataparametrar | ignoreCase | boolesk | Anger om den här jämförelsen bör Ignorera skiftläge med strängar som jämförs. |
| outputClaim | outputClaim | boolesk | ClaimType som skapas när detta omvandling av anspråk har anropats. |

Du kan använda detta anspråk omvandling för att kontrollera om ett anspråk är lika med ett värde som du angav. Till exempel följande anspråk omvandling kontrollerar om värdet för den **termsOfUseConsentVersion** anspråk är lika med `v1`.

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
- Inkommande anspråk:
    - **inputClaim1**: v1
- Indataparametrar:
    - **compareTo**: V1
    - **operatorn**: lika med 
    - **ignoreCase**: true
- Utgående anspråk:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Skapar en slumpmässig sträng med slumpmässig talgeneratorns startvärden. Om slumpmässiga talgeneratorns startvärden är av typen `integer`kan du också en seed-parameter och ett högsta tal kan anges. Formatparametern valfri sträng kan utdata ska vara formaterad med hjälp av den och en valfri base64-parameter som anger om utdata är base64-kodad randomGeneratorType [guid, heltal] outputClaim (sträng).

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| Indataparametrar | randomGeneratorType | sträng | Anger slumpmässigt värde som ska genereras `GUID` (globalt unikt ID) eller `integer` (ett tal). |
| Indataparametrar | stringFormat | sträng | [Valfritt] Formatera slumpmässigt värde. |
| Indataparametrar | Base64 | boolesk | [Valfritt] Konvertera slumpmässigt värde till base64. Om strängformat används kodas värdet efter strängformat till base64. |
| Indataparametrar | maximumNumber | int | [Valfritt] För `Integer` randomGeneratorType endast. Ange hur många maximute. |
| Indataparametrar | Startvärde  | int | [Valfritt] För `Integer` randomGeneratorType endast. Ange startvärdet för slumpmässigt värde. Obs: samma seed ger samma sekvens med slumptal. |
| outputClaim | outputClaim | sträng | ClaimTypes som skapas när detta omvandling av anspråk har anropats. Slumpmässigt värde. |

Följande exempel genererar ett globalt unikt ID. Detta anspråk omvandling används för att skapa slumpmässigt UPN (användarens huvudnamn).

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
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Följande exempel genererar ett slumpmässigt heltal mellan 0 och 1000. Värdet är formaterat till OTP_ {slumpmässigt värde}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="integer" />
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
    - **Base64**: false
- Utgående anspråk: 
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formatera ett anspråk enligt den angivna Formatsträngen. Den här omvandlingen använder C# `String.Format` metod.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |sträng |ClaimType som fungerar som strängformat {0} parametern. |
| Indataparametrar | stringFormat | sträng | Strängformat, inklusive den {0} parametern. |
| outputClaim | outputClaim | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats. |

Använd detta omvandling till format som alla strängar med en parameter av anspråk {0}. I följande exempel skapas en **userPrincipalName**. Alla sociala providern tekniska profiler, till exempel `Facebook-OAUTH` anrop den **CreateUserPrincipalName** att generera en **userPrincipalName**.   

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

- Inkommande anspråk:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Indataparametrar:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Utgående anspråk:
    - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatera två anspråk enligt den angivna Formatsträngen. Den här omvandlingen använder C# **String.Format** metod.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |sträng | ClaimType som fungerar som strängformat {0} parametern. |
| InputClaim | InputClaim | sträng | ClaimType som fungerar som strängformat {1} parametern. |
| Indataparametrar | stringFormat | sträng | Strängformat, inklusive den {0} och {1} parametrar. |
| outputClaim | outputClaim | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats. |

Använd detta anspråk omvandling till format som alla strängar med två parametrar, {0} och {1}. I följande exempel skapas en **displayName** med det angivna formatet:

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

- Inkommande anspråk:
    - **inputClaim1**: Josef
    - **inputClaim2**: Fernando
- Indataparametrar:
    - **stringFormat**: {0} {1}
- Utgående anspråk:
    - **outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Leta upp ett objekt från ett anspråk **begränsning** samling.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | sträng | Anspråk som innehåller den text som ska sökas i den **restrictionValueClaim** anspråk med den **begränsning** samling.  |
| outputClaim | restrictionValueClaim | sträng | Anspråk som innehåller den **begränsning** samling. När anspråkstransformering har anropats kan innehåller värdet för det här anspråket värdet för det markerade objektet. |

I följande exempel söker upp meddelandet felbeskrivningen baserat på fel-nyckel. Den **responseMsg** anspråk innehåller en samling av felmeddelanden för användaren eller skickas till den förlitande parten.

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
Anspråkstransformering söker efter text för objekt och returnerar värdet. Om begränsningen lokaliseras med `<LocalizedCollection>`, anspråkstransformering returnerar det lokaliserade värdet.

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

- Inkommande anspråk:
    - **mapFromClaim**: B2C_V1_90001
- Utgående anspråk:
    - **restrictionValueClaim**: du kan logga in eftersom du är en mindre.

## <a name="lookupvalue"></a>LookupValue

Leta upp ett anspråksvärde från en lista med värden baserat på värdet för en annan anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | sträng | Anspråk som innehåller en lookup-värde |
| Indataparametrar | |sträng | Samling av indataparametrar. |
| Indataparametrar | errorOnFailedLookup | boolesk | Kontrollera om ett fel returneras när ingen matchande sökning. |
| outputClaim | inputParameterId | sträng | ClaimTypes som skapas när detta omvandling av anspråk har anropats. Värdet för den matchande Id. |

I följande exempel söker upp domännamnet i inpuParameters samlingarna. Anspråkstransformering letar upp domännamnet i identifieraren och returnerar värdet (en program-ID).

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

- Inkommande anspråk:
    - **inputParameterId**: test.com
- Indataparametrar:
    - **Contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Utgående anspråk:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Rensa värdet för en viss anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| outputClaim | claim_to_null | sträng | Anspråket dess värde är NULL. |

Använd den här anspråksomvandling för att ta bort onödiga data från egenskapsuppsättningen anspråk. Därför blir sessions-cookie mindre. I följande exempel tar bort värdet för den `TermsOfService` Anspråkstypen.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inkommande anspråk:
    - **outputClaim**: Välkommen till Contoso-App. Om du fortsätter att bläddra och Använd den här webbplatsen samtycker du till följer och vara bunden av följande villkor...
- Utgående anspråk:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Hämtar domändelen i en e-postadress.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | E-postadress | sträng | ClaimType som innehåller den e-postadressen. |
| outputClaim | domän | sträng | ClaimType som skapas när detta omvandling av anspråk har anropats - domänen. |

Använd detta anspråk omvandling för att analysera domännamnet efter det @-tecknet för användaren. Det kan vara användbart för borttagning av personligt identifierbar information (PII) från granskningsdata. Följande anspråkstransformering visar hur du Parsar domännamnet från en **e-post** anspråk.

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

- Inkommande anspråk:
    - **e-postadress**: joe@outlook.com
- Utgående anspråk:
    - **domän**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontrollerar att en sträng anspråk och `matchTo` Indataparametern är lika med och anger utdata anspråk med värdet i `stringMatchMsg` och `stringMatchMsgCode` indataparametrar, tillsammans med jämför resultatet utgående anspråk som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | sträng | Anspråkstyp som ska jämföras. |
| Indataparametrar | matchTo | sträng | Strängen som ska jämföras med `inputClaim`. |
| Indataparametrar | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase`. |
| Indataparametrar | stringMatchMsg | sträng | Första värdet som anges om strängar är lika med. |
| Indataparametrar | stringMatchMsgCode | sträng | Andra värdet som anges om strängar är lika med. |
| outputClaim | outputClaim1 | sträng | Om strängar är lika med, påståendet utdata innehåller värdet för `stringMatchMsg` indataparameter. |
| outputClaim | outputClaim2 | sträng | Om strängar är lika med, påståendet utdata innehåller värdet för `stringMatchMsgCode` indataparameter. |
| outputClaim | stringCompareResultClaim | boolesk | Jämför resultatet utgående anspråk som är anges som `true` eller `false` baserat på resultatet av jämförelsen. |

Du kan använda detta anspråk omvandling för att kontrollera om ett anspråk är lika med värdet som du angav. Till exempel följande anspråk omvandling kontrollerar om värdet för den **termsOfUseConsentVersion** anspråk är lika med `v1`. Om Ja, ändra värdet till `v2`. 

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

- Inkommande anspråk:
    - **inputClaim**: v1
- Indataparametrar:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: The förklaringar uppgraderas till v2
- Utgående anspråk:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: The förklaringar uppgraderas till v2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontrollerar att en sträng anspråk och `matchTo` Indataparametern är lika med och anger utdata anspråk med värdet i `outputClaimIfMatched` Indataparametern, tillsammans med jämför resultatet utgående anspråk som ska anges som `true` eller `false` baserat på den resultat av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | sträng | Anspråkstyp som ska jämföras. |
| Indataparametrar | matchTo | sträng | Strängen som ska jämföras med inputClaim. |
| Indataparametrar | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase`. |
| Indataparametrar | outputClaimIfMatched | sträng | Värdet som anges om strängar är lika med. |
| outputClaim | outputClaim | sträng | Om strängar är lika med, påståendet utdata innehåller värdet för `outputClaimIfMatched` indataparameter. Eller null om strängarna inte matchar. |
| outputClaim | stringCompareResultClaim | boolesk | Jämför resultatet utgående anspråk som är anges som `true` eller `false` baserat på resultatet av jämförelsen. |

Till exempel följande anspråk omvandling kontrollerar om värdet för **ageGroup** anspråk är lika med `Minor`. Om Ja, returnera värdet för `B2C_V1_90001`. 

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

- Inkommande anspråk:
    - **claimToMatch**: mindre
- Indataparametrar:
    - **matchTo**: mindre
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**: B2C_V1_90001
- Utgående anspråk:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true

