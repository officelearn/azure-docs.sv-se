---
title: Konfigurera lösen ords komplexitet med anpassade principer
titleSuffix: Azure AD B2C
description: Så här konfigurerar du krav för lösen ords komplexitet med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85389011"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösen ords komplexitet med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du konfigurera komplexitets kraven för lösen ord som tillhandahålls av en användare när ett konto skapas. Som standard använder Azure AD B2C **starka** lösen ord. Den här artikeln visar hur du konfigurerar lösen ords komplexitet i [anpassade principer](custom-policy-overview.md). Det är också möjligt att konfigurera lösen ords komplexitet i [användar flöden](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Krav

Slutför stegen i [Kom igång med anpassade principer](custom-policy-get-started.md). Du bör ha en fungerande anpassad princip för registrering och inloggning med lokala konton.


## <a name="add-the-elements"></a>Lägg till elementen

Om du vill konfigurera lösen ords komplexiteten åsidosätter du- `newPassword` och- `reenterPassword` [anspråks typerna](claimsschema.md) med en referens till [predikat-valideringar](predicates.md#predicatevalidations). PredicateValidations-elementet grupperar en uppsättning predikat som utgör en verifiering av användarindata som kan tillämpas på en anspråks typ. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till- `newPassword` och- `reenterPassword` anspråk i **ClaimsSchema** -elementet.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikat](predicates.md) definierar en grundläggande verifiering för att kontrol lera värdet för en anspråks typ och returnerar true eller false. Verifieringen görs med hjälp av ett angivet metod element och en uppsättning parametrar som är relevanta för metoden. Lägg till följande predikat i **BuildingBlocks** -elementet, direkt efter stängningen av `</ClaimsSchema>` elementet:

    ```xml
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

1. Lägg till följande predikat verifieringar i **BuildingBlocks** -elementet, direkt efter stängningen av `</Predicates>` elementet:

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

1. Följande tekniska profiler är [Active Directory tekniska profiler](active-directory-technical-profile.md)som läser och skriver data till Azure Active Directory. Åsidosätt dessa tekniska profiler i tilläggs filen. Använd `PersistedClaims` för att inaktivera principen för starkt lösen ord. Hitta **ClaimsProviders** -elementet.  Lägg till följande anspråks leverantörer enligt följande:

    ```xml
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

1. Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns**och Sök sedan efter och välj den *TrustFrameworkExtensions.xml* filen.
7. Klicka på **Överför**.

### <a name="run-the-policy"></a>Kör principen

1. Öppna registrerings-eller inloggnings principen. Till exempel *B2C_1A_signup_signin*.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token ska **svars-URL:** en visas `https://jwt.ms` .
3. Klicka på **Kör nu**.
4. Välj **Registrera dig nu**, ange en e-postadress och ange ett nytt lösen ord. Vägledning ges om lösen ords begränsningar. Ange användar informationen och klicka sedan på **skapa**. Du bör se innehållet i den token som returnerades.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar ändring av lösen ord med anpassade principer i Azure Active Directory B2C](custom-policy-password-change.md).
- Lär dig mer om [predikat](predicates.md) och [PredicateValidations](predicates.md#predicatevalidations) -elementen i IEF-referensen.
