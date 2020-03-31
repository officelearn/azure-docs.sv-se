---
title: Konfigurera inloggning med ett Amazon-konto med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera inloggning med ett Amazon-konto i Azure Active Directory B2C med hjälp av anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de891ee109677f92ff603759701f7732f5951ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188519"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Amazon-konto med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från ett Amazon-konto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer](custom-policy-get-started.md).
- Om du inte redan har ett Amazon-konto skapar du ett på [https://www.amazon.com/](https://www.amazon.com/).

## <a name="register-the-application"></a>Registrera ansökan

Om du vill aktivera inloggning för användare från ett Amazon-konto måste du skapa ett Amazon-program.

1. Logga in på [Amazon Developer Center](https://login.amazon.com/) med dina Amazon-kontouppgifter.
2. Om du inte redan har gjort det klickar du på **Registrera dig,** följer stegen för registrering av utvecklare och accepterar principen.
3. Välj **Registrera ett nytt program**.
4. Ange **url:en namn,** **beskrivning**och **sekretessmeddelande**och klicka sedan på **Spara**. Sekretessmeddelandet är en sida som du hanterar och som ger sekretessinformation till användarna.
5. Kopiera värdena för **klient-ID**i avsnittet **Webbinställningar** . Välj **Visa hemlighet** om du vill få klienten hemlig och kopiera den sedan. Du behöver båda för att konfigurera ett Amazon-konto som en identitetsleverantör i din klientorganisation. **Klienthemlighet** är en viktig säkerhetsautentiseringsinformation.
6. I avsnittet **Webbinställningar** **Edit**väljer du Redigera `https://your-tenant-name.b2clogin.com` och anger sedan `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i **Tillåtna JavaScript-ursprung** och i **Tillåtna returadresser**. Ersätt `your-tenant-name` med namnet på din klient. Använd alla gemener när du anger ditt klientnamn även om klienten definieras med versaler i Azure AD B2C.
7. Klicka på **Spara**.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du måste lagra klienthemligheten som du tidigare har registrerat i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **Identity Experience Framework**.
5. Välj **Principnycklar** och välj sedan **Lägg till**.
6. För **Alternativ** `Manual`väljer du .
7. Ange ett **namn** för principnyckeln. Till exempel `AmazonSecret`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
8. I **Secret**anger du din klienthemlighet som du tidigare spelat in.
9. För **nyckelanvändning** `Signature`väljer du .
10. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med ett Amazon-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera ett Amazon-konto som en anspråksleverantör genom att lägga till det i elementet **ClaimsProviders i förlängningsfilen** för din princip.


1. Öppna *TrustFrameworkExtensions.xml*.
2. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
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
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
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

4. Ange **client_id** till program-ID:et från programregistreringen.
5. Spara filen.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med din Azure AD-katalog. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings-/inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har Amazon-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings-/inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett Amazon-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade.
2. Lägg till följande element under **ClaimsProviderSelects.** Ange värdet för **TargetClaimsExchangeId** till ett `AmazonExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett Amazon-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för ID:et som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till ID för den tekniska profil som du skapade tidigare. Till exempel `Amazon-OAuth`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInAmazon.xml*.
2. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInAmazon`.
3. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den nya användarresan som du skapade (SignUpSignAmazon).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrollera att Azure AD B2C-program som du har skapat har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.
