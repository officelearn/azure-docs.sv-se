---
title: Exempel på omvandling av stränganspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Stränganspråksomvandlingsexempel för IEF-schemat (Identity Experience Framework) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acacba591c9b895f1bd6abfbab5d3d4a4c858d12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472783"
---
# <a name="string-claims-transformations"></a>Omvandling av stränganspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder stränganspråksomvandlingarna i Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Jämför två anspråk och kasta ett undantag om de inte är lika enligt den angivna jämförelseindatanClaim1, inputClaim2 och stringComparison.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångClaim1 | sträng | Första anspråkets typ, som ska jämföras. |
| InputClaim | ingångClaim2 | sträng | Andra fordran typ, som skall jämföras. |
| InputParameter | strängKomprodera | sträng | strängjämn, ett av värdena: Ordinal, OrdinalIgnoreCase. |

**AssertStringClaimsAreEqual-anspråksomvandlingen** utförs alltid från en [teknisk valideringsprofil](validation-technical-profile.md) som anropas av en [självpåtrerad teknisk profil](self-asserted-technical-profile.md)eller en [DisplayConrtol](display-controls.md). Metadata `UserMessageIfClaimsTransformationStringsAreNotEqual` för en självförsäkrad teknisk profil styr felmeddelandet som visas för användaren. Felmeddelandena kan [lokaliseras](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual utförande](./media/string-transformations/assert-execution.png)

Du kan använda den här anspråksomvandlingen för att se till att två ClaimTypes har samma värde. Om inte, genereras ett felmeddelande. I följande exempel kontrolleras att **strongAuthenticationEmailAddress** ClaimType är lika med **e-post** ClaimType. Annars genereras ett felmeddelande.

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


Den tekniska profilen för **inloggningsbaserad** validering anropar **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** hävdar omvandling.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självsäkra tekniska profilen anropar den tekniska profilen för **valideringslogisan-NonInteractive.**

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

- Ingående anspråk:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Indataparametrar:
  - **stringComparison**: ordinalIgnoreCase
- Resultat: Fel uppstod

## <a name="changecase"></a>Ändringsärende

Ändrar fallet med det tillhandahållna påståendet att gemener eller versaler beror på operatören.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångClaim1 | sträng | Den ClaimType som ska ändras. |
| InputParameter | toCase (tillFalla) | sträng | Ett av följande `LOWER` värden: eller `UPPER`. |
| OutputClaim | outputClaim | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

Använd den här anspråksomvandlingen om du vill ändra en sträng claimtype till gemener eller versaler.

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

- Ingående anspråk:
  - **e-post:**SomeOne@contoso.com
- Indataparametrar:
    - **toCase**: LÄGRE
- Utdataanspråk:
  - **e-post:**someone@contoso.com

## <a name="createstringclaim"></a>SkapaStringClaim

Skapar ett stränganspråk från den angivna indataparametern i omvandlingen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
|----- | ----------------------- | --------- | ----- |
| InputParameter | värde | sträng | Strängen som ska ställas in. Den här indataparametern stöder [stränganspråkstransformationsuttryck](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | skapadeClaim | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats, med det värde som anges i indataparametern. |

Använd den här anspråksomvandlingen för att ange ett string ClaimType-värde.

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
    - **värde**: Contoso användarvillkor...
- Utdataanspråk:
    - **createdClaim**: TOS ClaimType innehåller "Contoso användarvillkor..." Värde.

## <a name="compareclaims"></a>JämförClaims

Ta reda på om ett stränganspråk är lika med ett annat. Resultatet är en ny boolesk ClaimType med värdet `true` eller `false`.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångClaim1 | sträng | Första anspråkstypen, som ska jämföras. |
| InputClaim | ingångClaim2 | sträng | Andra anspråkstypen, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga `EQUAL` värden: `NOT EQUAL`eller . |
| InputParameter | ignoreCase (ignoreraFall) | boolean | Anger om den här jämförelsen ska ignorera fallet med de strängar som jämförs. |
| OutputClaim | outputClaim | boolean | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

Använd den här anspråksomvandlingen för att kontrollera om ett anspråk är lika med ett annat anspråk. Följande anspråksomvandling kontrollerar till exempel om värdet för **e-postanspråket** är lika med anspråket **Verified.Email.**

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

- Ingående anspråk:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Indataparametrar:
    - **operator**: INTE LIKA
    - **ignoreCase**: sant
- Utdataanspråk:
    - **outputClaim**: sant

## <a name="compareclaimtovalue"></a>JämförClaimToValue

Avgör om ett anspråksvärde är lika med indataparametervärdet.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | ingångClaim1 | sträng | Påståendets typ, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga `EQUAL` värden: `NOT EQUAL`eller . |
| InputParameter | jämförTill | sträng | strängjämn, ett av värdena: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase (ignoreraFall) | boolean | Anger om den här jämförelsen ska ignorera fallet med de strängar som jämförs. |
| OutputClaim | outputClaim | boolean | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

Du kan använda den här anspråksomvandlingen för att kontrollera om ett anspråk är lika med ett värde som du har angett. Följande anspråksomvandling kontrollerar till exempel om värdet för **termernaOfUseConsentVersion-anspråk** är lika med `v1`.

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
- Ingående anspråk:
    - **inputClaim1**: v1
- Indataparametrar:
    - **jämförTill:** V1
    - **operatör**: EQUAL
    - **ignoreCase**: sant
- Utdataanspråk:
    - **outputClaim**: sant

## <a name="createrandomstring"></a>SkapaRandomString

Skapar en slumpmässig sträng med hjälp av slumptalsgeneratorn. Om slumptalsgeneratorn `integer`är av typen kan en fröparameter och ett högsta antal anges. En valfri strängformatsparameter gör att utdata kan formateras med den, och en valfri parameter för base64 anger om utdata är base64-kodad randomGeneratorType [guid, heltal] outputClaim (String).

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | sträng | Anger det slumpmässiga värde `GUID` som ska genereras ,(globalt unikt ID) eller `INTEGER` (ett tal). |
| InputParameter | strängFormat | sträng | [Valfritt] Formatera det slumpmässiga värdet. |
| InputParameter | base64 | boolean | [Valfritt] Konvertera det slumpmässiga värdet till base64. Om strängformat används kodas värdet efter strängformat till base64. |
| InputParameter | maximumNumber (maxAntal) | int | [Valfritt] Endast `INTEGER` för randomGeneratorType. Ange det maximala antalet. |
| InputParameter | Utsäde  | int | [Valfritt] Endast `INTEGER` för randomGeneratorType. Ange utsöndrna för det slumpmässiga värdet. Samma frö ger samma sekvens av slumptal. |
| OutputClaim | outputClaim | sträng | De ClaimTypes som kommer att produceras efter att den här anspråksomvandlingen har anropats. Det slumpmässiga värdet. |

I det här exemplet genereras ett globalt unikt ID. Den här anspråksomvandlingen används för att skapa det slumpmässiga UPN (användarnamnet).

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
- Utdataanspråk:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

I det här exemplet genereras ett heltalssvabbt värde mellan 0 och 1000. Värdet är formaterat till OTP_{slumpmässigt värde}.

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
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: falskt
- Utdataanspråk:
    - **outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formatera ett anspråk enligt den angivna formatsträngen. Den här omvandlingen `String.Format` använder C#-metoden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng |Den ClaimType som fungerar {0} som parameter för strängformat. |
| InputParameter | strängFormat | sträng | Strängformatet, inklusive {0} parametern. Den här indataparametern stöder [stränganspråkstransformationsuttryck](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

Använd den här anspråksomvandlingen {0}om du vill formatera en sträng med en parameter . I följande exempel skapas ett **userPrincipalName**. Alla tekniska profiler för den `Facebook-OAUTH` sociala identitetsprovidern, till exempel **anropar CreateUserPrincipalName** för att generera ett **userPrincipalName**.

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

- Ingående anspråk:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Indataparametrar:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Utdataanspråk:
  - **outputClaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormateraStringMultipleClaims

Formatera två anspråk enligt den angivna formatsträngen. Den här omvandlingen `String.Format` använder C#-metoden.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng | Den ClaimType som fungerar {0} som parameter för strängformat. |
| InputClaim | inputClaim | sträng | Den ClaimType som fungerar {1} som parameter för strängformat. |
| InputParameter | strängFormat | sträng | Strängformatet, {0} inklusive {1} och parametrarna. Den här indataparametern stöder [stränganspråkstransformationsuttryck](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats. |

Använd den här anspråksomvandlingen {0} för {1}att formatera en sträng med två parametrar och . I följande exempel skapas ett **displayName** med det angivna formatet:

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

- Ingående anspråk:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Indataparametrar:
    - **stringFormat** {0} :{1}
- Utdataanspråk:
    - **outputClaim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Kopierar lokaliserade strängar till anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Namnet på den lokaliserade strängen | sträng | Lista över anspråkstyper som produceras efter att den här anspråksomvandlingen har anropats. |

Så här använder du anspråkstransformationen GetLocalizedStringsTransformation:

1. Definiera en [lokaliseringssträng](localization.md) och associera den med en [självbekände-teknisk profil](self-asserted-technical-profile.md).
1. Elementet `ElementType` `LocalizedString` måste ställas in `GetLocalizedStringsTransformationClaimType`på .
1. Det `StringId` är en unik identifierare som du definierar och använder den senare i anspråksomvandlingen.
1. I anspråksomvandlingen anger du listan över anspråk som ska anges med den lokaliserade strängen. Är `ClaimTypeReferenceId` en referens till en ClaimType som redan definierats i avsnittet ClaimsSchema i principen. Namnet `TransformationClaimType` på den lokaliserade strängen som `StringId` definieras `LocalizedString` i elementet.
1. I en [självpåförd teknisk profil](self-asserted-technical-profile.md), eller en [visningskontroll](display-controls.md) indata eller utdata anspråk omvandling, göra en hänvisning till dina anspråk omvandling.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

I följande exempel visas e-postämnet, brödtexten, kodmeddelandet och e-postmeddelandets signatur från lokaliserade strängar. Dessa anspråk används senare av anpassad e-postverifieringsmall.

Definiera lokaliserade strängar för engelska (standard) och spanska.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Anspråksomvandlingen anger värdet för anspråkstypsämnet med värdet *subject* för `StringId` *email_subject*.

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Utdataanspråk:
  - **ämne:** Contoso konto e-verifieringskod
  - **meddelande:** Tack för att verifiera ditt konto!
  - **codeIntro:** Din kod är
  - **signatur**: Med vänliga hälsningar


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Slå upp ett objekt från en **anspråksbegränsningssamling.**

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | kartaFrånClaim | sträng | Påståendet som innehåller den text som ska tas upp i **begränsningenValueClaim** hävdar med **begränsningsamlingen.**  |
| OutputClaim | begränsningValueClaim | sträng | Påståendet som innehåller **begränsningssamlingen.** När anspråksomvandlingen har anropats innehåller värdet för det här anspråket värdet för det markerade objektet. |

I följande exempel visas felmeddelandebeskrivningen baserat på felnyckeln. **SvaretMsg-anspråket** innehåller en samling felmeddelanden som ska visas för slutanvändaren eller som ska skickas till den förlitande parten.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Anspråksomvandlingen slår upp texten i objektet och returnerar dess värde. Om begränsningen är `<LocalizedCollection>`lokaliserad med hjälp returnerar anspråksomvandlingen det lokaliserade värdet.

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

- Ingående anspråk:
    - **mapFromClaim**: B2C_V1_90001
- Utdataanspråk:
    - **restrictionValueClaim**: Du kan inte logga in eftersom du är minderårig.

## <a name="lookupvalue"></a>Slå uppVärde

Slå upp ett anspråksvärde från en lista med värden baserat på värdet av ett annat anspråk.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | sträng | Påståendet som innehåller uppslagsvärdet |
| InputParameter | |sträng | Insamling av ingångArmometer. |
| InputParameter | errorOnFailedLookup | boolean | Styra om ett fel returneras när ingen matchande sökning. |
| OutputClaim | inputParameterId | sträng | De ClaimTypes som kommer att produceras efter att den här anspråksomvandlingen har anropats. Värdet på matchandet `Id`. |

I följande exempel slår du upp domännamnet i en av indataParameters-samlingarna. Anspråksomvandlingen slår upp domännamnet i identifieraren och returnerar dess värde (ett program-ID).

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

- Ingående anspråk:
    - **inputParameterId**: test.com
- Indataparametrar:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: falskt
- Utdataanspråk:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

När `errorOnFailedLookup` indataparametern är inställd `true`på körs alltid omvandlingen av **uppslagsvärdesanspråk** från en [teknisk verifieringsprofil](validation-technical-profile.md) som anropas av en [självpåtrerad teknisk profil](self-asserted-technical-profile.md)eller en [DisplayConrtol](display-controls.md). Metadata `LookupNotFound` för en självförsäkrad teknisk profil styr felmeddelandet som visas för användaren.

![AssertStringClaimsAreEqual utförande](./media/string-transformations/assert-execution.png)

I följande exempel slår du upp domännamnet i en av indataParameters-samlingarna. Anspråksomvandlingen slår upp domännamnet i identifieraren och returnerar dess värde (ett program-ID) eller ger upphov till ett felmeddelande.

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputParameterId**: live.com
- Indataparametrar:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Fel:
    - Ingen matchning hittades för värdet för indataanspråk i listan över indataparameter-ID:n och errorOnFailedLookup är sant.


## <a name="nullclaim"></a>NullClaim (nullClaim)

Rensa värdet på en viss fordran.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | sträng | Anspråkets värde är inställt på NULL. |

Använd den här anspråksomvandlingen för att ta bort onödiga data från egenskapspåsen för anspråk så att sessionscookien blir mindre. I följande exempel tas värdet `TermsOfService` för anspråkstypen bort.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Ingående anspråk:
    - **outputClaim**: Välkommen till Contoso App. Om du fortsätter att surfa och använda denna webbplats, samtycker du till att följa och vara bunden av följande villkor ...
- Utdataanspråk:
    - **outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain (avtolkning)

Hämtar domändelen av en e-postadress.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Emailaddress | sträng | Den ClaimType som innehåller e-postadressen. |
| OutputClaim | domän | sträng | Den ClaimType som produceras efter att den här anspråksomvandlingen har anropats - domänen. |

Använd den här anspråksomvandlingen för att tolka domännamnet efter @-symbolen för användaren. Följande anspråksomvandling visar hur domännamnet tolkas från ett **e-postanspråk.**

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

- Ingående anspråk:
  - **e-postadress:**joe@outlook.com
- Utdataanspråk:
    - **domän**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Kontrollerar att `claimToMatch` en `matchTo` stränganspråks- och indataparameter är `outputClaimIfMatched` lika och anger utdataanspråken med det `true` `false` värde som finns i indataparametern, tillsammans med jämför resultatutdataanspråk, som ska anges som eller baseras på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | sträng | Anspråkstypen, som ska jämföras. |
| InputParameter | matchaTill | sträng | Det reguljära uttrycket som ska matchas. |
| InputParameter | outputClaimIfMatched | sträng | Det värde som ska anges om strängarna är lika. |
| OutputClaim | outputClaim | sträng | Om reguljärt uttryck matchas innehåller det `outputClaimIfMatched` här utdataanspråket värdet för indataparametern. Eller null, om ingen matchning. |
| OutputClaim | regexCompareResultClaim | boolean | Anspråkstypen för svar om resultatet för `true` reguljära uttryck matchar resultatutdatatypen, som ska anges som eller `false` baseras på resultatet av matchningen. |

Du kontrollerar till exempel om det angivna telefonnumret är giltigt, baserat på mönstret för reguljära uttryck för telefonnummer.

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **claimToMatch**: "64854114520"
- Indataparametrar:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**: "isPhone"
- Utdataanspråk:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: sant

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontrollerar att en `matchTo` stränganspråks- och indataparameter är `stringMatchMsg` `stringMatchMsgCode` lika och anger utdataanspråken med värdet `true` som `false` finns i och indataparametrar, tillsammans med jämför resultatutdataanspråk, som ska anges som eller baseras på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråkstypen, som ska jämföras. |
| InputParameter | matchaTill | sträng | Strängen som ska `inputClaim`jämföras med . |
| InputParameter | strängKomprodera | sträng | Möjliga `Ordinal` värden: `OrdinalIgnoreCase`eller . |
| InputParameter | stringMatchMsg | sträng | Det första värdet som ska anges om strängarna är lika. |
| InputParameter | stringMatchMsgCode | sträng | Det andra värdet som ska anges om strängarna är lika. |
| OutputClaim | outputClaim1 | sträng | Om strängar är lika med innehåller det här `stringMatchMsg` utdataanspråket värdet för indataparametern. |
| OutputClaim | outputClaim2 | sträng | Om strängar är lika med innehåller det här `stringMatchMsgCode` utdataanspråket värdet för indataparametern. |
| OutputClaim | strängCompareResultClaim | boolean | Jämförelseresultatutdataanspråkstypen, som `true` ska `false` anges som eller baseras på resultatet av jämförelsen. |

Du kan använda den här anspråksomvandlingen för att kontrollera om ett anspråk är lika med det värde som du har angett. Följande anspråksomvandling kontrollerar till exempel om värdet för **termernaOfUseConsentVersion-anspråk** är lika med `v1`. Om ja ändrar `v2`du värdet till .

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

- Ingående anspråk:
    - **inputClaim**: v1
- Indataparametrar:
    - **matchTo**: V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode:** TOS uppgraderas till v2
- Utdataanspråk:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2:** TOS uppgraderas till v2
    - **stringCompareResultClaim**: sant

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontrollerar att en `matchTo` stränganspråks- och indataparameter är `outputClaimIfMatched` lika och anger utdataanspråken med det `true` `false` värde som finns i indataparametern, tillsammans med jämför resultatutdataanspråk, som ska anges som eller baseras på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | sträng | Anspråkstypen, som ska jämföras. |
| InputParameter | matchaTill | sträng | Strängen som ska jämföras med inputClaim. |
| InputParameter | strängKomprodera | sträng | Möjliga `Ordinal` värden: `OrdinalIgnoreCase`eller . |
| InputParameter | outputClaimIfMatched | sträng | Det värde som ska anges om strängarna är lika. |
| OutputClaim | outputClaim | sträng | Om strängar är lika med innehåller det här `outputClaimIfMatched` utdataanspråket värdet för indataparametern. Eller null, om strängarna inte matchar. |
| OutputClaim | strängCompareResultClaim | boolean | Jämförelseresultatutdataanspråkstypen, som `true` ska `false` anges som eller baseras på resultatet av jämförelsen. |

Följande anspråksomvandling kontrollerar till exempel om värdet `Minor`för **ageGroup-anspråket** är lika med . Om ja returnerar `B2C_V1_90001`du värdet till .

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

- Ingående anspråk:
    - **claimToMatch**: Minor
- Indataparametrar:
    - **matchTo**: Minor
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Utdataanspråk:
    - **isMinorResponseCode:** B2C_V1_90001
    - **isMinor**: sant


## <a name="stringcontains"></a>StringContains

Bestäm om en angiven delsträng inträffar i indataanspråket. Resultatet är en ny boolesk ClaimType med värdet `true` eller `false`. `true`om värdeparametern förekommer i `false`den här strängen, annars .

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråkstypen, som ska genomsökas. |
|InputParameter|innehåller|sträng|Värdet att söka.|
|InputParameter|ignoreCase (ignoreraFall)|sträng|Anger om den här jämförelsen ska ignorera fallet med strängen som jämförs.|
| OutputClaim | outputClaim | sträng | Den ClaimType som produceras efter att den här ClaimsTransformation har anropats. En boolesk indikator om delsträngen inträffar i indataanspråket. |

Använd den här anspråksomvandlingen för att kontrollera om en stränganspråkstyp innehåller en delsträng. I det här `roles` exemplet kontrollerar du om stränganspråkstypen innehåller värdet **för administratören**.

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputClaim**: "Admin, Godkännare, Redaktör"
- Indataparametrar:
    - **innehåller**: "admin",
    - **ignoreCase**: sant
- Utdataanspråk:
    - **outputClaim**: sant

## <a name="stringsubstring"></a>SträngarSubstring

Extraherar delar av en stränganspråkstyp, med början vid tecknet på den angivna positionen, och returnerar det angivna antalet tecken.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråkstypen, som innehåller strängen. |
| InputParameter | startIndex (startIndex) | int | Den nollbaserade startteckenpositionen för en delsträng i den här instansen. |
| InputParameter | length | int | Antalet tecken i delsträngen. |
| OutputClaim | outputClaim | boolean | En sträng som motsvarar delsträngen för längden som börjar vid startIndex i den här instansen, eller Töm om startIndex är lika med längden på den här instansen och längden är noll. |

Du kan till exempel hämta landsprefixet för telefonnummer.


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputClaim**: "+1644114520"
- Indataparametrar:
    - **startIndex**: 0
    - **längd**: 2
- Utdataanspråk:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

Söker efter en formulärsträng för ett angivet värde och returnerar en ny anspråkstypssträng där alla förekomster av en angiven sträng i den aktuella strängen ersätts med en annan angiven sträng.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråkstypen, som innehåller strängen. |
| InputParameter | gamlaVärdera | sträng | Strängen som ska genomsökas. |
| InputParameter | newValue (nyaValue) | sträng | Strängen för att ersätta alla förekomster av`oldValue` |
| OutputClaim | outputClaim | boolean | En sträng som motsvarar den aktuella strängen förutom att alla förekomster av oldValue ersätts med newValue. Om oldValue inte hittas i den aktuella instansen returnerar metoden den aktuella instansen oförändrad. |

Till exempel normalisera ett telefonnummer genom `-` att ta bort tecknen


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Ingående anspråk:
    - **inputClaim**: "+164-411-452-054"
- Indataparametrar:
    - **oldValue**: "-"
    - **längd**: ""
- Utdataanspråk:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

Sammanfogar elementen i en angiven strängsamlingsanspråkstyp med hjälp av den angivna avgränsaren mellan varje element eller medlem.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | strängInsamling | En samling som innehåller strängarna att sammanfoga. |
| InputParameter | avgränsare | sträng | Strängen som ska användas som avgränsare, till exempel komma `,`. |
| OutputClaim | outputClaim | sträng | En sträng som består av `inputClaim` medlemmarna i strängsamlingen, `delimiter` avgränsad av indataparametern. |

I följande exempel tar du en strängsamling med användarroller och konverterar den till en kommaavgränsaresträng. Du kan använda den här metoden för att lagra en strängsamling i Azure AD-användarkonto. Senare, när du läser kontot från `StringSplit` katalogen, använder du för att konvertera kommatecknet avgränsare strängen tillbaka till strängsamling.

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **inputClaim**: [ "Admin", "Författare", "Reader" ]
- Indataparametrar:
  - **avgränsare**: ","
- Utdataanspråk:
  - **outputClaim**: "Admin,Författare,Reader"


## <a name="stringsplit"></a>StringSplit (Str.

Returnerar en strängmatris som innehåller de delsträngar i den här instansen som avgränsas av element i en angiven sträng.

| Objekt | TransformationClaimType | Datatyp | Anteckningar |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | En stränganspråkstyp som innehåller de understrängar som ska delas. |
| InputParameter | avgränsare | sträng | Strängen som ska användas som avgränsare, till exempel komma `,`. |
| OutputClaim | outputClaim | strängInsamling | En strängsamling vars element innehåller delsträngarna i den här `delimiter` strängen som avgränsas av indataparametern. |

I följande exempel tar du en kommaavgränsare sträng med användarroller och konverterar den till en strängsamling.

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Ingående anspråk:
  - **inputClaim:**"Admin,Författare,Reader"
- Indataparametrar:
  - **avgränsare**: ","
- Utdataanspråk:
  - **outputClaim**: [ "Admin", "Författare", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Stränganspråksomformningerar uttryck
Anspråksomvandlingsuttryck i Azure AD B2C-anpassade principer ger kontextinformation om klient-ID och tekniskt profil-ID.

  | Uttryck | Beskrivning | Exempel |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Den tekniska profilenIdnamn. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | Klient-ID för den förlitande partens princip. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | Klient-ID för förtroenderamverket. | your-tenant.onmicrosoft.com |
