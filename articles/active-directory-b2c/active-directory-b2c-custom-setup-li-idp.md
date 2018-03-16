---
title: "Azure Active Directory B2C: Lägga till LinkedIn som en OAuth2-identitetsleverantör med anpassade principer"
description: "En artikel om hur du konfigurerar ett LinkedIn-program med hjälp av OAuth2-protokollet och anpassade principer"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 77e2b9b283e4051370ffb905681135c27512834e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Lägga till LinkedIn som en identitetsleverantör med anpassade principer
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare av en LinkedIn-konto med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

## <a name="step-1-create-a-linkedin-account-application"></a>Steg 1: Skapa ett konto LinkedIn-program
Att använda LinkedIn som en identitetsleverantör i Azure Active Directory B2C (Azure AD B2C), måste du skapa ett LinkedIn-program och ange rätt parametrar. Du kan registrera ett LinkedIn-program genom att gå till den [LinkedIn registreringssidan](https://LinkedIn.com/signup).

1. Gå till den [LinkedIn programhantering](https://www.linkedin.com/secure/developer?newapp=) webbplats, logga in med autentiseringsuppgifterna för ditt LinkedIn och välj sedan **skapa program**.

    ![LinkedIn-konto – skapa program](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. På den **skapa ett nytt program** gör följande:

    a. Skriv din **företagsnamn**, ett beskrivande **namn** för företaget, och en **beskrivning** för din nya app.

    b. Ladda upp din **programmet logotypen**.

    c. Välj en **användningen av**.

    d. I den **Webbadress** klistra in  **https://login.microsoftonline.com** .

    e. Skriv din **företags-e-** adress och **telefon** nummer.

    f. Längst ned på sidan Läs och Godkänn användningsvillkoren och välj sedan **skicka**.

    ![LinkedIn-konto – konfigurera egenskaper för program](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Välj **autentisering**, och anteckna den **klient-ID** och **Klienthemlighet** värden.

4. I den **behörighet omdirigerings-URL: er** klistra in  **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp** . Ersätt {*klient*} med ditt klientnamn (till exempel contosob2c.onmicrosoft.com). Kontrollera att du använder HTTPS-schema. 

    ![LinkedIn-konto – ange behörighet omdirigerings-URL: er](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Klienthemligheten är en viktig säkerhetsuppgift för autentisering. Dela den här hemligheten med någon eller inte distribuera med din app.

5. Välj **Lägg till**.

6. Välj **inställningar**, ändra den **programstatus** till **Live**, och välj sedan **uppdatering**.

    ![Konto för LinkedIn - uppsättningen programstatus](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Steg 2: Lägg till din nyckel för LinkedIn-program i Azure AD B2C
Federation med LinkedIn konton kräver en klienthemlighet för LinkedIn-konto för Azure AD B2C-förtroende för programmet. Om du vill lagra programhemlighet LinkedIn i din Azure AD B2C-klient, gör du följande:  

1. Välj i din Azure AD B2C-klient **B2C inställningar** > **identitet upplevelse Framework**.

2. Om du vill visa de nycklar som är tillgängliga i din klient **princip nycklar**.

3. Välj **Lägg till**.

4. I den **alternativ** väljer **överför**.

5. I den **namn** skriver **B2cRestClientCertificate**.  
    Prefixet *B2C_1A_* kan läggas till automatiskt.

6. I den **hemlighet** ange din LinkedIn programhemlighet från den [Programregistreringsportalen](https://apps.dev.microsoft.com).

7. För **nyckelanvändning**väljer **kryptering**.

8. Välj **Skapa**. 

9. Bekräfta att du har skapat den `B2C_1A_LinkedInSecret`nyckel.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Steg 3: Lägg till en anspråksprovider i din princip för tillägg
Om du vill att användarna att logga in med sina LinkedIn-konto måste du definiera LinkedIn som en anspråksprovider. Med andra ord måste du ange slutpunkterna som Azure AD B2C kommunicerar med. Slutpunkterna ange en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats.

Definiera LinkedIn som en anspråksprovider genom att lägga till en `<ClaimsProvider>` nod i tillägget-principfil:

1. Öppna i arbetskatalogen i *TrustFrameworkExtensions.xml* principfil för tillägget. 

2. Sök efter den `<ClaimsProviders>` element.

3. I den `<ClaimsProviders>` element, Lägg till följande XML-kodstycke: 

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

4. Ersätt den *client_id* värdet med din LinkedIn programmet klient-ID.

5. Spara filen.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Steg 4: Registrera anspråksleverantören LinkedIn-konto
Du har konfigurerat identitetsleverantören. Men finns det inte ännu i något av fönstren registrering eller inloggning. Nu måste du lägga till identitetsleverantören LinkedIn konto användaren `SignUpOrSignIn` användaren resa.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Steg 4.1: Gör en kopia av användaren resa
Frigör användaren resan, skapar en dubblett av en befintlig användare resa mall och Lägg sedan till identitetsleverantören LinkedIn:

>[!NOTE]
>Om du har kopierat den `<UserJourneys>` element från filen bas för din princip för att den *TrustFrameworkExtensions.xml* tilläggsfilen, du kan hoppa över det här avsnittet.

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).

2. Sök efter den `<UserJourneys>` element, markera hela innehållet i den `<UserJourney>` och sedan väljer **klipp ut** att flytta den markerade texten till Urklipp.

3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Lägg till om elementet inte finns.

4. Klistra in hela innehållet i den `<UserJourney>` nod som du har flyttat till Urklipp i steg 2, till den `<UserJourneys>` element.

### <a name="step-42-display-the-button"></a>Steg 4.2: Visa knappen ””
Den `<ClaimsProviderSelections>` elementet definierar en lista över alternativ för val av anspråk providern och deras inbördes ordning. Den `<ClaimsProviderSelection>` nod är detsamma som knappen identity-providern på en registrering eller inloggning. Om du lägger till en `<ClaimsProviderSelection>` nod för en LinkedIn-konto, en ny knapp visas när en användare de hamnar på sidan. Gör följande för att lägga till det här elementet:

1. Sök efter den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i transporten användare som du kopierade.

2. Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`.

3. I den `<ClaimsProviderSelections>` element, Lägg till följande XML-kodstycke:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Steg 4.3: Länk för att en åtgärd
Nu när du har en knapp på plats måste du koppla den till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med kontot LinkedIn ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din LinkedIn konto anspråksleverantör:

1. Sök efter den `<OrchestrationStep>` nod som innehåller `Order="2"` i den `<UserJourney>` nod.

2. I den `<ClaimsExchanges>` element, Lägg till följande XML-kodstycke:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Se till att `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
    >* Se till att den `TechnicalProfileReferenceId` -ID har angetts för teknisk profilen som du skapade tidigare (LinkedIn-OAuth).

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
    Du bör nu kunna logga in med kontot LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Steg 7: (Valfritt) registrera kontot LinkedIn anspråk providern för transporten profil-Redigera användare
Du kanske också vill lägga till identitetsleverantören LinkedIn-konto till din `ProfileEdit` användaren resa. Att göra användaren transport tillgängliga, Upprepa ”steg 4”. Den här gången väljer den `<UserJourney>` nod som innehåller `Id="ProfileEdit"`. Spara, ladda upp och testa din princip.

## <a name="optional-download-the-complete-policy-files"></a>(Valfritt) Hämta de fullständiga principfilerna
När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
