---
title: Konfigurera inloggning för Azure AD med flera innehavare efter anpassade principer
titleSuffix: Azure AD B2C
description: Lägg till en Azure AD-identitetsprovider för flera innehavare med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ad51e113a752e0692cb377a83d4819b4e284bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188451"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurera inloggning för Azure Active Directory med flera innehavare med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare med slutpunkten för flera innehavare för Azure Active Directory (Azure AD) med hjälp av [anpassade principer](custom-policy-overview.md) i Azure AD B2C. Detta gör det möjligt för användare från flera Azure AD-klienter att logga in med Azure AD B2C, utan att du behöver konfigurera en identitetsprovider för varje klient. Gästmedlemmar i någon av dessa hyresgäster kommer dock **inte** att kunna logga in. För det måste du [konfigurera varje klient individuellt](identity-provider-azure-ad-single-tenant-custom.md).

## <a name="prerequisites"></a>Krav

Slutför stegen i [Komma igång med anpassade principer i Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registrera ett program

Om du vill aktivera inloggning för användare från en viss Azure AD-organisation måste du registrera ett program inom den organisatoriska Azure AD-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrollera att du använder katalogen som innehåller din organisation Azure AD-klientorganisation (till exempel contoso.com). Välj **katalog + prenumerationsfilter** i den övre menyn och välj sedan den katalog som innehåller din klient.
1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen och sök sedan efter och välj **Appregistreringar**.
1. Välj **Ny registrering**.
1. Ange ett **namn** för ditt program. Till exempel `Azure AD B2C App`.
1. Välj **Konton i valfri organisationskatalog** för det här programmet.
1. För **Omdirigera URI**godkänner du värdet **för webben**och anger följande `your-B2C-tenant-name` URL i alla gemener, där ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Välj **Registrera**. Registrera **program-ID:t (klient)** för användning i ett senare steg.
1. Välj **Certifikat & hemligheter**och välj sedan Ny **klienthemlighet**.
1. Ange en **beskrivning** för hemligheten, välj en förfallodatum och välj sedan **Lägg till**. Registrera **värdet** för hemligheten för användning i ett senare steg.

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

Du måste lagra programnyckeln som du skapade i din Azure AD B2C-klientorganisation.

1. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerationsfilter** i den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Under **Principer**väljer du **Identity Experience Framework**.
1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. Ange ett **namn** för principnyckeln. Till exempel `AADAppSecret`.  Prefixet `B2C_1A_` läggs automatiskt till i namnet på nyckeln när det skapas, så dess referens i XML i följande avsnitt är att *B2C_1A_AADAppSecret*.
1. I **Secret**anger du din klienthemlighet som du spelade in tidigare.
1. För **nyckelanvändning** `Signature`väljer du .
1. Välj **Skapa**.

## <a name="add-a-claims-provider"></a>Lägga till en anspråksleverantör

Om du vill att användare ska logga in med Azure AD måste du definiera Azure AD som en anspråksleverantör som Azure AD B2C kan kommunicera med via en slutpunkt. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Du kan definiera Azure AD som anspråksprovider genom att lägga till Azure AD i **ClaimsProvider-elementet** i tilläggsfilen för din princip.

1. Öppna filen *TrustFrameworkExtensions.xml.*
1. Leta reda på elementet **ClaimsProviders.** Om den inte finns lägger du till den under rotelementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. Under **elementet ClaimsProvider** uppdaterar du värdet för **Domän** till ett unikt värde som kan användas för att skilja det från andra identitetsleverantörer.
1. Uppdatera värdet för **DisplayName**under elementet `Contoso Employee` **TechnicalProfile** , till exempel . Det här värdet visas på inloggningsknappen på inloggningssidan.
1. Ange **client_id** till program-ID för Azure AD-programmet för flera innehavare som du registrerade tidigare.
1. Under **CryptographicKeys**uppdaterar du värdet **för StorageReferenceId** till namnet på principnyckeln som skapade tidigare. Till exempel `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Begränsa åtkomst

> [!NOTE]
> Om `https://login.microsoftonline.com/` du använder som värde för **ValidTokenIssuerPrefixes** kan alla Azure AD-användare logga in på ditt program.

Du måste uppdatera listan över giltiga tokenutfärdare och begränsa åtkomsten till en specifik lista över Azure AD-klientanvändare som kan logga in.

Om du vill hämta värdena tittar du på OpenID Connect-identifieringsmetadata för var och en av Azure AD-klienter som du vill att användare ska logga in från. Formatet på metadata-URL:en `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`liknar `your-tenant` , var är ditt Azure AD-klientnamn. Ett exempel:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Utför de här stegen för varje Azure AD-klient som ska användas för att logga in:

1. Öppna webbläsaren och gå till OpenID Connect-metadata-URL:en för klienten. Hitta **utfärdarobjektet** och registrera dess värde. Det bör se `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`ut som .
1. Kopiera och klistra in värdet i nyckeln **ValidTokenIssuerPrefixes.** Separera flera emittenter med ett kommatecken. Ett exempel med två utfärdare `ClaimsProvider` visas i föregående XML-exempel.

### <a name="upload-the-extension-file-for-verification"></a>Ladda upp tilläggsfilen för verifiering

Vid det här laget har du konfigurerat din princip så att Azure AD B2C vet hur du kommunicerar med dina Azure AD-kataloger. Prova att ladda upp förlängningsfilen för din policy bara för att bekräfta att den inte har några problem hittills.

1. På sidan **Anpassade principer** i din Azure AD B2C-klient väljer du **Överför princip**.
2. Aktivera **Skriv över principen om den finns**och bläddra sedan till och välj filen *TrustFrameworkExtensions.xml.*
3. Välj **Överför**.

## <a name="register-the-claims-provider"></a>Registrera anspråksleverantören

Nu har identitetsprovidern konfigurerats, men den är inte tillgänglig på någon av inloggnings-/inloggningsskärmarna. Om du vill göra den tillgänglig skapar du en dubblett av en befintlig mallanvändarresa och ändrar den så att den också har Azure AD-identitetsprovidern.

1. Öppna filen *TrustFrameworkBase.xml* från startpaketet.
2. Leta reda på och kopiera hela innehållet `Id="SignUpOrSignIn"`i **UserJourney-elementet** som innehåller .
3. Öppna *TrustFrameworkExtensions.xml* och leta reda på **Elementet UserJourneys.** Om elementet inte finns lägger du till ett.
4. Klistra in hela innehållet i **UserJourney-elementet** som du kopierade som underordnade **elementet UserJourneys.**
5. Byt namn på ID för användarens färd. Till exempel `SignUpSignInContoso`.

### <a name="display-the-button"></a>Visa knappen

Elementet **ClaimsProviderSelection** är analogt med en identitetsleverantörsknapp på en inloggnings-/inloggningsskärm. Om du lägger till ett **ClaimsProviderSelection-element** för Azure AD visas en ny knapp när en användare hamnar på sidan.

1. Leta reda på **orchestrationStep-elementet** som ingår `Order="1"` i användarfärden som du skapade i *TrustFrameworkExtensions.xml*.
1. Lägg till följande element under **ClaimsProviderSelects.** Ange värdet för **TargetClaimsExchangeId** till ett `AzureADExchange`lämpligt värde, till exempel:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats måste du länka den till en åtgärd. Åtgärden, i det här fallet, är för Azure AD B2C att kommunicera med Azure AD för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Azure AD-anspråksleverantör.

1. Hitta **OrchestrationStep** som `Order="2"` ingår i användarfärden.
2. Lägg till följande **ClaimsExchange-element** och se till att du använder samma värde för **ID** som du använde för **TargetClaimsExchangeId:**

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Uppdatera värdet för **TechnicalProfileReferenceId** till **ID** för den tekniska profil som du skapade tidigare. Till exempel `Common-AAD`.

3. Spara *filen TrustFrameworkExtensions.xml* och ladda upp den igen för verifiering.

## <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du registrerar i din B2C-klientorganisation. I det här avsnittet visas valfria steg som du kan slutföra för att skapa ett testprogram om du inte redan har gjort det.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Uppdatera och testa den förlitande partfilen

Uppdatera den förlitande partens (RP) fil som initierar användarens färd som du skapade:

1. Gör en kopia av *SignUpOrSignIn.xml* i arbetskatalogen och byt namn på den. Byt till exempel namn på den till *SignUpSignContoso.xml*.
1. Öppna den nya filen och uppdatera värdet för **Attributet PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Till exempel `SignUpSignInContoso`.
1. Uppdatera värdet för **PublicPolicyUri** med URI för principen. Till exempel `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Uppdatera värdet för **Attributet ReferenceId** i **DefaultUserJourney** så att det matchar ID:t för den användarresa som du skapade tidigare. Registrera till exempel *SignUpSignInContoso*.
1. Spara ändringarna och ladda upp filen.
1. Välj den nya principen i listan under **Anpassade principer.**
1. I listrutan **Välj program** väljer du Azure AD B2C-programmet som du skapade tidigare. Till exempel *testapp1*.
1. Kopiera **slutpunkten Kör nu** och öppna den i ett privat webbläsarfönster, till exempel Incognito-läge i Google Chrome eller ett InPrivate-fönster i Microsoft Edge. Om du öppnar i ett privat webbläsarfönster kan du testa den fullständiga användarresan genom att inte använda några för närvarande cachelagrade Azure AD-autentiseringsuppgifter.
1. Välj inloggningsknappen för Azure AD, till exempel *Contoso Employee*, och ange sedan autentiseringsuppgifterna för en användare i en av dina Azure AD-organisationsklienter. Du uppmanas att godkänna programmet och ange sedan information för din profil.

Om inloggningsprocessen lyckas omdirigeras webbläsaren till `https://jwt.ms`, som visar innehållet i token som returneras av Azure AD B2C.

Om du vill testa inloggningskapaciteten för flera innehavare utför du de två sista stegen med autentiseringsuppgifterna för en användare som finns en annan Azure AD-klientorganisation.

## <a name="next-steps"></a>Nästa steg

När du arbetar med anpassade principer kan du ibland behöva ytterligare information när du felsöker en princip under dess utveckling.

För att diagnostisera problem kan du tillfälligt placera principen i "utvecklarläge" och samla in loggar med Azure Application Insights. Ta reda på hur i [Azure Active Directory B2C: Samla in loggar](troubleshoot-with-application-insights.md).
