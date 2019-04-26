---
title: Konfigurera lösenordskomplexitet med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Hur du konfigurerar kraven på lösenordskomplexitet med en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: e8e3157bc9dfc97d364effee2ea90cfad85d18ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317147"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösenordskomplexitet med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan konfigurera komplexitetskrav för lösenord som tillhandahålls av en användare när du skapar ett konto i Azure Active Directory (Azure AD) B2C. Azure AD B2C använder som standard **starka** lösenord. Den här artikeln visar hur du konfigurerar lösenordskomplexitet i [anpassade principer](active-directory-b2c-overview-custom.md). Det är också möjligt att konfigurera lösenordskomplexitet i [användarflöden](active-directory-b2c-reference-password-complexity.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför stegen i [Kom igång med anpassade principer i Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Lägg till element

1. Kopiera den *SignUpOrSignIn.xml* filen som du hämtade starter-Pack och ge den namnet *SingUpOrSignInPasswordComplexity.xml*.
2. Öppna den *SingUpOrSignInPasswordComplexity.xml* filen och ändra den **PolicyId** och **PublicPolicyUri** till ett nytt principnamn. Till exempel *B2C_1A_signup_signin_password_complexity*.
3. Lägg till följande **ClaimType** element med identifierare för `newPassword` och `reenterPassword`:

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

4. [Predikat](predicates.md) har metoden typer av `IsLengthRange` eller `MatchesRegex`. Den `MatchesRegex` typen används för att matcha ett reguljärt uttryck. Den `IsLengthRange` typen tar en lägsta och högsta stränglängden. Lägg till en **predikat** elementet så att den **BuildingBlocks** element om det inte finns med följande **predikat** element:

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

5. Varje **InputValidation** elementet skapas med hjälp av det definierade **predikat** element. Det här elementet kan du utföra booleskt aggregeringar som liknar `and` och `or`. Lägg till en **InputValidations** elementet så att den **BuildingBlocks** element om det inte finns med följande **InputValidation** element:

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

6. Se till att den **PolicyProfile** tekniska profilen innehåller följande element:

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

7. Spara principfilen.

## <a name="test-your-policy"></a>Testa din princip

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha en Azure AD B2C-token som returneras till `https://jwt.ms` för att kunna granska anspråk i den.

### <a name="upload-the-files"></a>Ladda upp filer

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Identitetsramverk**.
5. På sidan anpassade principer **ladda upp principen**.
6. Välj **Skriv över principen om den finns**, och sök sedan efter och välj den *SingUpOrSignInPasswordComplexity.xml* fil.
7. Klicka på **Överför**.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_signup_signin_password_complexity*.
2. För **programmet**, Välj ditt program som du tidigare har registrerat. Se token i **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **Kör nu**.
4. Välj **registrera dig nu**, ange en e-postadress och ange ett nytt lösenord. Vägledning som visas på begränsningar för lösenord. Användarinformationen är klar och klicka sedan på **skapa**. Du bör se innehållet i den token som returnerades.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurera lösenordsändring med anpassade principer i Azure Active Directory B2C](active-directory-b2c-reference-password-change-custom.md).


