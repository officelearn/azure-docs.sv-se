---
title: Konfigurera lösen ords komplexitet med anpassade principer
titleSuffix: Azure AD B2C
description: Så här konfigurerar du krav för lösen ords komplexitet med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8718a04f9d63897b2d2472dd0cdffb196c41435
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949798"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösen ords komplexitet med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du konfigurera komplexitets kraven för lösen ord som tillhandahålls av en användare när ett konto skapas. Som standard använder Azure AD B2C **starka** lösen ord. Den här artikeln visar hur du konfigurerar lösen ords komplexitet i [anpassade principer](active-directory-b2c-overview-custom.md). Det är också möjligt att konfigurera lösen ords komplexitet i [användar flöden](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Krav

Slutför stegen i [Kom igång med anpassade principer i Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Lägg till elementen

1. Kopiera filen *SignUpOrSignIn. XML* som du laddade ned med start paketet och ge den namnet *SingUpOrSignInPasswordComplexity. XML*.
2. Öppna filen *SingUpOrSignInPasswordComplexity. XML* och ändra **PolicyId** och **PublicPolicyUri** till ett nytt princip namn. Till exempel *B2C_1A_signup_signin_password_complexity*.
3. Lägg till följande **claimType** -element med identifierare för `newPassword` och `reenterPassword`:

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

4. [Predikat](predicates.md) har metod typer för `IsLengthRange` eller `MatchesRegex`. `MatchesRegex` typen används för att matcha ett reguljärt uttryck. `IsLengthRange` typen tar en minsta och högsta sträng längd. Lägg till ett **predikat** -element i **BuildingBlocks** -elementet om det inte finns med följande **predikat** element:

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. Varje **InputValidation** -element konstrueras med hjälp av definierade **predikat** -element. Med det här elementet kan du utföra booleska agg regeringar som liknar `and` och `or`. Lägg till ett **InputValidations** -element i **BuildingBlocks** -elementet om det inte finns med följande **InputValidation** -element:

    ```XML
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
    </InputValidations>
    ```

6. Kontrol lera att den tekniska profilen **PolicyProfile** innehåller följande element:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha Azure AD B2C-token som returneras till `https://jwt.ms` för att kunna granska anspråk i det.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns**och Sök sedan efter och välj *SingUpOrSignInPasswordComplexity. XML-* filen.
7. Klicka på **Överför**.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin_password_complexity*.
2. För **program**väljer du ditt program som du har registrerat tidigare. Om du vill se token ska **svars-URL:** en Visa `https://jwt.ms`.
3. Klicka på **Kör nu**.
4. Välj **Registrera dig nu**, ange en e-postadress och ange ett nytt lösen ord. Vägledning ges om lösen ords begränsningar. Ange användar informationen och klicka sedan på **skapa**. Du bör se innehållet i den token som returnerades.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar ändring av lösen ord med anpassade principer i Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


