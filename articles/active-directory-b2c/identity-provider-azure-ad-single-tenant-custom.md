---
title: Konfigurera inloggning med ett Azure AD-konto med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med ett Azure Active Directory-konto i Azure Active Directory B2C med hjälp av anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678099"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Azure Active Directory-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln visas hur du aktiverar inloggning för användare från en Azure Active Directory-organisation (Azure AD) med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra programnyckeln som du skapade i din Azure AD B2C-klientorganisation.

1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **Principer**väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. Ange ett **namn** för principnyckeln. Till exempel `ContosoAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln när det skapas, så dess referens i XML i följande avsnitt är att *B2C_1A_ContosoAppSecret*.
1. I **Secret**anger du din klienthemlighet som du spelade in tidigare.
1. För **nyckelanvändning** `Signature`väljer du .
1. Välj **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med Azure AD måste du definiera Azure AD som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera Azure AD som anspråksprovider genom att lägga till Azure AD i **ClaimsProvider-elementet** i tilläggsfilen för din princip.

1. Öppna filen *TrustFrameworkExtensions.xml.*
2. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Under **elementet ClaimsProvider** uppdaterar du värdet för **Domän** till ett unikt värde som kan användas för att skilja det från andra identitetsleverantörer. Till exempel `Contoso`. Du placerar inte `.com` en i slutet av den här domäninställningen.
5. Uppdatera värdet för **DisplayName** till ett eget namn för anspråksprovidern under elementet **ClaimsProvider.** Det här värdet används för närvarande inte.

### <a name="update-the-technical-profile"></a>Uppdatera den tekniska profilen

För att få en token från Azure AD-slutpunkten måste du definiera de protokoll som Azure AD B2C ska använda för att kommunicera med Azure AD. Detta görs inuti **TechnicalProfile-delen** av **ClaimsProvider**.

1. Uppdatera ID:n för elementet **TechnicalProfile.** Det här ID:t används för att referera till den `OIDC-Contoso`här tekniska profilen från andra delar av principen, till exempel .
1. Uppdatera värdet för **DisplayName**. Det här värdet visas på inloggningsknappen på inloggningsskärmen.
1. Uppdatera värdet för **Beskrivning**.
1. Azure AD använder OpenID Connect-protokollet, så **Protocol** se `OpenIdConnect`till att värdet för protokollet är .
1. Ange värdet **METADATA** för `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`METADATA `tenant-name` till , var är ditt Azure AD-klientnamn. Till exempel, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Ange **client_id** till program-ID:et från programregistreringen.
1. Under **CryptographicKeys**uppdaterar du värdet **för StorageReferenceId** till namnet på principnyckeln som du skapade tidigare. Till exempel `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med din Azure AD-katalog. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
1. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
1. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är ännu inte tillgänglig på någon av inloggnings-/inloggningssidorna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den även har Azure AD-identitetsprovidern:

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
1. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
1. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
1. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
1. Byt namn på ID för användarens färd. Till exempel `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsprovider på en inloggnings-/inloggningssida. Om du lägger till ett **ClaimsProviderSelection-element** för Azure AD visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade i *TrustFrameworkExtensions.xml*.
1. Lägg till följande element under **ClaimsProviderSelections.** Ange värdet för **TargetClaimsExchangeId** till ett `ContosoExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
1. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID** för den tekniska profil som du skapade tidigare. Till exempel `OIDC-Contoso`.

1. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInContoso.xml*.
1. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInContoso`.
1. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Till exempel `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den användarresa som du skapade tidigare. Registrera till exempel *SignUpSignInContoso*.
1. Spara ändringarna och ladda upp filen.
1. Välj den nya principen i listan under **Anpassade principer.**
1. I listrutan **Välj program** väljer du Azure AD B2C-programmet som du skapade tidigare. Till exempel *testapp1*.
1. Kopiera **slutpunkten Kör nu** och öppna den i ett privat webbläsarfönster, till exempel Incognito-läge i Google Chrome eller ett InPrivate-fönster i Microsoft Edge. Om du öppnar i ett privat webbläsarfönster kan du testa den fullständiga användarresan genom att inte använda några för närvarande cachelagrade Azure AD-autentiseringsuppgifter.
1. Välj inloggningsknappen för Azure AD, till exempel *Contoso Employee*, och ange sedan autentiseringsuppgifterna för en användare i din Azure AD-organisationsklient. Du uppmanas att godkänna programmet och ange sedan information för din profil.

Om inloggningsprocessen lyckas omdirigeras webbläsaren till `https://jwt.ms`, som visar innehållet i token som returneras av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att diagnostisera problem kan du tillfälligt placera principen i "utvecklarläge" och samla in loggar med Azure Application Insights. Ta reda på hur i [Azure Active Directory B2C: Samla in loggar](troubleshoot-with-application-insights.md).
