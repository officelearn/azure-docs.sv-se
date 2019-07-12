---
title: Lägg till Microsoft-konto (MSA) som en identitetsprovider med anpassade principer i Azure Active Directory B2C
description: Exempel med hjälp av Microsoft som identitetsprovider med OpenID Connect (OIDC)-protokollet.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654157"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ställ in logga in med ett Microsoft-konto med hjälp av anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från ett Microsoft-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Förutsättningar

- Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Om du inte redan har ett Microsoft-konto, skapa en på [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Lägga till ett program

Om du vill aktivera inloggning för användare med ett Microsoft-konto som du behöver registrera ett program i Azure AD-klient. Azure AD-klienten är inte samma som din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrollera att du använder den katalog som innehåller Azure AD-klienten genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
1. Välj **ny registrering**.
1. Ange en **namn** för ditt program. Till exempel *MSAapp1*.
1. Under **stöds kontotyper**väljer **konton alla organisationskatalog och personliga Microsoft-konton (t.ex. Skype, Xbox, Outlook.com)** .
1. Under **omdirigerings-URI (valfritt)** väljer **Web** och ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` i textrutan. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klientorganisation.
1. Välj **registrera**
1. Post i **(klient)-ID: T** visas på översiktssidan för programmet. Du behöver den när du konfigurerar anspråksleverantören i ett senare avsnitt.
1. Välj **certifikat och hemligheter**
1. Klicka på **nya klienthemlighet**
1. Ange en **beskrivning** för hemlighet, till exempel *MSA-Programklienthemlighet*, och klicka sedan på **Lägg till**.
1. Anteckna lösenordet för program som visas i den **värdet** kolumn. Du kan använda det här värdet i nästa avsnitt.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Nu när du har skapat programmet i Azure AD-klienten, som du behöver lagra den programklienthemlighet i din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer **Identitetsramverk**.
1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **alternativ**, Välj `Manual`.
1. Ange en **namn** för principnyckeln. Till exempel `MSASecret`. Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
1. I **hemlighet**, ange klienthemligheten som du antecknade i föregående avsnitt.
1. För **nyckelanvändning**väljer `Signature`.
1. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill låta dina användare att logga in med ett Microsoft-konto, måste du definiera kontot som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera Azure AD som en anspråksprovider genom att lägga till den **ClaimsProvider** elementet i tilläggsfilen av din princip.

1. Öppna den *TrustFrameworkExtensions.xml* principfil.
1. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
1. Lägga till en ny **ClaimsProvider** på följande sätt:

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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

1. Ersätt värdet för **client_id** med Azure AD-programmet *(klient)-ID: T* som du antecknade tidigare.
1. Spara filen.

Du har nu konfigurerat principen så att Azure AD B2C vet hur du kommunicerar med ditt Microsoft-konto program i Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Ladda upp den ändrade principen för att bekräfta att den inte behöver hittills eventuella problem innan du fortsätter.

1. Navigera till din Azure AD B2C-klient i Azure-portalen och välj **Identitetsramverk**.
1. På den **anpassade principer** väljer **överföra anpassad princip**.
1. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
1. Klicka på **Överför**.

Om inga fel visas i portalen, kan du fortsätta till nästa avsnitt.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu kan du har konfigurerat identitetsprovidern, men den ännu inte är tillgängliga i någon av skärmarna registrering eller inloggning. Skapa en dubblett av en befintlig mall för användarresan så att den blir tillgänglig, och sedan ändrar du den så att de innehåller även identitetsprovider för Microsoft-konto.

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
1. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
1. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
1. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
1. Byt namn på ID för användarresa. Till exempel `SignUpSignInMSA`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en skärm för registrering eller inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett Microsoft-konto, en ny knapp visas när en användare finns på sidan.

1. I den *TrustFrameworkExtensions.xml* filen, hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
1. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med ett microsoftkonto för att ta emot en token.

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
1. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för det ID som du använde för **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** som kan matcha det `Id` värde i den **TechnicalProfile** element för anspråksleverantören som du lade till tidigare. Till exempel `MSA-OIDC`.

1. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du skapar i din Azure AD B2C-klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **Program** och därefter **Lägg till**.
1. Ange ett namn för programmet, till exempel *testapp1*.
1. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
1. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInMSA.xml*.
1. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInMSA`.
1. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_msa`
1. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för användarresan som du skapade tidigare (SignUpSignInMSA).
1. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
1. Se till att Azure AD B2C-program som du skapade i föregående avsnitt (eller genom att slutföra förutsättningar, till exempel *webapp1* eller *testapp1*) är markerad i den **Välj programmet** fältet och sedan testa den genom att klicka på **kör nu**.
1. Välj den **Account** knappen och logga in.

    Om inloggningen lyckas, är du omdirigeras till `jwt.ms` som visar den avkodas Token, liknar:

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
