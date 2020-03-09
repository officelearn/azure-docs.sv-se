---
title: Lokalisering-Azure Active Directory B2C
description: Ange lokaliserings element för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5dcbc2ee35620d0a29c495b24bd3756769095a17
ms.sourcegitcommit: 3616b42a0d6bbc31b965995d861930e53d2cf0d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933007"
---
# <a name="localization"></a>Lokalisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokaliserings** elementet gör att du kan stödja flera språk eller språk i principen för användar resan. Lokaliserings stödet i principer gör att du kan:

- Konfigurera den explicita listan över de språk som stöds i en princip och välj ett standard språk.
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

**Lokaliserings** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Enabled | Nej | Möjliga värden: `true` eller `false`. |

**Lokaliserings** elementet innehåller följande XML-element

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lista över språk som stöds. |
| LocalizedResources | 0: n | Lista över lokaliserade resurser. |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | Det språk som ska användas som standard för lokaliserade resurser. |
| MergeBehavior | Nej | Ett uppräknings värde för värden som slås samman med en ClaimType som finns i en överordnad princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som anges i bas principen. Möjliga värden: `Append`, `Prepend`eller `ReplaceAll`. Värdet `Append` anger att data uppsättningen som finns ska läggas till i slutet av den samling som anges i den överordnade principen. Värdet `Prepend` anger att data mängden som finns ska läggas till innan den samling som anges i den överordnade principen. Värdet `ReplaceAll` anger att data uppsättningen som definieras i den överordnade principen ska ignoreras, med hjälp av i stället de data som definierats i den aktuella principen. |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Visar innehåll som följer en språktagg enligt RFC 5646-taggar för att identifiera språk. |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för att identifiera lokaliserade resurser unikt. |

**LocalizedResources** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definierar hela samlingar i olika kulturer. En samling kan ha olika antal objekt och olika strängar för olika kulturer. Exempel på samlingar är uppräkningar som visas i anspråks typer. Till exempel visas en lista över länder/regioner för användaren i en listruta. |
| LocalizedStrings | 0: n | Definierar alla strängar, förutom de strängar som visas i samlingar, i olika kulturer. |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Lista över språk som stöds. |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | Refererar till ett ClaimType-element eller ett användar gränssnitts element i princip filen. |
| ElementId | Ja | En sträng som innehåller en referens till en anspråks typ som redan har definierats i ClaimsSchema-avsnittet som används om **ElementType** har angetts till en claimType. |
| TargetCollection | Ja | Mål samlingen. |

**LocalizedCollection** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Definierar ett tillgängligt alternativ som användaren kan välja för ett anspråk i användar gränssnittet, t. ex. ett värde i en listruta. |

Elementet **item** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Den användarvänliga visnings strängen som ska visas för användaren i användar gränssnittet för det här alternativet. |
| Värde | Ja | Det sträng anspråks värde som är associerat med att välja det här alternativet. |
| SelectByDefault | Nej | Anger om det här alternativet ska vara markerat som standard i användar gränssnittet. Möjliga värden: true eller false. |

I följande exempel visas användningen av **LocalizedCollections** -elementet. Den innehåller två **LocalizedCollection** -element, ett för engelska och ett för spanska. Båda ställer in **begränsnings** insamlingen för anspråks `Gender` med en lista över objekt för engelska och spanska.

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

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | En lokaliserad sträng. |

**LocalizedString** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | En referens till ett anspråks typ element eller ett användar gränssnitts element i principen. Möjliga värden: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`eller `GetLocalizedStringsTransformationClaimType`. `ClaimType`-värdet används för att lokalisera ett av attributen för anspråk enligt vad som anges i StringId. `UxElement`-värdet används för att lokalisera ett av användar gränssnitts elementen som anges i StringId. `ErrorMessage`-värdet används för att lokalisera ett av system fel meddelandena som anges i StringId. `Predicate`-värdet används för att lokalisera ett av de [predikat](predicates.md) fel meddelanden som anges i StringId. `InputValidation`-värdet används för att lokalisera en av [PredicateValidation](predicates.md) Groups fel meddelanden som anges i StringId. `GetLocalizedStringsTransformationClaimType`-värdet används för att kopiera lokaliserade strängar till anspråk. Mer information finns i [GetLocalizedStringsTransformation Claims-transformering](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Ja | Om **ElementType** är inställt på `ClaimType`, `Predicate`eller `InputValidation`innehåller det här elementet en referens till en anspråks typ som redan har definierats i avsnittet ClaimsSchema. |
| StringId | Ja | Om **ElementType** är inställt på `ClaimType`, innehåller det här elementet en referens till ett attribut av en anspråks typ. Möjliga värden: `DisplayName`, `AdminHelpText`eller `PatternHelpText`. Värdet `DisplayName` används för att ange visnings namn för anspråket. `AdminHelpText`-värdet används för att ange hjälp text namnet för anspråks användaren. `PatternHelpText`-värdet används för att ange hjälp texten för anspråks mönstret. Om **ElementType** är inställt på `UxElement`, innehåller det här elementet en referens till ett attribut i ett användar gränssnitts element. Om **ElementType** är inställt på `ErrorMessage`, anger det här elementet identifieraren för ett fel meddelande. Se [lokaliserings Strängs-ID: n](localization-string-ids.md) för en fullständig lista över `UxElement` identifierare.|


I följande exempel visas en lokaliserad registrerings sida. De första tre **LocalizedString** -värdena anger attributet Claim. Den tredje ändrar värdet för knappen Fortsätt. Den senaste ändringen av fel meddelandet.

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

I följande exempel visas en lokaliserad **UserHelpText** av **predikat** med ID `IsLengthBetween8And64`. Och en lokaliserad **UserHelpText** av **PredicateGroup** med ID `CharacterClasses` av **PredicateValidation** med ID `StrongPassword`.

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

Den här artikeln visar hur du stöder flera språk i principen för användar resor. Lokalisering kräver tre steg: Konfigurera den explicita listan över de språk som stöds, ange språkspecifika strängar och samlingar och redigera ContentDefinition för sidan.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Konfigurera den explicita listan över språk som stöds

Under **BuildingBlocks** -elementet lägger du till **lokaliserings** elementet med listan över språk som stöds. I följande exempel visas hur du definierar lokaliserings stödet för både engelska (standard) och spanska:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Tillhandahåll språkspecifika strängar och samlingar

Lägg till **LocalizedResources** -element i **lokaliserings** elementet efter slutet av **SupportedLanguages** -elementet. Du lägger till **LocalizedResources** -element för varje sida (innehålls definition) och valfritt språk som du vill stödja. Om du vill anpassa den enhetliga inloggnings-eller inloggnings sidan, registrera dig och Multi-Factor Authentication (MFA) sidor för engelska, spanska och Frankrike lägger du till följande **LocalizedResources** -element.

- Enhetlig registrerings-eller inloggnings sida, engelska `<LocalizedResources Id="api.signuporsignin.en">`
- En enhetlig registrerings-eller inloggnings sida, spanska `<LocalizedResources Id="api.signuporsignin.es">`
- Enhetlig registrering eller inloggnings sida, Frankrike `<LocalizedResources Id="api.signuporsignin.fr">`
- Registrera dig, engelska `<LocalizedResources Id="api.localaccountsignup.en">`
- Registrera dig, spanska `<LocalizedResources Id="api.localaccountsignup.es">`
- Registrering, Frankrike `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, engelska `<LocalizedResources Id="api.phonefactor.en">`
- MFA, spanska `<LocalizedResources Id="api.phonefactor.es">`
- MFA, Frankrike `<LocalizedResources Id="api.phonefactor.fr">`

Varje **LocalizedResources** -element innehåller alla nödvändiga **LocalizedStrings** -element med flera **LocalizedString** -element och **LocalizedCollections** -element med flera **LocalizedCollection** -element.  I följande exempel lägger du till en engelsk lokalisering på registrerings sidan:

Obs! det här exemplet gör en referens till `Gender` och `City` anspråks typer. Om du vill använda det här exemplet kontrollerar du att du definierar dessa anspråk. Mer information finns i [ClaimsSchema](claimsschema.md).

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Registrerings sidans lokalisering för spanska.

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>Redigera ContentDefinition för sidan

För varje sida som du vill lokalisera anger du de språk koder som du vill söka efter i **ContentDefinition**.

I följande exempel läggs engelska (en) och spanska (ES) till anpassade strängar till registrerings sidan. **LocalizedResourcesReferenceId** för varje **LocalizedResourcesReference** är detsamma som deras nationella inställningar, men du kan använda valfri sträng som identifierare. För varje språk-och sid kombination pekar du på motsvarande **LocalizedResources** som du skapade tidigare.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

I följande exempel visas den sista XML-koden:

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```
