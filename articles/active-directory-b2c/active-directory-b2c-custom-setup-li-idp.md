---
title: Lägg till LinkedIn som identitetsprovider OAuth2 med hjälp av anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: En artikel om hur du konfigurerar ett LinkedIn-program med hjälp av protokollet OAuth2 och anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 334f696d79cf801facf7c5301b2240b69f7134f7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444386"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Lägga till LinkedIn som en identitetsprovider med hjälp av anpassade principer
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av en LinkedIn-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

## <a name="step-1-create-a-linkedin-account-application"></a>Steg 1: Skapa ett program för LinkedIn-konto
Att använda LinkedIn som en identitetsprovider i Azure Active Directory B2C (Azure AD B2C) måste du skapa ett LinkedIn-program och ange rätt parametrar. Du kan registrera ett LinkedIn-program genom att gå till den [LinkedIn registreringssidan](https://www.linkedin.com/start/join).

1. Gå till den [LinkedIn programhantering](https://www.linkedin.com/secure/developer?newapp=) webbplats, logga in med dina LinkedIn-konto och välj sedan **skapa program**.

    ![LinkedIn-konto – skapa program](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. På den **skapa ett nytt program** gör följande:

    a. Skriv din **företagsnamn**, en beskrivande **namn** för företaget, och en **beskrivning** för din nya app.

    b. Ladda upp din **Programlogotyp**.

    c. Välj en **användningen av**.

    d. I den **Webbadress** rutan, klistra in **https://login.microsoftonline.com**.

    e. Skriv din **företags-e-** adress och **Företagstelefon** tal.

    f. Längst ned på sidan läser och godkänner användningsvillkoren och välj sedan **skicka**.

    ![LinkedIn-konto – konfigurera egenskaper för program](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Välj **autentisering**, och anteckna den **klient-ID** och **Klienthemlighet** värden.

4. I den **behörighet omdirigerings-URL: er** rutan, klistra in **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Ersätt {*klient*} med ditt klientnamn (till exempel contosob2c.onmicrosoft.com). Kontrollera att du använder HTTPS-schema. 

    ![LinkedIn-konto – Ställ in behörighet omdirigera URL: er](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Klienthemlighet är en viktig säkerhetsuppgift för autentisering. Inte dela den här hemligheten med vem som helst och distribuera den med din app.

5. Välj **Lägg till**.

6. Välj **inställningar**, ändra den **programstatus** till **Live**, och välj sedan **uppdatering**.

    ![LinkedIn-konto - Set programstatus](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Steg 2: Lägga till dina LinkedIn-programnyckel i Azure AD B2C
Federation med LinkedIn-konton kräver en klienthemlighet för LinkedIn-konto till förtroende Azure AD B2C åt programmet. Om du vill lagra LinkedIn programhemlighet i din Azure AD B2C-klient måste du göra följande:  

1. I din Azure AD B2C-klient väljer **B2C inställningar** > **Identitetsramverk**.

2. Om du vill visa de nycklar som är tillgängliga i din klient, Välj **Principnycklar**.

3. Välj **Lägg till**.

4. I den **alternativ** väljer **överför**.

5. I den **namn** skriver **B2cRestClientCertificate**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** anger du dina LinkedIn-programhemlighet från den [Programregistreringsportalen](https://apps.dev.microsoft.com).

7. För **nyckelanvändning**väljer **kryptering**.

8. Välj **Skapa**. 

9. Bekräfta att du har skapat den `B2C_1A_LinkedInSecret`nyckel.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Steg 3: Lägga till en anspråksprovider i din princip för tillägg
Om du vill att användarna ska logga in med sina LinkedIn-konto, måste du definiera LinkedIn som en anspråksprovider. Med andra ord måste du ange de slutpunkter som Azure AD B2C kommunicerar med. Slutpunkterna tillhandahåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera LinkedIn som en anspråksprovider genom att lägga till en `<ClaimsProvider>` nod i ditt tillägg principfil:

1. Öppna i din arbetskatalog i *TrustFrameworkExtensions.xml* principfil för tillägget. 

2. Sök efter den `<ClaimsProviders>` element.

3. I den `<ClaimsProviders>` element, Lägg till följande XML-fragment: 

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Ersätt den *client_id* värdet med ditt LinkedIn programmets klients-ID.

5. Spara filen.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Steg 4: Registrera anspråksleverantören LinkedIn-konto
Du har konfigurerat identitetsprovidern. Det är dock inte ännu tillgängliga i någon av windows för registrering eller inloggning. Nu måste du lägga till identitetsleverantören. LinkedIn-konto till dina användare `SignUpOrSignIn` användarresa.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1: Skapa en kopia av användarresa
Frigör användarresa genom att skapa en dubblett av en befintlig mall för användaren resa och Lägg sedan till LinkedIn identitetsprovider:

>[!NOTE]
>Om du har kopierat den `<UserJourneys>` element från bas-filen i din princip att den *TrustFrameworkExtensions.xml* tilläggsfil, du kan hoppa över det här avsnittet.

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).

2. Sök efter den `<UserJourneys>` element, markera hela innehållet i den `<UserJourney>` noden och välj sedan **klipp ut** att flytta den markerade texten till Urklipp.

3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns kan du lägga till den.

4. Klistra in hela innehållet i den `<UserJourney>` nod som du har flyttat till Urklipp i steg 2, till den `<UserJourneys>` element.

### <a name="step-42-display-the-button"></a>Steg 4.2: Visa på ”knappen”
Den `<ClaimsProviderSelections>` elementet definierar en lista med alternativ för val av anspråk providern och deras inbördes ordning. Den `<ClaimsProviderSelection>` noden är detsamma som en knapp med identity-providern på en registrering eller inloggning. Om du lägger till en `<ClaimsProviderSelection>` nod för LinkedIn-konto, en ny knapp visas när en användare finns på sidan. Om du vill lägga till det här elementet, gör du följande:

1. Sök efter den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i användarresan som du kopierade.

2. Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`.

3. I den `<ClaimsProviderSelections>` element, Lägg till följande XML-fragment:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3: Länka knappen till en åtgärd
Nu när du har en knapp på plats, måste du koppla den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med LinkedIn-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din LinkedIn-konto anspråksprovidern:

1. Sök efter den `<OrchestrationStep>` nod som innehåller `Order="2"` i den `<UserJourney>` noden.

2. I den `<ClaimsExchanges>` element, Lägg till följande XML-fragment:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Se till att `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
    >* Se till att den `TechnicalProfileReferenceId` -ID har angetts till den tekniska profilen som du skapade tidigare (LinkedIn-OAuth).

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
    Du bör nu kunna logga in med LinkedIn-konto.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Steg 7: (Valfritt) registrera LinkedIn-konto anspråk providern profilredigering användarresa
Du kanske också vill lägga till identitetsprovider för LinkedIn-konto till din `ProfileEdit` användarresa. Att göra användaren transport tillgängliga Upprepa ”steg 4”. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.

## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
