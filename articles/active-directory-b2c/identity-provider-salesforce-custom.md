---
title: Konfigurera inloggning med en Salesforce SAML-leverantör med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med en Salesforce SAML-provider med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67fe9ef4ad2b025d11f88976973658c9cd8ae693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187958"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med en Salesforce SAML-provider med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en Salesforce-organisation med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [SAML-teknisk profil](saml-technical-profile.md) i en anpassad princip.

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Om du inte redan har gjort det registrerar du dig för ett [kostnadsfritt Developer Edition-konto](https://developer.salesforce.com/signup). I den här artikeln används [Salesforce Lightning Experience](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Konfigurera en My Domain](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) för din Salesforce-organisation.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Konfigurera Salesforce som identitetsprovider

1. [Logga in på Salesforce](https://login.salesforce.com/).
2. Expandera **Identitet**under **Inställningar**på den vänstra menyn och välj sedan **Identitetsprovider**.
3. Välj **Aktivera identitetsprovider**.
4. Under **Välj certifikat**väljer du det certifikat som du vill att Salesforce ska använda för att kommunicera med Azure AD B2C. Du kan använda standardcertifikatet.
5. Klicka på **Spara**.

### <a name="create-a-connected-app-in-salesforce"></a>Skapa en ansluten app i Salesforce

1. På sidan **Identitetsprovider** **skapas nu tjänsteleverantörer via anslutna appar. Klicka här.**
2. Under **Grundläggande information**anger du de värden som krävs för den anslutna appen.
3. Markera rutan Aktivera SAML under Inställningar **för Webbappar.** **Web App Settings**
4. Ange följande URL i fältet **Enhetsenhets-ID.** Se till att du `your-tenant` ersätter värdet för med namnet på din Azure AD B2C-klientorganisation.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Ange följande URL i fältet **ACS-URL.** Se till att du `your-tenant` ersätter värdet för med namnet på din Azure AD B2C-klientorganisation.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Bläddra längst ned i listan och klicka sedan på **Spara**.

### <a name="get-the-metadata-url"></a>Hämta metadata-URL:en

1. Klicka på **Hantera**på översiktssidan i den anslutna appen.
2. Kopiera värdet för **slutpunkten för metadataidentifiering**och spara det sedan. Du kommer att använda den senare i den här artikeln.

### <a name="set-up-salesforce-users-to-federate"></a>Konfigurera Salesforce-användare så att de federerar

1. Klicka på **Hantera profiler**på sidan **Hantera** i den anslutna appen .
2. Välj de profiler (eller grupper av användare) som du vill federera med Azure AD B2C. Som systemadministratör markerar du kryssrutan **Systemadministratör** så att du kan federera med ditt Salesforce-konto.

## <a name="generate-a-signing-certificate"></a>Generera ett signeringscertifikat

Begäranden som skickas till Salesforce måste signeras av Azure AD B2C. Om du vill generera ett signeringscertifikat öppnar du Azure PowerShell och kör sedan följande kommandon.

> [!NOTE]
> Se till att du uppdaterar klientnamnet och lösenordet i de två översta raderna.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra certifikatet som du skapade i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **Alternativ** `Upload`väljer du .
7. Ange ett **Namn** för principen. Till exempel SAMLSigningCert. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
8. Bläddra till och välj det B2CSigningCert.pfx-certifikat som du har skapat.
9. Ange **lösenordet** för certifikatet.
3. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med ett Salesforce-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera ett Salesforce-konto som en anspråksleverantör genom att lägga till det i elementet **ClaimsProviders i tilläggsfilen** för din princip. Mer information finns i [definiera en SAML-teknisk profil](saml-technical-profile.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Uppdatera värdet **för PartnerEntity** med url:en för Salesforce-metadata som du kopierade tidigare.
1. Uppdatera värdet för båda instanserna av **StorageReferenceId** till namnet på nyckeln till signeringscertifikatet. Till exempel B2C_1A_SAMLSigningCert.
1. Leta `<ClaimsProviders>` reda på avsnittet och lägg till följande XML-kodavsnitt. Om din princip redan `SM-Saml-idp` innehåller den tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av en enda inloggningssession](custom-policy-reference-sso.md).

    ```XML
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med ditt Salesforce-konto. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings- eller inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har Salesforce-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings- eller inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett LinkedIn-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du just skapade.
2. Lägg till följande element under **ClaimsProviderSelects.** Ange värdet för **TargetClaimsExchangeId** till ett `SalesforceExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett Salesforce-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID** för den tekniska profil som du skapade tidigare. Till exempel `LinkedIn-OAUTH`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande parten (RP) fil som initierar användarens färd som du just skapade:

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInSalesforce.xml*.
2. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInSalesforce`.
3. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den nya användarresan som du skapade (SignUpSignInSalesforce).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrollera att Azure AD B2C-program som du har skapat har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.
