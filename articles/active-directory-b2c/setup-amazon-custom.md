---
title: Konfigurera inloggning med ett Amazon-konto med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Konfigurera inloggning med ett Amazon-konto i Azure Active Directory B2C att använda anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bac37eed33535962ac0f1e6dbb34d8c396507682
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063636"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Amazon-konto med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från ett Amazon-konto genom att använda [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Förutsättningar

- Slutför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
- Om du inte redan har ett Amazon-konto skapar du ett [https://www.amazon.com/](https://www.amazon.com/)på.

## <a name="register-the-application"></a>Registrera programmet

Om du vill aktivera inloggning för användare från ett Amazon-konto måste du skapa ett Amazon-program.

1. Logga in på [Amazon Developer Center](https://login.amazon.com/) med dina Amazon-kontoautentiseringsuppgifter.
2. Om du inte redan har gjort det klickar du på **Registrera**, följer utvecklings registrerings stegen och godkänner principen.
3. Välj **Registrera nytt program**.
4. Ange ett **namn**, en **Beskrivning**och **en URL för sekretess meddelande**och klicka sedan på **Spara**. Sekretess meddelandet är en sida som du hanterar som ger sekretess information till användarna.
5. I avsnittet **webb inställningar** kopierar du värdena för **klient-ID**. Välj **Visa hemlighet** för att hämta klient hemligheten och kopiera den sedan. Du behöver båda dessa för att konfigurera ett Amazon-konto som en identitets leverantör i din klient organisation. **Klient hemlighet** är en viktig säkerhets autentiseringsuppgift.
6. I avsnittet **webb inställningar** väljer du **Redigera**och `https://your-tenant-name.b2clogin.com` anger sedan i **tillåtna JavaScript-ursprung** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **tillåtna retur-URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Använd gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
7. Klicka på **Spara**.

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra klient hemligheten som du tidigare registrerade i Azure AD B2C-klienten.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ**väljer `Manual`du.
7. Ange ett **namn** för princip nyckeln. Till exempel `AmazonSecret`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. I **hemlighet**anger du din klient hemlighet som du tidigare har spelat in.
9. För **nyckel användning**väljer `Signature`du.
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett Amazon-konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett Amazon-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil.


1. Öppna *TrustFrameworkExtensions. XML*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ange **client_id** till program-ID: t från program registreringen.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med Azure AD-katalogen. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions. XML* .
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av inloggnings-och inloggnings skärmarna. Om du vill göra det tillgängligt skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Amazon Identity-providern.

1. Öppna filen *TrustFrameworkBase. XML* från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"`.
3. Öppna *TrustFrameworkExtensions. XML* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Till exempel `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-och inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för ett Amazon-konto visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade.
2. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är att Azure AD B2C kommunicera med ett Amazon-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID: t för den tekniska profil som du skapade tidigare. Till exempel `Amazon-OAuth`.

3. Spara filen *TrustFrameworkExtensions. XML* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C program

Kommunikation med Azure AD B2C sker via ett program som du skapar i din klient organisation. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan har gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **webbapp/webb-API**väljer `Yes`du och anger `https://jwt.ms` sedan för **svars-URL: en**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn. XML* i din arbets katalog och Byt namn på den. Byt till exempel namnet till *SignUpSignInAmazon. XML*.
2. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInAmazon`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignAmazon).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrol lera att Azure AD B2C programmet som du har skapat är markerat i fältet **Välj program** och testa det genom att klicka på **Kör nu**.
