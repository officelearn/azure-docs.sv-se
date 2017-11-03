---
title: "Azure Active Directory B2C: Lägga till Google + som en OAuth2-identitetsleverantör anpassade principer"
description: "Exempel med hjälp av Google + som identitetsleverantör med OAuth2-protokollet"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: 0d84dde1f70023abcfd0c15f5425d3cbaeb8c765
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Lägga till Google + som en OAuth2-identitetsleverantör anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här guiden visar hur du aktiverar inloggning för användare från Google +-konto med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Krav

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:

1.  Skapa ett Google + konto program.
2.  Att lägga till en Google-konto programmet nyckel + Azure AD B2C
3.  Att lägga till anspråksprovidern för en princip
4.  Registrera Google + konto anspråksprovidern för en användare resa
5.  Ladda upp principen till en Azure AD B2C-klient och testa den

## <a name="create-a-google-account-application"></a>Skapa ett Google + konto program
Om du vill använda Google + som en identitetsleverantör i Azure Active Directory (AD Azure) B2C, måste du skapa ett Google +-program och ange rätt parametrar. Du kan registrera en Google +-programmet här: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Gå till den [Google utvecklare konsolen](https://console.developers.google.com/) och logga in med Google + autentiseringsuppgifterna för ditt konto.
2.  Klicka på **skapa projekt**, ange en **projektnamn**, och klicka sedan på **skapa**.

3.  Klicka på den **menyn projekt**.

    ![Google +-konto – Välj projekt](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Klicka på den  **+**  knappen.

    ![Google + konto – Skapa nytt projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Ange en **projektnamn**, och klicka sedan på **skapa**.

    ![Google +-konto – nytt projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Vänta tills projektet är klar och klicka på den **menyn projekt**.

    ![Google + konto - vänta tills det nya projektet är redo att användas](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Klicka på ditt projektnamn.

    ![Google +-konto – Välj det nya projektet](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Klicka på **API-hanterare** och klicka sedan på **autentiseringsuppgifter** i det vänstra navigeringsfönstret.
9.  Klicka på den **OAuth-medgivande skärmen** högst upp.

    ![Google +-konto – ange OAuth-medgivande skärmen](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Välj eller ange en giltig **e-postadress**, ange en **produktnamn**, och klicka på **spara**.

    ![Google + - programmet autentiseringsuppgifter](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Klicka på **nya autentiseringsuppgifter** och välj sedan **OAuth-klient-ID**.

    ![Google + - skapa nya autentiseringsuppgifter för programmet](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  Under **programtyp**väljer **webbprogrammet**.

    ![Google + – Välj apptyp](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Ange en **namn** för ditt program ange `https://login.microsoftonline.com` i den **behörighet JavaScript ursprung** fältet och `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **auktoriserad omdirigerings-URI: er** fältet. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com). Den **{klient}** värdet är skiftlägeskänsligt. Klicka på **Skapa**.

    ![Google + - ge behörighet JavaScript ursprung och omdirigerings-URI: er](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Kopiera värdena i **klient-Id** och **klienthemlighet**. Du måste både för att konfigurera Google + som en identitetsleverantör i din klient. **Klienthemlighet** är en viktig säkerhetsuppgift för autentisering.

    ![Google + - kopiera värdena för klient-Id och klienten hemligt](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Lägga till Google + konto programmet nyckeln i Azure AD B2C
Federation med Google + konton kräver en klienthemlighet för Google + konto för Azure AD B2C-förtroende för programmet. Du måste lagra dina Google + programhemlighet i Azure AD B2C-klient:  

1.  Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **identitet upplevelse Framework**
2.  Välj **princip nycklar** att visa nycklarna som är tillgängliga i din klient.
3.  Klicka på **+ Lägg till**.
4.  För **alternativ**, använda **manuell**.
5.  För **namn**, Använd `GoogleSecret`.  
    Prefixet `B2C_1A_` kan läggas till automatiskt.
6.  I den **hemlighet** ange ditt Google programhemlighet från den [Google utvecklare konsolen](https://console.developers.google.com/) som du kopierade ovan.
7.  För **nyckelanvändning**, använda **signatur**.
8.  Klicka på **Skapa**
9.  Bekräfta att du har skapat nyckeln `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg

Om du vill att användarna att logga in med Google + konto måste du definiera Google + kontot som en anspråksprovider. Med andra ord, måste du ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats.

Definiera Google + kontot som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i tillägget-principfil:

1.  Öppna filen för princip för tillägg (TrustFrameworkExtensions.xml) från arbetskatalogen. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), en enkel plattformsoberoende redigerare.
2.  Hitta de `<ClaimsProviders>` avsnitt
3.  Lägg till följande XML-kodstycke under den `ClaimsProviders` element och Ersätt `client_id` värdet med Google + konto programmet klient-ID innan du sparar filen.  

```xml
<ClaimsProvider>
    <Domain>google.com</Domain>
    <DisplayName>Google</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Google-OAUTH">
        <DisplayName>Google</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
        <Item Key="ProviderName">google</Item>
        <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
        <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
        <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrera Google + konto anspråksleverantören om du vill registrera dig eller logga in användaren resa

Identitetsleverantören har ställts in.  Det är dock inte tillgänglig i alla skärmar sign-upp/inloggning. Lägga till Google + konto identitetsleverantören till dina användare `SignUpOrSignIn` användare resa. Om du vill göra den tillgänglig, kan vi skapa en dubblett av en befintlig mall användaren resa.  Vi Lägg sedan till Google + konto identitetsleverantören:

>[!NOTE]
>
>Om du har kopierat den `<UserJourneys>` element från bas-filen för din princip för tilläggsfilen (TrustFrameworkExtensions.xml) som du kan hoppa till det här avsnittet.

1.  Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
2.  Hitta de `<UserJourneys>` element och kopiera hela innehållet i `<UserJourneys>` nod.
3.  Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
4.  Klistra in hela innehållet i `<UserJournesy>` nod som du kopierade som underordnad till den `<UserJourneys>` element.

### <a name="display-the-button"></a>Visa knappen
Den `<ClaimsProviderSelections>` elementet definierar en lista över alternativ för val av anspråk providern och deras inbördes ordning.  `<ClaimsProviderSelection>`elementet är detsamma som knappen identity-providern på en sign-upp/inloggningssidan. Om du lägger till en `<ClaimsProviderSelection>` element för Google + konto, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1.  Hitta de `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i transporten användare som du kopierade.
2.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
3.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Google +-konto för att ta emot en token.

1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt
> * Se till att `TechnicalProfileReferenceId` -ID har angetts för teknisk profilen du skapade tidigare (Google-OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient
1.  I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2.  Välj **identitet upplevelse Framework**.
3.  Öppna den **alla principer** bladet.
4.  Välj **överföra princip**.
5.  Kontrollera **skriva över principen om den finns** rutan.
6.  **Överför** TrustFrameworkExtensions.xml och se till att inte misslyckas verifieringen

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen genom att använda Kör nu
1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.

    >[!NOTE]
    >
    >    **Kör nu** kräver minst ett program till preregistered för innehavaren. 
    >    Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.


2.  Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Valfritt] Registrera Google + konto anspråksprovidern för profil-Redigera användare resa
Du kanske vill lägga till Google + konto identitetsleverantören också till dina användare `ProfileEdit` användare resa. Vi Upprepa de två sista stegen för att göra den tillgänglig:

### <a name="display-the-button"></a>Visa knappen
1.  Öppna filen för tillägg av principen (till exempel TrustFrameworkExtensions.xml).
2.  Hitta de `<UserJourney>` nod som innehåller `Id="ProfileEdit"` i transporten användare som du kopierade.
3.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
4.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testa en anpassad profil-Redigera princip genom att använda Kör nu

1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.
2.  Öppna **B2C_1A_ProfileEdit**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med Google +.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna
Valfritt: Vi rekommenderar att du skapar ditt scenario med en egen anpassad princip för filer när du har slutfört komma igång med principer för anpassade igenom istället för att använda dessa exempelfiler.  [Exempelfiler för principen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
