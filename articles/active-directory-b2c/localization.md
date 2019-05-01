---
title: Localization - Azure Active Directory B2C | Microsoft Docs
description: Ange det lokalisering elementet i en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2291bdd270a15c4932d79b124616400d2667c891
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705847"
---
# <a name="localization"></a>Lokalisering

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den **lokalisering** elementet kan du stöd för flera språk eller språk i principen för användaren resor. Lokaliseringsstöd i principer kan du:

- Ställ in explicit listan över språk som stöds i en princip och välja ett standardspråk.
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

Den **lokalisering** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Enabled | Nej | Möjliga värden: `true` eller `false`. |

Den **lokalisering** elementet innehåller följande XML-element

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Lista över språk som stöds. | 
| LocalizedResources | 0: n | Lista över lokaliserade resurser. |

## <a name="supportedlanguages"></a>SupportedLanguages

Den **SupportedLanguages** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | Språk som ska användas som standard för lokaliserade resurser. |
| MergeBehavior | Nej | En uppräkningsvärdena med värden som slås samman tillsammans med eventuella ClaimType finns i en överordnad-princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som angetts i basprincipen. Möjliga värden: `Append`, `Prepend`, eller `ReplaceAll`. Den `Append` värdet anger att mängden data som finns ska läggas till slutet av den samling som anges i den överordnade principen. Den `Prepend` värdet anger att mängden data som finns bör läggas innan den samling som anges i den överordnade principen. Den `ReplaceAll` värdet anger att insamlingen av data som definierats i den överordnade principen ska ignoreras, i stället använda de data som definieras i den aktuella principen. |

### <a name="supportedlanguages"></a>SupportedLanguages

Den **SupportedLanguages** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Visar innehåll som överensstämmer med en språktagg per RFC 5646 - taggar för identifiering av språk. | 

## <a name="localizedresources"></a>LocalizedResources

Den **LocalizedResources** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för att unikt identifiera lokaliserade resurser. |

Den **LocalizedResources** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definierar hela samlingar i olika kulturer. En samling kan ha olika antal objekt och olika strängar för olika kulturer. Exempel på samlingar är uppräkningar som visas i anspråkstyper. Exempelvis visas en lista för land/region för användaren i en nedrullningsbar listruta. |
| LocalizedStrings | 0: n | Definierar alla strängar, förutom de strängar som visas i samlingar i olika kulturer. |

### <a name="localizedcollections"></a>LocalizedCollections

Den **LocalizedCollections** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Lista över språk som stöds. |

#### <a name="localizedcollection"></a>LocalizedCollection

Den **LocalizedCollection** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | Refererar till ett ClaimType element eller en användargränssnittselement i policyfilen. |
| ElementId | Ja | En sträng som innehåller en referens till en Anspråkstyp redan definierats i avsnittet ClaimsSchema som används om **ElementType** är inställd på en ClaimType. |
| TargetCollection | Ja | Målsamlingen. |

Den **LocalizedCollection** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Definierar ett tillgängligt alternativ för att användaren väljer för ett anspråk i användargränssnittet, till exempel ett värde i en listruta. |

Den **objekt** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Användarvänligt visningssträngen som ska visas för användaren i användargränssnittet för det här alternativet. |
| Värde | Ja | Strängen anspråksvärde som är associerade med det här alternativet. |

I följande exempel visar användningen av den **LocalizedCollections** element. Den innehåller två **LocalizedCollection** element, en för engelska och en annan för spanska. Båda in det **begränsning** samling anspråket `Gender` med en lista över objekt för engelska och spanska.

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

Den **LocalizedStrings** elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | En lokaliserad sträng. |

Den **LocalizedString** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | En referens till ett anspråk typen element eller en användargränssnittselement i principen. Möjliga värden: `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate`, eller. Den `ClaimType` värde används för att lokalisera ett av attributen anspråk som anges i StringId. Den `UxElement` värde används för att lokalisera en av användargränssnittselement som anges i StringId. Den `ErrorMessage` värdet används för att hitta något av system-felmeddelanden som anges i StringId. Den `Predicate` värde används för att lokalisera en av de [predikat](predicates.md) felmeddelanden som anges i StringId. Den `InputValidation` värde används för att lokalisera en av de [PredicateValidation](predicates.md) gruppen felmeddelanden som anges i StringId. |
| ElementId | Ja | Om **ElementType** är inställd på `ClaimType`, `Predicate`, eller `InputValidation`, det här elementet innehåller en referens till en Anspråkstyp som redan har definierats i avsnittet ClaimsSchema. | 
| StringId | Ja | Om **ElementType** är inställd på `ClaimType`, det här elementet innehåller en referens till ett attribut för en anspråkstyp. Möjliga värden: `DisplayName`, `AdminHelpText`, eller `PatternHelpText`. Den `DisplayName` värde används för att ange visningsnamn för anspråk. Den `AdminHelpText` värde används för att ange användarens anspråk hjälp text-namn. Den `PatternHelpText` värde används för att ange hjälptexten anspråk mönster. Om **ElementType** är inställd på `UxElement`, det här elementet innehåller en referens till ett attribut för en objektet i användargränssnittet. Om **ElementType** är inställd på `ErrorMessage`, det här elementet anger identifieraren för ett felmeddelande. Se [lokalisering sträng ID: N](localization-string-ids.md) för en fullständig lista över de `UxElement` identifierare.|


I följande exempel visas en lokaliserad registreringssidan. De första tre **LocalizedString** attributet anspråk för värden. Tredje ändrar värdet för knappen Fortsätt. Den sista som ändrar ett felmeddelande.

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

I följande exempel visas en lokaliserad den **UserHelpText** av **predikat** med Id `IsLengthBetween8And64`. Och en lokaliserad **UserHelpText** av **PredicateGroup** med Id `CharacterClasses` av **PredicateValidation** med Id `StrongPassword`.

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

Den här artikeln visar hur du stöd för flera språk eller språk i principen för användaren resor. Lokalisering kräver tre steg: konfigurera explicita listan över språk som stöds anger språkspecifika strängar och samlingar och redigera ContentDefinition för sidan.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Konfigurera explicita listan över språk som stöds

Under den **BuildingBlocks** element, lägga till den **lokalisering** element med listan över språk som stöds. I följande exempel visas hur du definierar lokaliseringsstöd för både engelska (standard) och spanska:

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>Ange språkspecifika strängar och samlingar 

Lägg till **LocalizedResources** elementen i den **lokalisering** element efter den **SupportedLanguages** element. Du lägger till **LocalizedResources** -element för varje sida (innehållsdefinition) och vilket språk som du vill stödja. Lägg till följande för att anpassa sidan sida för enhetlig registrering eller inloggning, registrering och Multi-Factor authentication (MFA) för engelska, spanska och Frankrike, **LocalizedResources** element.  
- Enhetlig registrering eller inloggning sidan, engelska `<LocalizedResources Id="api.signuporsignin.en">`
- Enhetlig registrering eller inloggning sidan spanska `<LocalizedResources Id="api.signuporsignin.es">`
- Enhetlig registrering eller inloggning sidan, Frankrike `<LocalizedResources Id="api.signuporsignin.fr">` 
- Registrering, engelska `<LocalizedResources Id="api.localaccountsignup.en">`
- Registrering, spanska `<LocalizedResources Id="api.localaccountsignup.es">`
- Registrering för, Frankrike `<LocalizedResources Id="api.localaccountsignup.fr">`
- MFA, engelska `<LocalizedResources Id="api.phonefactor.en">`
- MFA, spanska `<LocalizedResources Id="api.phonefactor.es">`
- MFA, Frankrike `<LocalizedResources Id="api.phonefactor.fr">`

Varje **LocalizedResources** elementet innehåller alla de nödvändiga **LocalizedStrings** element med flera **LocalizedString** element och  **LocalizedCollections** element med flera **LocalizedCollection** element.  I följande exempel lägger du till registreringssidan för engelska lokalisering: 

Obs! Det här exemplet gör en referens till `Gender` och `City` anspråkstyper. Om du vill använda det här exemplet, kontrollera att du definierar dessa krav. Mer information finns i [ClaimsSchema](claimsschema.md).

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

Registreringssida för lokalisering för spanska.

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

Ange språkkoder ska sökas efter i för varje sida som du vill lokalisera den **ContentDefinition**.

I följande exempel läggs till registreringssidan för engelska (en) och spanska (es) anpassade strängar. Den **LocalizedResourcesReferenceId** för var och en **LocalizedResourcesReference** är samma som deras nationella inställningar, men du kan använda valfri sträng som identifierare. För varje språk och sidan kombination kan peka till motsvarande **LocalizedResources** du skapade tidigare.

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

I följande exempel visas den slutliga XML:

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




