---
title: 'Azure Active Directory B2C: Lägga till Twitter som en identitetsleverantör OAuth1 med anpassade principer'
description: Använda Twitter som en identitetsleverantör med hjälp av protokollet OAuth1
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.openlocfilehash: 4a7cc552f49877874dc173a47efe22873d5aeeed
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Lägga till Twitter som en identitetsleverantör OAuth1 med anpassade principer
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av ett Twitter-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

## <a name="step-1-create-a-twitter-account-application"></a>Steg 1: Skapa ett program för Twitter-konto
Att använda Twitter som en identitetsleverantör i Azure Active Directory B2C (Azure AD B2C), måste du skapa ett Twitter-program och ange rätt parametrar. Du kan registrera ett Twitter-program genom att gå till den [Twitter registreringssidan](https://twitter.com/signup).

1. Gå till den [Twitter utvecklare](https://apps.twitter.com/) webbplats, logga in med dina autentiseringsuppgifter för Twitter och välj sedan **Skapa ny App**.

    ![Twitter-konto – Skapa ny app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. I den **skapar ett program** fönster, gör du följande:
 
    a. Typ av **namn** och en **beskrivning** för din nya app. 

    b. I den **webbplats** klistra in **https://login.microsoftonline.com**. 

    c. I den **motringning URL** klistra in **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Ersätt {*klient*} med ditt klientnamn (till exempel contosob2c.onmicrosoft.com). Kontrollera att du använder HTTPS-schema. 

    d. Längst ned på sidan Läs och acceptera villkoren och välj sedan **skapa programmet Twitter**.

    ![Twitter-konto – Lägg till en ny app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. I den **B2C demo** väljer **inställningar**, Välj den **att det här tillämpningsprogrammet som används för att logga in med Twitter** kryssrutan och välj sedan **uppdatering Inställningar för**.

4. Välj **nycklar och åtkomst-token**, och notera den **konsumentnyckel (API-nyckel)** och **konsumenten hemligheten (API hemliga)** värden.

    ![Twitter-konto – anger programegenskaper](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Hemligheten som konsumenten är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera med din app.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Steg 2: Lägg till program för Twitter kontonyckel i Azure AD B2C
Federation med Twitter-konton kräver en konsumenthemlighet för Twitter-konto för Azure AD B2C-förtroende för programmet. Om du vill lagra Twitter-programmets konsumenthemlighet i din Azure AD B2C-klient, gör du följande: 

1. Välj i din Azure AD B2C-klient **B2C inställningar** > **identitet upplevelse Framework**.

2. Om du vill visa de nycklar som är tillgängliga i din klient **princip nycklar**.

3. Välj **Lägg till**.

4. I den **alternativ** väljer **manuell**.

5. I den **namn** väljer **TwitterSecret**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** ange ditt Microsoft-program hemliga från den [Programregistreringsportalen](https://apps.dev.microsoft.com).

7. För **nyckelanvändning**, använda **kryptering**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_TwitterSecret` nyckel.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Steg 3: Lägg till en anspråksprovider i din princip för tillägg

Om du vill att användarna att logga in med Twitter-konto måste du definiera Twitter som en anspråksprovider. Med andra ord måste du ange slutpunkterna som Azure AD B2C kommunicerar med. Slutpunkterna ange en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats.

Definiera Twitter som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i tillägget-principfil:

1. Öppna i arbetskatalogen i *TrustFrameworkExtensions.xml* principfil för tillägget. 

2. Sök efter den `<ClaimsProviders>` avsnitt.

3. I den `<ClaimsProviders>` nod, Lägg till följande XML-kodstycke:  

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
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
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

4. Ersätt den *client_id*'-värde med din Twitter-konto programmet konsumentnyckel.

5. Spara filen.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Steg 4: Registrera anspråksprovidern Twitter-konto för registrering eller inloggning användare-resa
Du har konfigurerat identitetsleverantören. Men finns det inte ännu i något av fönstren registrering eller inloggning. Nu måste du lägga till identitetsleverantören Twitter-konto till din användare `SignUpOrSignIn` användare resa.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1: Gör en kopia av användaren resa
Frigör användaren resan, skapar en dubblett av en befintlig användare resa mall och Lägg sedan till Twitter identitetsleverantör:

>[!NOTE]
>Om du har kopierat den `<UserJourneys>` element från filen bas för din princip för att den *TrustFrameworkExtensions.xml* tilläggsfilen, du kan gå vidare till nästa avsnitt.

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).

2. Sök efter den `<UserJourneys>` element, markera hela innehållet i den `<UserJourney>` och sedan väljer **klipp ut** att flytta den markerade texten till Urklipp.

3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och sök sedan efter den `<UserJourneys>` element. Lägg till om elementet inte finns.

4. Klistra in hela innehållet i den `<UserJourney>` nod som du har flyttat till Urklipp i steg 2, till den `<UserJourneys>` element.

### <a name="step-42-display-the-button"></a>Steg 4.2: Visa knappen ””
Den `<ClaimsProviderSelections>` elementet definierar en lista över alternativ för val av anspråk providern och deras inbördes ordning. Den `<ClaimsProviderSelection>` nod är detsamma som knappen identity-providern på en registrering eller inloggning. Om du lägger till en `<ClaimsProviderSelection>` nod för ett Twitter-konto, en ny knapp visas när en användare de hamnar på sidan. Gör följande för att lägga till det här elementet:

1. Sök efter den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i transporten användare som du kopierade.

2. Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`.

3. I den `<ClaimsProviderSelections>` element, Lägg till följande XML-kodstycke:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3: Länk för att en åtgärd
Nu när du har en knapp på plats måste du koppla den till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Twitter-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för anspråksprovider din Twitter-konto:

1. Sök efter den `<OrchestrationStep>` nod som innehåller `Order="2"` i den `<UserJourney>` nod.
2. I den `<ClaimsExchanges>` element, Lägg till följande XML-kodstycke:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Se till att `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
    >* Se till att den `TechnicalProfileReferenceId` -ID har angetts för teknisk profilen som du skapade tidigare (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Steg 5: Överför principen till din klient
1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överför den *TrustFrameworkBase.xml* och *TrustFrameworkExtensions.xml* filer och se till att de valideras.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Steg 6: Testa den anpassade principen genom att använda Kör nu

1. Välj **Azure AD B2C inställningar**, och välj sedan **identitet upplevelse Framework**.

    >[!NOTE]
    >Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.  
    Du bör nu kunna logga in med Twitter-konto.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Steg 7: (Valfritt) registrera Twitter-konto anspråk providern för transporten profil-Redigera användare
Du kanske också vill lägga till identitetsleverantören Twitter-konto till din `ProfileEdit` användaren resa. Att göra användaren transport tillgängliga, Upprepa ”steg 4”. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.


## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
