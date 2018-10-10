---
title: Konfigurera logga in med ett Azure Active Directory-konto i Azure Active Directory B2C med anpassade principer | Microsoft Docs
description: Ställ in logga in med ett Azure Active Directory-konto i Azure Active Directory B2C med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c122153f43bd68b0c3c5a1046e2a0b7c65249572
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887299"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Ställ in logga in med ett Azure Active Directory-konto med hjälp av anpassade principer i Azure Active Directory B2C 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från en organisation med Azure Active Directory (Azure AD) med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i [Kom igång med anpassade principer i Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrera ett program

Aktivera inloggning för användare från en viss Azure AD-organisation kan du behöva registrera ett program i organisationen Azure AD-klient.

>[!NOTE]
>`Contoso.com` används för i organisationens Azure AD-klient och `fabrikamb2c.onmicrosoft.com` används som Azure AD B2C-klient i följande anvisningar.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller organisationens Azure AD-klient (contoso.com) genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. För den **programtyp**väljer `Web app / API`.
7. För den **inloggnings-URL**, ange följande URL i alla gemener, där `your-tenant` ersätts med namnet på din Azure AD B2C-klient (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Klicka på **Skapa**. Kopiera den **program-ID** som ska användas senare.
9. Välj programmet och välj sedan **inställningar**.
10. Välj **nycklar**, ange nyckelbeskrivningen, Välj en varaktighet och klicka sedan på **spara**. Kopiera värdet för den nyckel som visas för att användas senare.

## <a name="create-a-policy-key"></a>Skapa en principnyckel

Du behöver lagra programnyckeln som du skapade i din Azure AD B2C-klient.

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
2. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
3. På sidan Översikt väljer **Identitetsupplevelse – FÖRHANDSVERSION**.
4. Välj **Principnycklar** och välj sedan **Lägg till**.
5. För **alternativ**, Välj `Manual`.
6. Ange en **namn** för principnyckeln. Till exempel `ContosoAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till namnet på din nyckel.
7. I **hemlighet**, ange din programnyckel som du antecknade tidigare.
8. För **nyckelanvändning**väljer `Signature`.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråksprovider

Om du vill att användarna ska logga in med hjälp av Azure AD, måste du definiera Azure AD som en anspråksprovider som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats. 

Du kan definiera Azure AD som en anspråksprovider genom att lägga till Azure AD för att den **ClaimsProvider** elementet i tilläggsfilen av din princip.

1. Öppna den *TrustFrameworkExtensions.xml*.
2. Hitta den **ClaimsProviders** element. Om det inte finns, lägger du till det under rotelementet.
3. Lägga till en ny **ClaimsProvider** på följande sätt:

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-tenant/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Under den **ClaimsProvider** element, uppdatera värdet för **domän** till ett unikt värde som kan användas för att skilja den från andra identitetsleverantörer.
5. Under den **ClaimsProvider** element, uppdatera värdet för **DisplayName** till ett eget namn för anspråksprovidern. Det här värdet används inte för närvarande.

### <a name="update-the-technical-profile"></a>Uppdatera den tekniska profilen

Om du vill hämta en token från Azure AD-slutpunkten som du behöver definiera de protokoll som Azure AD B2C ska använda vid kommunikation med Azure AD. Detta görs i den **TechnicalProfile** element i **ClaimsProvider**.

1. Uppdatera ID för den **TechnicalProfile** element. Detta ID används för att referera till den här tekniska profilen från andra delar av principen.
2. Uppdatera värdet för **DisplayName**. Det här värdet visas på knappen Logga in på skärmen inloggning.
3. Uppdatera värdet för **beskrivning**.
4. Azure AD använder OpenID Connect-protokollet, så se till att värdet för **protokollet** är `OpenIdConnect`.
5. Anger värdet för den **METADATA** till `https://login.windows.net/your-tenant/.well-known/openid-configuration`, där `your-tenant` är ditt Azure AD-klientnamn (contoso.com).
6. Öppna webbläsaren och gå till den **METADATA** URL som du uppdaterade, leta upp den **utfärdare** objekt, kopiera och klistra in värdet i värdet för **ProviderName** i XML-filen.
8. Ange **client_id** och **IdTokenAudience** program-ID: t från programregistrering.
9. Under **CryptograhicKeys**, uppdatera värdet för **StorageReferenceId** till principnyckeln som du har definierat. Till exempel `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen med för verifiering

Nu bör har du konfigurerat principen så att Azure AD B2C vet att kommunicera med Azure AD-katalogen. Försök att överföra tilläggsfilen i din princip bara för att bekräfta att den inte har några problem hittills.

1. På den **anpassade principer** sidan i din Azure AD B2C-klient väljer **ladda upp principen**.
2. Aktivera **Skriv över principen om den finns**, och sedan bläddra till och markera den *TrustFrameworkExtensions.xml* fil.
3. Klicka på **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksprovidern

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrerings-registreringen/logga in. Om du vill göra den tillgänglig, skapa en dubblett av en befintlig mall för användarresan och ändra den så att de innehåller även identitetsprovidern Azure AD:

1. Öppna den *TrustFrameworkBase.xml* filen från startpaket.
2. Hitta och kopiera hela innehållet i den **UserJourney** element som innehåller `Id="SignUpOrSignIn"`.
3. Öppna den *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela innehållet i den **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Byt namn på ID för användarresa. Till exempel `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelection** element är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningsskärmen. Om du lägger till en **ClaimsProviderSelection** element för Azure AD, en ny knapp visas när en användare finns på sidan.

1. Hitta den **OrchestrationStep** element som innehåller `Order="1"` i användarresan som du skapade.
2. Under **ClaimsProviderSelects**, lägger du till följande element. Ange värdet för **TargetClaimsExchangeId** till ett lämpligt värde, till exempel `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Azure AD-anspråksleverantör:

1. Hitta den **OrchestrationStep** som innehåller `Order="2"` i användarresan.
2. Lägg till följande **ClaimsExchange** element att se till att du använder samma värde för **Id** som du använde för **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Uppdatera värdet för **TechnicalProfileReferenceId** till den **Id** för den tekniska profilen du skapade tidigare. Till exempel `ContosoProfile`.

3. Spara den *TrustFrameworkExtensions.xml* fil och ladda upp den igen för att bekräfta.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2c sker via ett program som du skapar i din klient. Det här avsnittet innehåller valfria steg som du kan utföra för att skapa ett testprogram om du inte redan gjort det.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa filen förlitande part

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt namn på den. Exempel: Byt namn på den till *SignUpSignInContoso.xml*.
2. Öppna den nya filen och uppdatera värdet för den **PolicyId** attributet för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInContoso`.
3. Uppdatera värdet för **PublicPolicyUri** med URI: N för principen. Exempel:`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Uppdatera värdet för den **ReferenceId** attributet i **DefaultUserJourney** att matcha ID för den nya användarresa som du skapade (SignUpSignInContoso).
5. Spara dina ändringar, överföra filen och välj sedan den nya principen i listan.
6. Se till att Azure AD B2C-program som du skapat är markerad i den **Välj program** fältet och sedan testa den genom att klicka på **kör nu**.

