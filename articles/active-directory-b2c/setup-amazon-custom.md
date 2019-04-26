---
title: Ställ in logga in med ett Amazon-konto med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Ställ in logga in med ett Amazon-konto i Azure Active Directory B2C med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f62238543f1c24d05702ee0679610934d308538d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360392"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ställ in logga in med ett Amazon-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en Amazon-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Utför stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
- Om du inte redan har ett Amazon-konto, skapa en på [ https://www.amazon.com/ ](https://www.amazon.com/).

## <a name="register-the-application"></a>Registrera programmet

Om du vill aktivera inloggning för användare från en Amazon-konto, måste du skapa ett program för Amazon.

1. Logga in på den [Amazon Developer Center](https://login.amazon.com/) med autentiseringsuppgifterna för ditt Amazon.
2. Om du inte redan har gjort det, klickar du på **registrera dig**, av utvecklare registrering anvisningarna och acceptera principen.
3. Välj **registrera nya program**.
4. Ange en **namn**, **beskrivning**, och **meddelande Sekretesswebbadress**, och klicka sedan på **spara**. Sekretessmeddelandet är en sida som du hanterar och som innehåller Sekretessinformation för användare.
5. I den **webbinställningar** och kopiera värdena för **klient-ID**. Välj **visa hemligheten** att hämta klienthemligheten och kopiera den. Du måste båda du konfigurerar ett Amazon-konto som identitetsprovider i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.
6. I den **webbinställningar** väljer **redigera**, och ange sedan `https://your-tenant-name.b2clogin.com` i **tillåtna ursprung för JavaScript** och `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **tillåten Returnerar URL: er**. Ersätt `your-tenant-name` med namnet på din klient. Använd gemener när du anger ditt klientnamn även om klienten har definierats med versaler i Azure AD B2C.
7. Klicka på **Spara**.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du behöver lagra klienthemlighet som tidigare registrerades i din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer **Identitetsupplevelse – FÖRHANDSVERSION**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **alternativ**, Välj `Manual`.
7. Ange en **namn** för principnyckeln. Till exempel `AmazonSecret`. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
8. I **hemlighet**, ange dina klienthemlighet som du antecknade tidigare.
9. För **nyckelanvändning**väljer `Signature`.
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill att användarna att logga in med ett Amazon-konto måste du definiera kontot som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera ett Amazon-konto som en anspråksprovider genom att lägga till den **ClaimsProviders** elementet i tilläggsfilen av din princip.


1. Öppna den *TrustFrameworkExtensions.xml*.
2. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
3. Lägga till en ny **ClaimsProvider** på följande sätt:  

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

4. Ange **client_id** program-ID: t från programregistrering.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet att kommunicera med Azure AD-katalogen. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills.

1. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
2. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrerings-registreringen/logga in. Du skapar en dubblett av en befintlig mall för användarresan så att den blir tillgänglig, och ändra den så att de innehåller även identitetsprovider Amazon.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningsskärmen. Om du lägger till en **ClaimsProviderSelection** element för ett Amazon-konto, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
2. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med en Amazon-konto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** -ID: t för den tekniska profilen du skapade tidigare. Till exempel `Amazon-OAuth`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInAmazon.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInAmazon`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignAmazon).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.
