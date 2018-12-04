---
title: Lösenordskomplexitet i anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Så här konfigurerar du komplexitetskrav för lösenord i anpassad princip.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6b8312a08d1d92bccf70e7d3dda5f01811b4f87
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848535"
---
# <a name="configure-password-complexity-in-custom-policies"></a>Konfigurera lösenordskomplexitet i anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln är en avancerad beskrivning av hur lösenordskomplexitet fungerar och är aktiverat Azure AD B2C anpassade principer.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurera komplexitetskrav för lösenord

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitetskrav för lösenord som tillhandahålls av en slutanvändare när du skapar ett konto.  Azure AD B2C använder som standard **starka** lösenord.  Azure AD-B2C stöder också alternativ för att styra komplexitet och deras lösenord som kunder kan använda.  Den här artikeln handlar om hur du konfigurerar lösenordskomplexitet i anpassade principer.  Det är också möjligt att använda [konfigurera lösenordskomplexitet i inbyggda principer](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto registrerings-registreringen/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Så här konfigurerar du lösenordskomplexitet i anpassad princip

Om du vill konfigurera lösenordskomplexitet i anpassad princip för allmänna strukturen för den anpassade principen måste innehålla en `ClaimsSchema`, `Predicates`, och `InputValidations` -element inuti `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Syftet med de här elementen är följande:

- Varje `Predicate` elementet definierar en grundläggande sträng verifieringskontroll som returnerar SANT eller FALSKT.
- Den `InputValidations` elementet har en eller flera `InputValidation` element.  Varje `InputValidation` skapas med hjälp av en serie `Predicate` element. Det här elementet kan du utföra booleskt aggregeringar (liknar `and` och `or`).
- Den `ClaimsSchema` definierar vilka anspråk verifieras.  Den definierar vilket sedan `InputValidation` regel används för att verifiera det vidare.

### <a name="defining-a-predicate-element"></a>Definiera ett predikat element

Predikat har två typer: IsLengthRange eller MatchesRegex. Vi ska gå igenom ett exempel på var och en.  Först har vi ett exempel på MatchesRegex som används för att matcha ett reguljärt uttryck.  I det här exemplet matchar den sträng som innehåller tal.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Nästa vi ska gå igenom ett exempel på IsLengthRange.  Den här metoden tar ett lägsta och högsta stränglängden.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Använd den `HelpText` attribut för att skapa ett felmeddelande visas för slutanvändare om den inte.  Den här strängen kan lokaliseras med hjälp av den [språkanpassningsfunktionen](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definiera ett InputValidation-element

En `InputValidation` är en sammansättning av `PredicateReferences`. Varje `PredicateReferences` måste vara sant för att den `InputValidation` ska lyckas.  Men i den `PredicateReferences` elementet används ett attribut kallas `MatchAtLeast` att ange hur många `PredicateReference` kontroller måste returnera true.  Du kan också definiera en `HelpText` attribut för att åsidosätta ett felmeddelande som definierats i den `Predicate` element som den refererar till.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>Definiera ett ClaimsSchema-element

Anspråkstyper `newPassword` och `reenterPassword` betraktas som särskilda så behöver inte ändra namnen.  Användargränssnittet validerar användaren korrekt reentered sitt lösenord när kontot skapas baserat på dessa `ClaimType` element.  Du hittar samma `ClaimType` element, titta i TrustFrameworkBase.xml i din startpaket.  Vad är nytt i det här exemplet är att vi åsidosätta de här elementen att definiera en `InputValidationReference`. Den `ID` attributet för det här nya elementet pekar på den `InputValidation` element som vi definierade.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Färdigställa allt

Det här exemplet visar hur alla delar hänger ihop för att bilda en aktiv princip.  Använda det här exemplet:

1. Följ instruktionerna i uppfyller [komma igång](active-directory-b2c-get-started-custom.md) för att hämta, konfigurera och överföra TrustFrameworkBase.xml och TrustFrameworkExtensions.xml
1. Skapa en SignUporSignIn.xml-fil med hjälp av exemplen i det här avsnittet.
1. Uppdatera SignUporSignIn.xml ersätter `yourtenant` med namnet på din Azure AD B2C-klientorganisation.
1. Ladda upp principfilen som SignUporSignIn.xml senast.

Det här exemplet innehåller en verifiering för PIN-kod lösenord och en för starka lösenord:

- Leta efter `PINpassword`. Detta `InputValidation` elementet verifierar en pinkod som helst.  Den används inte för tillfället eftersom det inte refereras i den `InputValidationReference` -element inuti `ClaimType`. 
- Leta efter `PasswordValidation`. Detta `InputValidation` elementet verifierar ett lösenord är 8 och 16 tecken och innehåller 3 av 4 i versaler, gemener, siffror eller symboler.  Den refereras i `ClaimType`.  Den här regeln gäller därför i den här principen.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
