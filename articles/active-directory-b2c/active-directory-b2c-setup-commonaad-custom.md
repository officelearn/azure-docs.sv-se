---
title: Konfigurera inloggning för en Azure AD-identitetsprovider med flera innehavare med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lägg till en Azure AD-identitetsprovider med flera innehavare med anpassade principer – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8524eb8f9a8d220964e5dd1f6f8dc6d1aaf94a6d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980723"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning för Azure Active Directory för flera innehavare med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare som använder slut punkten för flera innehavare för Azure Active Directory (Azure AD) med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure AD B2C. Detta gör att användare från flera Azure AD-klienter kan logga in på Azure AD B2C utan att konfigurera en teknisk Provider för varje klient. Gäst medlemmar i någon av dessa klienter **kommer dock inte** att kunna logga in. För det måste du [Konfigurera varje klient för sig](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com`används för Azure AD-klienten i organisationen och `fabrikamb2c.onmicrosoft.com` används som Azure AD B2C klient i följande instruktioner.

## <a name="prerequisites"></a>Förutsättningar

Slutför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrera ett program

Om du vill aktivera inloggning för användare från en specifik Azure AD-organisation måste du registrera ett program inom organisationens Azure AD-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller organisationens Azure AD-klient (contoso.com) genom att klicka på **katalog-och prenumerations filtret** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Appregistreringar**.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. För **program typ**väljer `Web app / API`du.
7. För **inloggnings-URL**: en anger du följande URL med gemener, där `your-tenant` ersätts med namnet på din Azure AD B2C-klient (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Klicka på **Skapa**. Kopiera **program-ID: t** som ska användas senare.
9. Välj programmet och välj sedan **Inställningar**.
10. Välj **nycklar**, ange nyckel beskrivningen, Välj en varaktighet och klicka sedan på **Spara**. Kopiera värdet för den nyckel som visas senare.
11. Under **Inställningar**väljer du **Egenskaper**, ange **flera innehavare** till `Yes`och klickar sedan på **Spara**

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra den program nyckel som du skapade i Azure AD B2C klient organisationen.

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
3. På sidan Översikt väljer du **ID för identitets miljö**.
4. Välj **princip nycklar** och välj sedan **Lägg till**.
5. För **alternativ**väljer `Manual`du.
6. Ange ett **namn** för princip nyckeln. Till exempel `ContosoAppSecret`.  Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
7. I **hemlighet**anger du din program nyckel som du tidigare har spelat in.
8. För **nyckel användning**väljer `Signature`du.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med Azure AD måste du definiera Azure AD som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera Azure AD som en anspråks leverantör genom att lägga till Azure AD i **ClaimsProvider** -elementet i tilläggs filen för din princip.

1. Öppna *TrustFrameworkExtensions. XML*.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
3. Lägg till en ny **ClaimsProvider** enligt följande:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="unique_name" />
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

4. Under elementet **ClaimsProvider** uppdaterar du värdet för **domän** till ett unikt värde som kan användas för att skilja den från andra identitets leverantörer.
5. Uppdatera värdet för **DisplayName**under **TechnicalProfile** -elementet. Det här värdet visas på inloggnings knappen på inloggnings skärmen.
6. Ange **client_id** till program-ID: t från Azure AD mulity-klientens app Registration.

### <a name="restrict-access"></a>Begränsa åtkomst

> [!NOTE]
> Om `https://sts.windows.net` du använder som värde för **ValidTokenIssuerPrefixes** kan alla Azure AD-användare logga in i ditt program.

Du måste uppdatera listan över giltiga token-utfärdare och begränsa åtkomsten till en särskild lista över Azure AD-Innehavaradministratörer som kan logga in. För att hämta värdena måste du titta på metadata för var och en av de Azure AD-klienter som du vill att användarna ska logga in från. Formatet på data ser ut ungefär så här: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, där `your-tenant` är ditt Azure AD-klient namn (contoso.com eller någon annan Azure AD-klient).

1. Öppna webbläsaren och gå till URL: en för **metadata** och leta efter **Issuer** -objektet och kopiera dess värde. Det bör se ut så här: `https://sts.windows.net/tenant-id/`.
2. Kopiera och klistra in värdet för nyckeln **ValidTokenIssuerPrefixes** . Du kan lägga till flera genom att avgränsa dem med kommatecken. Ett exempel på detta är kommenterat i exempel-XML ovan.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggs filen för verifiering

Nu har du konfigurerat principen så att Azure AD B2C vet hur de kan kommunicera med Azure AD-katalogen. Försök att ladda upp tilläggs filen för principen för att bekräfta att den inte har några problem hittills.

1. På sidan **anpassade principer** i Azure AD B2C klienten väljer du **Ladda upp princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions. XML* .
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråks leverantören

Nu har identitets leverantören kon figurer ATS, men den är inte tillgänglig på någon av inloggnings-och inloggnings skärmarna. För att göra det tillgängligt, skapar du en dubblett av en befintlig användar resa och ändrar den så att den även har Azure AD-identitetsprovider.

1. Öppna filen *TrustFrameworkBase. XML* från start paketet.
2. Sök efter och kopiera hela innehållet i **UserJourney** -elementet som innehåller `Id="SignUpOrSignIn"`.
3. Öppna *TrustFrameworkExtensions. XML* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Byt namn på användar resans ID. Till exempel `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

**ClaimsProviderSelection** -elementet är detsamma som en identitetsprovider på en registrerings-och inloggnings skärm. Om du lägger till ett **ClaimsProviderSelection** -element för Azure AD visas en ny knapp när en användare hamnar på sidan.

1. Hitta **OrchestrationStep** -elementet som innehåller `Order="1"` i användar resan som du skapade.
2. Lägg till följande-element under **ClaimsProviderSelects**. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C för att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för Azure AD-anspråks leverantören.

1. Hitta **OrchestrationStep** som ingår `Order="2"` i användar resan.
2. Lägg till följande **ClaimsExchange** -element och kontrol lera att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID: t** för den tekniska profil som du skapade tidigare. Till exempel `Common-AAD`.

3. Spara filen *TrustFrameworkExtensions. XML* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C program

Kommunikation med Azure AD B2C sker via ett program som du skapar i din klient organisation. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan har gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Se till att du använder den katalog som innehåller din Azure AD B2C-klientorganisation genom att klicka på **katalog- och prenumerationsfiltret** på den översta menyn och välja katalogen som innehåller din klientorganisation.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **Program** och därefter **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **webbapp/webb-API**väljer `Yes`du och anger `https://jwt.ms` sedan för svars- **URL: en**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande part filen

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn. XML* i din arbets katalog och Byt namn på den. Byt till exempel namnet till *SignUpSignContoso. XML*.
2. Öppna den nya filen och uppdatera värdet för attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInContoso`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: n för principen. Till exempel`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Uppdatera värdet för attributet **ReferenceId** i **DefaultUserJourney** för att matcha ID för den nya användar resan som du skapade (SignUpSignContoso).
5. Spara ändringarna, ladda upp filen och välj sedan den nya principen i listan.
6. Kontrol lera att Azure AD B2C programmet som du har skapat är markerat i fältet **Välj program** och testa det genom att klicka på **Kör nu**.
