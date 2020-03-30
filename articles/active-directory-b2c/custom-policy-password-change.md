---
title: Konfigurera lösenordsändring med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar användare att ändra sitt lösenord med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189182"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera lösenordsändring med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) kan du göra det möjligt för användare som är inloggade med ett lokalt konto att ändra sitt lösenord utan att behöva bevisa sin äkthet via e-postverifiering. Om sessionen går ut när användaren kommer till lösenordets ändringsflöde uppmanas han att logga in igen. Den här artikeln visar hur du konfigurerar lösenordsändring i [anpassade principer](custom-policy-overview.md). Det är också möjligt att konfigurera [självbetjäningslösenordsåterställning](user-flow-self-service-password-reset.md) för användarflöden.

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer i Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Lägg till elementen

1. Öppna filen *TrustframeworkExtensions.xml* och lägg till följande **ClaimType-element** med en identifierare `oldPassword` för i [ClaimsSchema-elementet:](claimsschema.md)

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

2. Ett [ClaimsProvider-element](claimsproviders.md) innehåller den tekniska profilen som autentiserar användaren. Lägg till följande anspråksleverantörer i elementet **ClaimsProviders:**

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

    Ersätt `IdentityExperienceFrameworkAppId` med program-ID för IdentityExperienceFramework-programmet som du skapade i den nödvändiga självstudien. Ersätt `ProxyIdentityExperienceFrameworkAppId` med program-ID för ProxyIdentityExperienceFramework-programmet som du också tidigare har skapat.

3. [UserJourney-elementet](userjourneys.md) definierar sökvägen som användaren tar när den interagerar med ditt program. Lägg till **UserJourneys-elementet** om det inte finns `PasswordChange`med **UserJourney** identifierad som :

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

4. Spara principfilen *TrustFrameworkExtensions.xml.*
5. Kopiera *filen ProfileEdit.xml* som du hämtade med startpaketet och namnge den *ProfileEditPasswordChange.xml*.
6. Öppna den nya filen och uppdatera **Attributet PolicyId** med ett unikt värde. Det här värdet är namnet på din princip. Till exempel *B2C_1A_profile_edit_password_change*.
7. Ändra **attributet ReferenceId** för `<DefaultUserJourney>` att matcha ID:t för den nya användarresan som du skapade. Till exempel *PasswordChange*.
8. Spara ändringarna.

Du hittar exempelpolicyn [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Testa din policy

När du testar dina program i Azure AD B2C kan det vara bra `https://jwt.ms` att ha Azure AD B2C-token returnerad för att kunna granska anspråken i den.

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Identity Experience Framework**.
5. Klicka på **Ladda upp princip**på sidan Anpassade principer.
6. Välj **Skriv över principen om den finns**och sök sedan efter och välj filen *TrustframeworkExtensions.xml.*
7. Klicka på **Överför**.
8. Upprepa steg 5 till 7 för den förlitande parten filen, till exempel *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Kör principen

1. Öppna principen som du har ändrat. Till exempel *B2C_1A_profile_edit_password_change*.
2. För **Ansökan**väljer du det program som du tidigare har registrerat. Om du vill visa token `https://jwt.ms`ska **svars-URL:en** visas .
3. Klicka på **Kör nu**. Logga in med den acouunt som du tidigare skapade. Du bör nu ha möjlighet att ändra lösenordet.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du kan [konfigurera lösenordskomplexitet med hjälp av anpassade principer i Azure Active Directory B2C](custom-policy-password-complexity.md).
