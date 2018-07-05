---
title: Ändring av lösenord för självbetjäning i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt som visar hur du ställer in ändring av lösenord via Självbetjäning för dina användare i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 028d10b5c005be2db7cfd9c5ca5210ab55f0592a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448143"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: Konfigurera lösenordsändring i anpassade principer  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Med funktionen för ändring av lösenord (med lokala konton) inloggade användare kan ändra sina lösenord utan att behöva bekräfta deras tillförlitlighet med e-Postverifiering enligt beskrivningen i den [lösenordsåterställning via självbetjäning flöde.](active-directory-b2c-reference-sspr.md) Om sessionen upphör att gälla efter den tid som får förändringsflödet för lösenord, uppmanas du att logga in igen. 

## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C-klient som konfigurerats för att slutföra ett lokalt konto registrerings-registreringen/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Så här konfigurerar du ändra lösenordet i anpassad princip

Om du vill konfigurera lösenordsändring i anpassad princip gör följande ändringar i din framework tillägg förtroendeprincipen, 

## <a name="define-a-claimtype-oldpassword"></a>Definiera en ClaimType 'gammalt lösenord ”

Övergripande strukturen för den anpassade principen måste innehålla en `ClaimsSchema`och definiera en ny `ClaimType` oldPassword om du som nedan. 

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

Syftet med de här elementen är följande:

- Den `ClaimsSchema` definierar vilka anspråk verifieras.  I det här fallet kommer att valideras det gamla lösenordet. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Lägg till en lösenordet ändra anspråksprovider med dess element

Anspråk providern om lösenordsändring

1. Autentisera användare mot det gamla lösenordet
2. Och om ”nytt lösenord” matchar ”Bekräfta nytt lösenord', det här värdet lagras i B2C-datalagret och därför att lösenordet har ändrats. 

![bild](images/passwordchange.jpg)

Lägg till följande anspråksleverantören till din princip för tillägg. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Lägg till program-ID: N i en egen princip

Lägg till program-ID i tilläggsfilen (`TrustFrameworkExtensions.xml`):

1. Hitta elementet i tilläggsfilen (TrustFrameworkExtensions.xml) `<TechnicalProfile Id="login-NonInteractive">` och `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Ersätt alla förekomster av `IdentityExperienceFrameworkAppId` med program-ID för programmet för Identitetsupplevelse enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md). Här är ett exempel:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Ersätt alla förekomster av `ProxyIdentityExperienceFrameworkAppId` med program-ID för Proxy Identitetsramverk programmet enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

4. Spara tilläggsfilen.



## <a name="create-a-password-change-user-journey"></a>Skapa en användarresa för ändring av lösenord

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

Du är klar ändra tilläggsfilen. Spara och ladda upp den här filen. Se till att alla verifieringar lyckas.



## <a name="create-a-relying-party-rp-file"></a>Skapa en förlitande part (RP)-fil

Därefter uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade:

1. Göra en kopia av ProfileEdit.xml i din arbetskatalog. Sedan, byta namn (till exempel PasswordChange.xml).
2. Öppna i ny fil och uppdatera den `PolicyId` attributet för `<TrustFrameworkPolicy>` med ett unikt värde. Det här är namnet på din princip (till exempel PasswordChange).
3. Ändra den `ReferenceId` attributet i `<DefaultUserJourney>` så att den matchar den `Id` för nya användarresa som du skapade (till exempel PasswordChange).
4. Spara dina ändringar och överför sedan filen.
5. Om du vill testa den anpassade principen som du laddade upp i Azure-portalen, gå till principbladet och klicka sedan på **kör nu**.




## <a name="link-to-password-change-sample-policy"></a>Länka till exempel för lösenordsändring

Du hittar exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










