---
title: Konfigurera lösenordsändring med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du aktiverar användare ändra sina lösenord med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a9e58139d248daed19f4fe35c7d33ede9dfe64b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317198"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösenordsändring med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera användare som är inloggad med ett lokalt konto för att ändra sina lösenord utan att behöva sina autentisering av e-Postverifiering i Azure Active Directory (Azure AD) B2C. Om sessionen upphör att gälla när användaren får ändra flöde till lösenordet, uppmanas de att logga in igen. Den här artikeln visar hur du konfigurerar lösenordsändring i [anpassade principer](active-directory-b2c-overview-custom.md). Det är också möjligt att konfigurera [lösenordsåterställning via självbetjäning](active-directory-b2c-reference-sspr.md) för användarflöden.

## <a name="prerequisites"></a>Nödvändiga komponenter

Utför stegen i [Kom igång med anpassade principer i Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Lägg till element 

1. Öppna din *TrustframeworkExtensions.xml* filen och Lägg till följande **ClaimType** element med en identifierare för `oldPassword` till den [ClaimsSchema](claimsschema.md) element: 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. En [ClaimsProvider](claimsproviders.md) elementet innehåller den tekniska profilen som autentiserar användaren. Lägg till följande anspråk providers till den **ClaimsProviders** element:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Ersätt `IdentityExperienceFrameworkAppId` med program-ID för IdentityExperienceFramework programmet som du skapade i den obligatoriska föregående självstudiekursen. Ersätt `ProxyIdentityExperienceFrameworkAppId` med program-ID för ProxyIdentityExperienceFramework programmet som du skapade tidigare.

3. Den [UserJourney](userjourneys.md) elementet definierar sökvägen som användaren vidtar när du interagerar med programmet. Lägg till den **UserJourneys** element om det inte finns med i **UserJourney** identifieras som `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Spara den *TrustFrameworkExtensions.xml* principfil.
5. Kopiera den *ProfileEdit.xml* filen som du hämtade starter-Pack och ge den namnet *ProfileEditPasswordChange.xml*.
6. Öppna i ny fil och uppdatera den **PolicyId** attribut med ett unikt värde. Det här värdet är namnet på din princip. Till exempel *B2C_1A_profile_edit_password_change*.
7. Ändra den **ReferenceId** attributet i `<DefaultUserJourney>` att matcha ID för den nya användarresa som du skapade. Till exempel *PasswordChange*.
8. Spara ändringarna.

Du hittar exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Testa din princip

När du testar dina program i Azure AD B2C kan det vara praktiskt att ha en Azure AD B2C-token som returneras till `https://jwt.ms` för att kunna granska anspråk i den.

### <a name="upload-the-files"></a>Ladda upp filer

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Identitetsramverk**.
5. På sidan anpassade principer **ladda upp principen**.
6. Välj **Skriv över principen om den finns**, och sök sedan efter och välj den *TrustframeworkExtensions.xml* fil.
7. Klicka på **Överför**.
8. Upprepa steg 5 till 7 för filen förlitande part, till exempel *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna den princip som du har ändrat. Till exempel *B2C_1A_profile_edit_password_change*.
2. För **programmet**, Välj ditt program som du tidigare har registrerat. Se token i **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **Kör nu**. Logga in med acouunt som du skapade tidigare. Du bör nu ha möjlighet att ändra lösenordet. 

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du kan [konfigurera lösenordskomplexitet med anpassade principer i Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
