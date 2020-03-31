---
title: Konfigurera lösenordskomplexitet med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera krav på lösenordskomplexitet med hjälp av en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138442"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösenordskomplexitet med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du konfigurera komplexitetskraven för lösenord som tillhandahålls av en användare när du skapar ett konto. Som standard använder Azure AD B2C **starka** lösenord. Den här artikeln visar hur du konfigurerar lösenordskomplexitet i [anpassade principer](custom-policy-overview.md). Det är också möjligt att konfigurera lösenordskomplexitet i [användarflöden](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.


## <a name="add-the-elements"></a>Lägg till elementen

Om du vill konfigurera lösenordskomplexiteten åsidosätter du `newPassword` `reenterPassword` [typerna](claimsschema.md) och anspråk med en referens till [predikatvalningar](predicates.md#predicatevalidations). Elementet Predikatvalidationer grupperar en uppsättning predikat för att bilda en verifiering av indata från användaren som kan tillämpas på en anspråkstyp. Öppna filen för tillägg i principen. Till exempel <em> `SocialAndLocalAccounts/` </em>.

1. Sök efter elementet [BuildingBlocks.](buildingblocks.md) Om elementet inte finns lägger du till det.
1. Leta reda på elementet [ClaimsSchema.](claimsschema.md) Om elementet inte finns lägger du till det.
1. Lägg `newPassword` till `reenterPassword` och anspråk i **elementet ClaimsSchema.**

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikat definierar](predicates.md) en grundläggande validering för att kontrollera värdet för en anspråkstyp och returnerar sant eller falskt. Valideringen görs med hjälp av ett angivet metodelement och en uppsättning parametrar som är relevanta för metoden. Lägg till följande predikater i **Elementet BuildingBlocks,** omedelbart `</ClaimsSchema>` efter stängningen av elementet:

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Lägg till följande predikatvalider i **Elementet BuildingBlocks,** `</Predicates>` omedelbart efter stängningen av elementet:

    ```XML
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

1. Följande tekniska profiler är [tekniska profiler i Active Directory](active-directory-technical-profile.md), som läser och skriver data till Azure Active Directory. Åsidosätt dessa tekniska profiler i tilläggsfilen. Används `PersistedClaims` för att inaktivera principen för starkt lösenord. Leta reda på elementet **ClaimsProviders.**  Lägg till följande anspråksleverantörer enligt följande:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Spara principfilen.

## <a name="test-your-policy"></a>Testa din policy

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Identity Experience Framework**.
5. Klicka på **Ladda upp princip**på sidan Anpassade principer.
6. Välj **Skriv över principen om den finns**och sök sedan efter och välj filen *TrustFrameworkExtensions.xml.*
7. Klicka på **Överför**.

### <a name="run-the-policy"></a>Kör principen

1. Öppna registrerings- eller inloggningsprincipen. Till exempel *B2C_1A_signup_signin*.
2. För **Ansökan**väljer du det program som du tidigare har registrerat. Om du vill visa token `https://jwt.ms`ska **svars-URL:en** visas .
3. Klicka på **Kör nu**.
4. Välj **Registrera dig nu,** ange en e-postadress och ange ett nytt lösenord. Vägledning presenteras om lösenordsbegränsningar. Slutför inmatningen av användarinformationen och klicka sedan på **Skapa**. Du bör se innehållet i token som returnerades.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar lösenordsändring med hjälp av anpassade principer i Azure Active Directory B2C](custom-policy-password-change.md).
- Läs mer om elementen [Predikater](predicates.md) och [Predikatvalidationer](predicates.md#predicatevalidations) i IEF-referensen.
