---
title: Lokalisering - Azure Active Directory B2C
description: Ange lokaliseringselementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126754"
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
| Elementtyp | Ja | En referens till ett anspråkstypelement eller ett användargränssnittselement i principen. `ClaimType`Möjliga värden: `UxElement` `ErrorMessage`, `Predicate`, `GetLocalizedStringsTransformationClaimType`, , eller . Värdet `ClaimType` används för att lokalisera ett av anspråksattributen, enligt vad som anges i StringId. Värdet `UxElement` används för att lokalisera ett av de användargränssnittselement som anges i StringId. Värdet `ErrorMessage` används för att lokalisera ett av systemfelmeddelandena som anges i StringId. Värdet `Predicate` används för att lokalisera ett av [predikatfelmeddelandena,](predicates.md) enligt vad som anges i StringId. Värdet `InputValidation` används för att lokalisera ett av felmeddelandena [för gruppen PredicateValidation](predicates.md) enligt vad som anges i StringId. Värdet `GetLocalizedStringsTransformationClaimType` används för att kopiera lokaliserade strängar till anspråk. Mer information finns i [GetLocalizedStringsTransformation claims transformation](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId (elementId) | Ja | Om **ElementType** är `ClaimType` `Predicate`inställt `InputValidation`på , eller innehåller det här elementet en referens till en anspråkstyp som redan har definierats i avsnittet ClaimsSchema. |
| StringId (på) | Ja | Om **ElementType** är `ClaimType`inställt på innehåller det här elementet en referens till ett attribut av en anspråkstyp. Möjliga `DisplayName`värden: `AdminHelpText`, `PatternHelpText`, eller . Värdet `DisplayName` används för att ange anspråksvisningsnamnet. Värdet `AdminHelpText` används för att ange anspråksanvändarens hjälptextnamn. Värdet `PatternHelpText` används för att ange hjälptexten för anspråksmönster. Om **ElementType** är `UxElement`inställt på innehåller det här elementet en referens till ett attribut för ett användargränssnittselement. Om **ElementType** är `ErrorMessage`inställt på anger det här elementet identifieraren för ett felmeddelande. Se [Lokaliseringssträng-ID:er](localization-string-ids.md) `UxElement` för en fullständig lista över identifierarna.|


I följande exempel visas en lokaliserad registreringssida. De tre första **värdena LocalizedString** anger anspråksattributet. Den tredje ändrar värdet på continue-knappen. Den sista ändrar felmeddelandet.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

I följande exempel visas en lokaliserad **UserHelpText** av `IsLengthBetween8And64` **Predikat** med ID . Och en lokaliserad **UserHelpText** av **PredicateGroup** med ID `CharacterClasses` för `StrongPassword` **predikatvalidation** med ID .

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Konfigurera lokalisering

I den här artikeln visas hur du stöder flera språk eller språk i principen för användarresor. Lokalisering kräver tre steg: konfigurera den explicita listan över språk som stöds, tillhandahålla språkspecifika strängar och samlingar och redigera ContentDefinition för sidan.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Ställ in den explicita listan över språk som stöds

Lägg till **lokaliseringselementet** i elementet BuildingBlocks under elementet **BuildingBlocks** med listan över språk som stöds. I följande exempel visas hur du definierar lokaliseringsstödet för både engelska (standard) och spanska:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för lokaliseringsexempel:

- [Språkanpassning med anpassad princip i Azure Active Directory B2C](custom-policy-localization.md)
- [Språkanpassning med användarflöden i Azure Active Directory B2C](user-flow-language-customization.md)
