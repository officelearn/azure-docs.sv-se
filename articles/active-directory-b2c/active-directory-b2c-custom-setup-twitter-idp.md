---
title: Lägga till Twitter som identitetsprovider OAuth1 med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Använd Twitter som identitetsprovider med hjälp av OAuth1-protokollet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 28679ef07c2625908f7b08f808ff49c48ddb625b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339876"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Lägga till Twitter som identitetsprovider OAuth1 med anpassade principer
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av en Twitter-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

## <a name="step-1-create-a-twitter-account-application"></a>Steg 1: Skapa ett Twitter-konto-program
Använda Twitter som identitetsprovider i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett Twitter-program och ange det med rätt parametrar. Du kan registrera ett Twitter-program genom att gå till den [Twitter registreringssidan](https://twitter.com/signup).

1. Gå till den [Twitter utvecklare](https://apps.twitter.com/) webbplats, logga in med dina autentiseringsuppgifter för Twitter-konto och välj sedan **Skapa ny App**.

    ![Twitter-konto – Skapa ny app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. I den **skapar ett program** fönstret gör du följande:
 
    a. Skriv den **namn** och en **beskrivning** för din nya app. 

    b. I den **webbplats** rutan, klistra in **https://{tenant}.b2clogin.com**. Där **{klient}** är ditt klientnamn (till exempel https://contosob2c.b2clogin.com).

    c. 4. För den **Motringnings-URL**, ange `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp`. Ersätt **{klient}** med klientens namn (till exempel contosob2c) och **{policyId}** med princip-id (till exempel b2c_1_policy).  **Motringningen URL: en måste finnas i alla gemener.** Du bör lägga till en Motringnings-URL för alla principer som använder Twitter-inloggning. Se till att använda `b2clogin.com` i stället för ` login.microsoftonline.com` om du använder den i ditt program.

    d. Längst ned på sidan Läs och Godkänn villkoren och välj sedan **skapa ditt Twitter-program**.

    ![Twitter-konto – lägga till en ny app](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. I den **B2C demo** väljer **inställningar**väljer den **gör så att programmet som ska användas för att logga in med Twitter** kryssrutan och välj sedan **Update Inställningar för**.

4. Välj **nycklar och åtkomsttoken**, och notera den **använda nyckeln (API-nyckel)** och **Konsumenthemligheten (API-hemlighet)** värden.

    ![Twitter-konto – ange egenskaper för programmet](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Konsumenthemligheten är en viktig säkerhetsuppgift för autentisering. Inte dela den här hemligheten med vem som helst och distribuera den med din app.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Steg 2: Lägga till nyckel för application ditt Twitter-konto i Azure AD B2C
Federation med Twitter-konton kräver en konsumenthemligheten för Twitter-konto till förtroende Azure AD B2C åt programmet. Om du vill lagra konsumenthemligheten för Twitter-program i din Azure AD B2C-klient måste du göra följande: 

1. I din Azure AD B2C-klient väljer **B2C inställningar** > **Identitetsramverk**.

2. Om du vill visa de nycklar som är tillgängliga i din klient, Välj **Principnycklar**.

3. Välj **Lägg till**.

4. I den **alternativ** väljer **manuell**.

5. I den **namn** väljer **TwitterSecret**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** anger du din Microsoft-programhemlighet från den [Programregistreringsportalen](https://apps.dev.microsoft.com).

7. För **nyckelanvändning**, använda **kryptering**.

8. Välj **Skapa**.

9. Bekräfta att du har skapat den `B2C_1A_TwitterSecret` nyckel.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Steg 3: Lägga till en anspråksprovider i din princip för tillägg

Om du vill att användarna ska logga in med Twitter-konto, måste du definiera Twitter som en anspråksprovider. Med andra ord måste du ange de slutpunkter som Azure AD B2C kommunicerar med. Slutpunkterna tillhandahåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera Twitter som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i ditt tillägg principfil:

1. Öppna i din arbetskatalog i *TrustFrameworkExtensions.xml* principfil för tillägget. 

2. Sök efter den `<ClaimsProviders>` avsnittet.

3. I den `<ClaimsProviders>` nod, Lägg till följande XML-fragment:  

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

4. Ersätt den *client_id*' värdet med ditt Twitter-konto program använda nyckeln.

5. Spara filen.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Steg 4: Registrera anspråksprovidern Twitter-konto för din registrering eller inloggning användarresa
Du har konfigurerat identitetsprovidern. Det är dock inte ännu tillgängliga i någon av windows för registrering eller inloggning. Nu måste du lägga till Twitter-konto identitetsprovider användaren `SignUpOrSignIn` användarresa.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1: Skapa en kopia av användarresa
Frigör användarresa genom att skapa en dubblett av en befintlig användare resa mall och Lägg sedan till Twitter-identitetsprovider:

>[!NOTE]
>Om du har kopierat den `<UserJourneys>` element från bas-filen i din princip att den *TrustFrameworkExtensions.xml* tilläggsfil, du kan gå vidare till nästa avsnitt.

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).

2. Sök efter den `<UserJourneys>` element, markera hela innehållet i den `<UserJourney>` noden och välj sedan **klipp ut** att flytta den markerade texten till Urklipp.

3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och sök sedan efter den `<UserJourneys>` element. Om elementet inte finns kan du lägga till den.

4. Klistra in hela innehållet i den `<UserJourney>` nod som du har flyttat till Urklipp i steg 2, till den `<UserJourneys>` element.

### <a name="step-42-display-the-button"></a>Steg 4.2: Visa på ”knappen”
Den `<ClaimsProviderSelections>` elementet definierar en lista med alternativ för val av anspråk providern och deras inbördes ordning. Den `<ClaimsProviderSelection>` noden är detsamma som en knapp med identity-providern på en registrering eller inloggning. Om du lägger till en `<ClaimsProviderSelection>` nod för ett Twitter-konto, en ny knapp visas när en användare finns på sidan. Om du vill lägga till det här elementet, gör du följande:

1. Sök efter den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i användarresan som du kopierade.

2. Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`.

3. I den `<ClaimsProviderSelections>` element, Lägg till följande XML-fragment:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3: Länka knappen till en åtgärd
Nu när du har en knapp på plats, måste du koppla den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med Twitter-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för anspråksleverantören ditt Twitter-konto:

1. Sök efter den `<OrchestrationStep>` nod som innehåller `Order="2"` i den `<UserJourney>` noden.
2. I den `<ClaimsExchanges>` element, Lägg till följande XML-fragment:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Se till att `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
    >* Se till att den `TechnicalProfileReferenceId` -ID har angetts till den tekniska profilen som du skapade tidigare (Twitter-OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Steg 5: Ladda upp principen till din klient
1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **Identitetsramverk**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **Skriv över principen om den finns** markerar du kryssrutan.

6. Ladda upp den *TrustFrameworkBase.xml* och *TrustFrameworkExtensions.xml* filer och se till att de valideras.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Steg 6: Testa den anpassade principen med hjälp av kör nu

1. Välj **Azure AD B2C-inställningar**, och välj sedan **Identitetsramverk**.

    >[!NOTE]
    >Kör nu kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.

2. Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassa princip som du överförde och väljer sedan **kör nu**.  
    Du bör nu kunna logga in med Twitter-konto.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Steg 7: (Valfritt) registrera Twitter-konto anspråk providern profilredigering användarresa
Du kanske också vill lägga till identitetsprovider för Twitter-konto till din `ProfileEdit` användarresa. Att göra användaren transport tillgängliga Upprepa ”steg 4”. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.


## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
