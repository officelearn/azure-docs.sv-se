---
title: Konfigurera inloggning med ett Microsoft-konto med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Så här använder du anpassade principer för att aktivera MICROSOFT Account (MSA) som identitetsprovider med OpenID Connect-protokollet (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188128"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning med ett Microsoft-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln visas hur du aktiverar inloggning för användare från ett Microsoft-konto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

- Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).
- Om du inte redan har ett Microsoft-konto skapar du ett på [https://www.live.com/](https://www.live.com/).

## <a name="register-an-application"></a>Registrera ett program

Om du vill aktivera inloggning för användare med ett Microsoft-konto måste du registrera ett program i Azure AD-klienten. Azure AD-klienten är inte samma sak som din Azure AD B2C-klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din Azure AD-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD-klientorganisation.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **Kontotyper som stöds**väljer du Konton i alla **organisationskataloger och personliga Microsoft-konton (t.ex Outlook.com.**
1. Under **Omdirigera URI (valfritt)** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` väljer du **Webben** och anger i textrutan. Ersätt `your-tenant-name` med ditt Azure AD B2C-klientnamn.
1. Välj **registrera**
1. Registrera **programmets (klient)-ID** som visas på sidan Översikt för programmet. Du behöver detta när du konfigurerar anspråksprovidern i ett senare avsnitt.
1. Välj **certifikat & hemligheter**
1. Klicka på **Ny klienthemlighet**
1. Ange en **beskrivning** för hemligheten, till exempel *MSA Application Client Secret,* och klicka sedan på **Lägg till**.
1. Registrera programlösenordet som visas i kolumnen **Värde.** Du använder det här värdet i nästa avsnitt.

## <a name="configuring-optional-claims"></a>Konfigurera valfria anspråk

Om du vill `family_name` hämta `given_name` och anspråk från Azure AD kan du konfigurera valfria anspråk för ditt program i Azure-portalens användargränssnitt eller programmanifest. Mer information finns i [Så här anger du valfria anspråk till din Azure AD-app](../active-directory/develop/active-directory-optional-claims.md).

1. Logga in på [Azure-portalen](https://portal.azure.com). Sök efter och välj **Azure Active Directory**.
1. Välj **Appregistreringar**i avsnittet **Hantera** .
1. Välj det program som du vill konfigurera valfria anspråk för i listan.
1. Välj **Tokenkonfiguration (förhandsgranskning)** i avsnittet **Hantera** .
1. Välj **Lägg till valfritt anspråk**.
1. Välj den tokentyp som du vill konfigurera.
1. Välj valfria anspråk att lägga till.
1. Klicka på **Lägg till**.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Nu när du har skapat programmet i din Azure AD-klient måste du lagra programmets klienthemlighet i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. Ange ett **namn** för principnyckeln. Till exempel `MSASecret`. Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln.
1. I **Secret**anger du klienthemligheten som du spelade in i föregående avsnitt.
1. För **nyckelanvändning** `Signature`väljer du .
1. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användarna ska kunna logga in med ett Microsoft-konto måste du definiera kontot som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera Azure AD som anspråksprovider genom att lägga till **claimsprovider-elementet** i tilläggsfilen för din princip.

1. Öppna principfilen *TrustFrameworkExtensions.xml.*
1. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Ersätt värdet **för client_id** med Azure AD-programmets *program -ID (Application)* som du spelade in tidigare.
1. Spara filen.

Du har nu konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med ditt Microsoft-kontoprogram i Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Innan du fortsätter laddar du upp den ändrade principen för att bekräfta att den inte har några problem hittills.

1. Navigera till din Azure AD B2C-klient i Azure-portalen och välj **Identity Experience Framework**.
1. På sidan **Anpassade principer** väljer du Ladda upp **anpassad princip**.
1. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
1. Klicka på **Överför**.

Om inga fel visas i portalen fortsätter du till nästa avsnitt.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har du konfigurerat identitetsprovidern, men den är ännu inte tillgänglig på någon av inloggnings- eller inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har Identitetsprovidern för Microsoft-kontot.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
1. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
1. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
1. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
1. Byt namn på ID för användarens färd. Till exempel `SignUpSignInMSA`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings- eller inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för ett Microsoft-konto visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarresan som du skapade i filen *TrustFrameworkExtensions.xml.*
1. Lägg till följande element under **ClaimsProviderSelects.** Ange värdet för **TargetClaimsExchangeId** till ett `MicrosoftAccountExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med ett Microsoft-konto för att ta emot en token.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
1. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för ID:et som du använde för **TargetClaimsExchangeId:**

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** för `Id` att matcha värdet i **elementet TechnicalProfile** i anspråksprovidern som du lade till tidigare. Till exempel `MSA-OIDC`.

1. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade.

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignInMSA.xml*.
1. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInMSA`.
1. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_msa`
1. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den användarresa som du skapade tidigare (SignUpSignInMSA).
1. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
1. Kontrollera att Azure AD B2C-program som du skapade i föregående avsnitt (eller genom att fylla i förutsättningarna, till exempel *webapp1* eller *testapp1)* har valts i fältet **Välj program** och testa det sedan genom att klicka på **Kör nu**.
1. Välj knappen **Microsoft-konto** och logga in.

    Om inloggningen lyckas omdirigeras du till `jwt.ms` vilken visar den avkodade token, ungefär som:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
