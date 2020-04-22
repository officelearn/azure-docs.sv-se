---
title: Lokalisering - Azure Active Directory B2C
description: Ange lokaliseringselementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681419"
---
# <a name="localization"></a>Lokalisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med **lokaliseringselementet** kan du stödja flera språk eller språk i principen för användarens färder. Med lokaliseringsstödet i principer kan du:

- Ställ in den explicita listan över språk som stöds i en princip och välj ett standardspråk.
- Ange språkspecifika strängar och samlingar.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Lokaliseringselementet innehåller följande attribut: **Localization**

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Enabled | Inga | Möjliga `true` värden: `false`eller . |

**Lokaliseringselementet** innehåller följande XML-element

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Språk som stöds | 1:n | Lista över språk som stöds. |
| Lokaliserade Resurser | 0:n | Lista över lokaliserade resurser. |

## <a name="supportedlanguages"></a>Språk som stöds

Elementet **Språk som stöds** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | Det språk som ska användas som standard för lokaliserade resurser. |
| MergeBehavior | Inga | Ett uppräkningsvärden för värden som slås samman tillsammans med alla ClaimType som finns i en överordnad princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som anges i basprincipen. Möjliga `Append`värden: `Prepend`, `ReplaceAll`, eller . Värdet `Append` anger att insamlingen av data som finns ska läggas till i slutet av den samling som anges i den överordnade principen. Värdet `Prepend` anger att insamlingen av data ska läggas till före den samling som anges i den överordnade principen. Värdet `ReplaceAll` anger att insamlingen av data som definierats i den överordnade principen ska ignoreras, med hjälp av i stället de data som definierats i den aktuella principen. |

### <a name="supportedlanguages"></a>Språk som stöds

Elementet **Språk som stöds** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Språk som stöds | 1:n | Visar innehåll som överensstämmer med en språktagg per RFC 5646 - Taggar för identifierande språk. |

## <a name="localizedresources"></a>Lokaliserade Resurser

Elementet **LocalizedResources** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för att unikt identifiera lokaliserade resurser. |

Elementet **LocalizedResources** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Lokaliserade sorteringar | 0:n | Definierar hela samlingar i olika kulturer. En samling kan ha olika antal objekt och olika strängar för olika kulturer. Exempel på samlingar är uppräkningar som visas i anspråkstyper. En lands-/regionlista visas till exempel för användaren i en listruta. |
| Lokaliseradestrång | 0:n | Definierar alla strängar, utom de strängar som visas i samlingar, i olika kulturer. |

### <a name="localizedcollections"></a>Lokaliserade sorteringar

Elementet **LocalizedCollections** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Lokaliseradkollage | 1:n | Lista över språk som stöds. |

#### <a name="localizedcollection"></a>Lokaliseradkollage

Elementet **LocalizedCollection** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Elementtyp | Ja | Refererar till ett ClaimType-element eller ett användargränssnittselement i principfilen. |
| ElementId (elementId) | Ja | En sträng som innehåller en referens till en anspråkstyp som redan definierats i avsnittet ClaimsSchema och som används om **ElementType** är inställt på en ClaimType. |
| MålInsamling | Ja | Målsamlingen. |

Elementet **LocalizedCollection** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0:n | Definierar ett tillgängligt alternativ som användaren kan välja för ett anspråk i användargränssnittet, till exempel ett värde i en listruta. |

Elementet **Objekt** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Den användarvänliga visningssträngen som ska visas för användaren i användargränssnittet för det här alternativet. |
| Värde | Ja | Strängen anspråksvärde som är associerad med att välja det här alternativet. |
| VäljByDefault | Inga | Anger om det här alternativet ska väljas som standard i användargränssnittet. Möjliga värden: Sant eller Falskt. |

I följande exempel visas användningen av elementet **LocalizedCollections.** Den innehåller två **LocalizedCollection** element, en för engelska och en annan för spanska. Båda ställer in **begränsningssamlingen** av fordran `Gender` med en lista över objekt för engelska och spanska.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>Lokaliseradestrång

Elementet **LocalizedStrings** innehåller följande element:

| Element | Händelser | Beskrivning |
| ------- | ----------- | ----------- |
| LokaliseradString | 1:n | En lokaliserad sträng. |

Elementet **LocalizedString** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Elementtyp | Ja | Möjliga värden: [ClaimsProvider](#claimsprovider), [ClaimType](#claimtype), [ErrorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [Predikat](#predicate), [InputValidation](#inputvalidation)eller [UxElement](#uxelement).   | 
| ElementId (elementId) | Ja | Om **ElementType** är `ClaimType` `Predicate`inställt `InputValidation`på , eller innehåller det här elementet en referens till en anspråkstyp som redan har definierats i avsnittet ClaimsSchema. |
| StringId (på) | Ja | Om **ElementType** är `ClaimType`inställt på innehåller det här elementet en referens till ett attribut av en anspråkstyp. Möjliga `DisplayName`värden: `AdminHelpText`, `PatternHelpText`, eller . Värdet `DisplayName` används för att ange anspråksvisningsnamnet. Värdet `AdminHelpText` används för att ange anspråksanvändarens hjälptextnamn. Värdet `PatternHelpText` används för att ange hjälptexten för anspråksmönster. Om **ElementType** är `UxElement`inställt på innehåller det här elementet en referens till ett attribut för ett användargränssnittselement. Om **ElementType** är `ErrorMessage`inställt på anger det här elementet identifieraren för ett felmeddelande. Se [Lokaliseringssträng-ID:er](localization-string-ids.md) `UxElement` för en fullständig lista över identifierarna.|

## <a name="elementtype"></a>Elementtyp

ElementType-referensen till en anspråkstyp, en anspråksomvandling eller ett användargränssnittselement i principen som ska lokaliseras.

| Element för att lokalisera | Elementtyp | ElementId (elementId) |StringId (på) |
| --------- | -------- | ----------- |----------- |
| Identitetsproviderns namn |`ClaimsProvider`| | ID för elementet ClaimsExchange|
| Attribut för anspråkstyp|`ClaimType`|Anspråkstypens namn| Attributet för anspråket som ska lokaliseras. Möjliga `AdminHelpText`värden: `DisplayName` `PatternHelpText`, `UserHelpText`, och .|
|Felmeddelande|`ErrorMessage`||ID:n för felmeddelandet |
|Kopierar lokaliserade strängar till anspråk|`GetLocalizedStringsTra nsformationClaimType`||Namnet på utdataanspråket|
|Predikatanvändarmeddelande|`Predicate`|Namnet på predikatet| Attributet för predikatet som ska lokaliseras. Möjliga värden: `HelpText`.|
|Användarmeddelande för predikatgrupp|`InputValidation`|ID för elementet Predikatvalidation.|ID för elementet PredicateGroup. Predikatgruppen måste vara underordnad det predikatvalideringselement som definieras i ElementId.|
|Element för användargränssnitt |`UxElement` | | ID:n för det användargränssnittselement som ska lokaliseras.|

## <a name="examples"></a>Exempel

### <a name="claimsprovider"></a>FordringarProvider

Värdet för ClaimsProvider används för att lokalisera ett av anspråksleverantörernas visningsnamn. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

I följande exempel visas hur anspråksleverantörers visningsnamn lokaliseras.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType (Påståttstyp)

ClaimType-värdet används för att lokalisera ett av anspråksattributen. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

I följande exempel visas hur du lokaliserar attributen DisplayName, UserHelpText och PatternHelpText för e-anspråkstypen.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>Errormessage

ErrorMessage-värdet används för att lokalisera ett av systemfelmeddelandena. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

I följande exempel visas hur du lokaliserar felmeddelandet UserMessageIfClaimsPrincipalreadyExists.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

Värdet GetLocalizedStringsTransformationClaimType används för att kopiera lokaliserade strängar till anspråk. Mer information finns i [GetLocalizedStringsTransformation claims transformation](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

I följande exempel visas hur du lokaliserar utdataanspråk för getlocalizedStringsTransformation claims transformation.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikat

Predikatvärdet används för att lokalisera ett av [predikatfelmeddelandena.](predicates.md) 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

I följande exempel visas hur du lokaliserar predikathjälptext.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

Värdet för indatavalidation används för att lokalisera ett av felmeddelandena [för predicateValidation-gruppen.](predicates.md) 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

I följande exempel visas hur du lokaliserar en hjälptext för predikatverifieringsgrupper.

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement (olikartade)

UxElement-värdet används för att lokalisera ett av elementen i användargränssnittet. I följande exempel visas hur du lokaliserar knapparna för fortsätt och avbryt.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för lokaliseringsexempel:

- [Språkanpassning med anpassad princip i Azure Active Directory B2C](custom-policy-localization.md)
- [Språkanpassning med användarflöden i Azure Active Directory B2C](user-flow-language-customization.md)
