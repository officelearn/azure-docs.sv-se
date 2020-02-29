---
title: Konfigurera inloggning med ett Twitter-konto genom att använda anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med ett Twitter-konto genom att använda anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85af3457f83f06c107f8b4aa9bd88a9f915c776f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187941"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Twitter-konto genom att använda anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av ett Twitter-konto genom att använda [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Förutsättningar

- Slutför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Om du inte redan har ett Twitter-konto skapar du ett på [Twitter-registrerings sidan](https://twitter.com/signup).

## <a name="create-an-application"></a>Skapa ett program

Om du vill använda Twitter som identitets leverantör i Azure AD B2C måste du skapa ett Twitter-program.

1. Logga in på webbplatsen för [Twitter-utvecklare](https://developer.twitter.com/en/apps) med dina Twitter-kontoautentiseringsuppgifter.
2. Välj **skapa en app**.
3. Ange ett **namn på appen** och en **program beskrivning**.
4. I **webbplats-URL**anger du `https://your-tenant.b2clogin.com`. Ersätt `your-tenant` med namnet på din klient. Till exempel https://contosob2c.b2clogin.com.
5. Ange `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`för **återanrops-URL: en**. Ersätt `your-tenant` med namnet på ditt klient namn och `your-policy-Id` med identifieraren för din princip. Till exempel `b2c_1A_signup_signin_twitter`. Du måste använda små bokstäver när du anger ditt klient namn även om klienten har definierats med versaler i Azure AD B2C.
6. Längst ned på sidan, Läs och godkänn villkoren och välj sedan **skapa**.
7. På sidan **information om appar** väljer du **Redigera > Redigera information**, markerar kryss rutan **Aktivera inloggning med Twitter**och väljer sedan **Spara**.
8. Välj **nycklar och tokens** och registrera konsument- **API-nyckeln** och de **hemliga nyckel** värden för konsument-API som ska användas senare.

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den hemliga nyckeln som du tidigare har registrerat i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ**väljer du `Manual`.
7. Ange ett **namn** för princip nyckeln. Till exempel `TwitterSecret`. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
8. I **hemlighet**anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning**väljer du `Encryption`.
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett Twitter-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Twitter-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.

1. Öppna *TrustFrameworkExtensions. XML*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ersätt värdet för **client_id** med den konsument nyckel som du tidigare har registrerat.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur man kommunicerar med ditt Twitter-konto. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions. XML* .
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av registrerings-eller inloggnings skärmarna. För att göra det tillgängligt, skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Twitter-identitetsprovider.

1. Öppna filen *TrustFrameworkBase. XML* från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"`.
3. Öppna *TrustFrameworkExtensions. XML* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Till exempel `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-eller inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för ett Twitter-konto visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade.
2. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med ett Twitter-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som innehåller `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID: t för den tekniska profil som du skapade tidigare. Till exempel `Twitter-OAUTH1`.

3. Spara filen *TrustFrameworkExtensions. XML* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i B2C-klienten. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn. XML* i din arbets katalog och Byt namn på den. Byt till exempel namnet till *SignUpSignInTwitter. XML*.
2. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInTwitter`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignTwitter).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrol lera att Azure AD B2C programmet som du har skapat är markerat i fältet **Välj program** och testa det genom att klicka på **Kör nu**.
