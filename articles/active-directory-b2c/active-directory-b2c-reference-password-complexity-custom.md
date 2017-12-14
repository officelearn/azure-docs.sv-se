---
title: "Lösenordskomplexitet i anpassade principer - Azure AD B2C | Microsoft Docs"
description: "Konfigurera komplexitetskrav för lösenord i en anpassad princip"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: mtillman
editor: parakhj
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: eb187a120399089f1c3c145a06fbe993f50fb92b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-password-complexity-in-custom-policies"></a>Konfigurera lösenordskomplexitet i anpassade principer

> [!NOTE]
> **Den här funktionen är i förhandsgranskningen.**  Kontakta [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) ha din testklienten aktiverad med den här funktionen.  Testa detta inte på klienter för produktion.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln är en avancerad beskrivning av hur lösenordskomplexitet fungerar och är aktiverat Azure AD B2C anpassade principer.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurera komplexitetskrav för lösenord

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitetskrav för lösenord som tillhandahålls av en slutanvändare när du skapar ett konto.  Som standard använder Azure AD B2C **starka** lösenord.  Azure AD B2C stöder även konfigurationsalternativ för att styra komplexitet för lösenord som kunder kan använda.  Den här artikeln handlar om hur du konfigurerar lösenordskomplexitet i anpassade principer.  Det är också möjligt att använda [konfigurera lösenordskomplexitet i inbyggda principer](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Krav

En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto sign-upp/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>Konfigurera lösenordskomplexitet i anpassad princip

Om du vill konfigurera lösenordskomplexitet i anpassad princip för allmänna strukturen för den anpassade principen måste innehålla en `ClaimsSchema`, `Predicates`, och `InputValidations` -element inuti `BuildingBlocks`.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

Syftet med de här elementen är följande:

- Varje `Predicate` elementet definierar en grundläggande sträng valideringskontrollen som returnerar true eller false.
- Den `InputValidations` element har en eller flera `InputValidation` element.  Varje `InputValidation` har skapats med hjälp av en serie `Predicate` element. Det här elementet kan du utföra booleskt aggregeringar (liknar `and` och `or`).
- Den `ClaimsSchema` definierar vilka anspråk som ska verifieras.  Den definierar vilket sedan `InputValidation` regeln används för att verifiera att anspråk.

### <a name="defining-a-predicate-element"></a>Definiera ett predikat element

Predikat har två typer: IsLengthRange eller MatchesRegex. Nu ska vi se ett exempel på varje.  Först har vi ett exempel på MatchesRegex som används för att matcha ett reguljärt uttryck.  I det här exemplet matchar den sträng som innehåller tal.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

Nästa nu ska vi se ett exempel på IsLengthRange.  Den här metoden tar en lägsta och högsta string-längden.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

Använd den `HelpText` attribut för att ge användarna ett felmeddelande om den inte.  Den här strängen kan lokaliseras med hjälp av den [språk anpassning av funktionen](active-directory-b2c-reference-language-customization.md).

### <a name="defining-an-inputvalidation-element"></a>Definiera ett InputValidation-element

En `InputValidation` är en sammanställning av `PredicateReferences`. Varje `PredicateReferences` måste vara uppfyllda för att den `InputValidation` ska lyckas.  Men i den `PredicateReferences` element använda attributet kallas `MatchAtLeast` att ange hur många `PredicateReference` kontrollerar måste returnera true.  Du kan också definiera en `HelpText` attribut för att åsidosätta felmeddelandet som definierats i den `Predicate` element som den refererar till.

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

Anspråkstyper `newPassword` och `reenterPassword` betraktas som särskilda så behöver inte ändra namnen.  Användargränssnittet validerar användaren korrekt igen sitt lösenord när kontot skapas baserat på dessa `ClaimType` element.  Du hittar samma `ClaimType` element, leta i TrustFrameworkBase.xml i din startpaket.  Vad är nytt i det här exemplet är att vi åsidosätts de här elementen att definiera en `InputValidationReference`. Den `ID` attributet för den här nya-elementet pekar på den `InputValidation` element som vi har definierat.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>Alltihop

Det här exemplet illustrerar hur alla delar hänger ihop för att bilda en fungerande princip.  Använda det här exemplet:

1. Följ instruktionerna i krav [komma igång](active-directory-b2c-get-started-custom.md) för att hämta, konfigurera och överföra TrustFrameworkBase.xml och TrustFrameworkExtensions.xml
1. Skapa en SignUporSignIn.xml-fil med exempel innehållet i det här avsnittet.
1. Uppdatera SignUporSignIn.xml ersätta `yourtenant` med namn på din Azure AD B2C-klient.
1. Överför principfilen SignUporSignIn.xml senast.

Det här exemplet innehåller en verifiering för PIN-kod lösenord och en för starka lösenord:

- Leta efter `PINpassword`. Detta `InputValidation` element verifierar en pinkod i längd.  Den används inte för tillfället eftersom den inte har refererats i den `InputValidationReference` -element inuti `ClaimType`. 
- Leta efter `PasswordValidation`. Detta `InputValidation` element validerar lösenord är 8 och 16 tecken och innehåller 3 4 i versaler, gemener, siffror eller symboler.  Den refereras i `ClaimType`.  Därför är den här regeln tillämpas i den här principen.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
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
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
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
