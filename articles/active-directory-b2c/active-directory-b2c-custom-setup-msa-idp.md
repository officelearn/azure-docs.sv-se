---
title: "Azure Active Directory B2C: Lägga till Microsoft-konto (Hanterade) som en identitetsleverantör anpassade principer"
description: "Exempel med hjälp av Microsoft som identitetsleverantör med OpenID Connect (OIDC)-protokollet"
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
ms.date: 08/04/2017
ms.author: yoelh
ms.openlocfilehash: cdc77d093358fa15bb1acbc9ba6b1867bae062f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Lägga till Microsoft-konto (Hanterade) som en identitetsleverantör anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från Microsoft-konto (MSA) med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Krav
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikel.

De här stegen innefattar:

1.  Skapa ett program för Microsoft-konto.
2.  Lägga till nyckeln programmet Microsoft-konto i Azure AD B2C
3.  Att lägga till anspråksprovidern för en princip
4.  Registrera Account anspråksprovidern för en användare resa
5.  Ladda upp principen till en Azure AD B2C-klient och testa den

## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto
Om du vill använda Microsoft-konto som en identitetsleverantör i Azure Active Directory (AD Azure) B2C måste du skapa ett program för Microsoft-konto och ange rätt parametrar. Du behöver ett microsoftkonto. Om du inte har någon besöker [https://www.live.com/](https://www.live.com/).

1.  Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) och logga in med ditt Microsoft-kontouppgifter.
2.  Klicka på **Lägg till en app**.

    ![Microsoft-konto – Lägg till en app](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Ange en **namn** för programmet, **kontakta e-post**, avmarkera **vi kan hjälpa dig att komma igång** och på **skapa**.

    ![Microsoft-konto - registrera ditt program](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Kopiera värdet för **program-Id**. Du behöver det för att konfigurera Microsoft-konto som en identitetsleverantör i din klient.

    ![Microsoft-konto – Kopiera värdet för program-Id](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Klicka på **Lägg till plattformen**

    ![Microsoft-konto – Lägg till plattformen](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Plattformslistan och väljer **Web**.

    ![Microsoft-konto - plattformslistan väljer Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Ange `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` i den **omdirigerings-URI: er** fältet. Ersätt **{klient}** med din klient namn (till exempel contosob2c.onmicrosoft.com).

    ![Microsoft-konto - uppsättningen omdirigerings-URL: er](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Klicka på **generera nya lösenord** under den **programmet hemligheter** avsnitt. Kopiera det nya lösenordet som visas på skärmen. Du behöver det för att konfigurera Microsoft-konto som en identitetsleverantör i din klient. Lösenordet är en viktig säkerhetsuppgift för autentisering.

    ![Microsoft-konto – Skapa nytt lösenord](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft-konto – kopiera det nya lösenordet](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Markera kryssrutan som säger **Live SDK stöd** under den **avancerade alternativ** avsnitt. Klicka på **Spara**.

    ![Microsoft-konto - stöd för Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Lägg till program-tangenten Microsoft-konto i Azure AD B2C
Federation med Microsoft-konton kräver en klienthemlighet för Microsoft-konto för Azure AD B2C-förtroende för programmet. Du måste lagra secert för programmet ditt Microsoft-konto i Azure AD B2C-klient:   

1.  Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **identitet upplevelse Framework**
2.  Välj **princip nycklar** att visa nycklarna som är tillgängliga i din klient.
3.  Klicka på **+ Lägg till**.
4.  För **alternativ**, använda **manuell**.
5.  För **namn**, Använd `MSASecret`.  
    Prefixet `B2C_1A_` kan läggas till automatiskt.
6.  I den **hemlighet** ange ditt hemliga för Microsoft-program från https://apps.dev.microsoft.com
7.  För **nyckelanvändning**, använda **signatur**.
8.  Klicka på **Skapa**
9.  Bekräfta att du har skapat nyckeln `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg
Om du vill att användarna att logga in med hjälp av Account måste du definiera Account som en anspråksprovider. Med andra ord, måste du ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en specifik användare har autentiserats.

Definiera Account som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i tillägget-principfil:

1.  Öppna filen för princip för tillägg (TrustFrameworkExtensions.xml) från arbetskatalogen. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), en enkel plattformsoberoende redigerare.
2.  Hitta de `<ClaimsProviders>` avsnitt
3.  Lägg till följande XML-kodstycke under den `ClaimsProviders` element:

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
        <Item Key="client_id">Your Microsoft application client id</Item>
        </Metadata>
    <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
    </CryptographicKeys>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
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

4.  Ersätt `client_id` värdet med Account programklienten Id

5.  Spara filen.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrera Account anspråk leverantör för att logga in eller logga in användaren resa

Nu identitetsleverantören har ställts in, men den är inte tillgänglig i alla skärmar sign-upp/inloggning. Nu måste du lägga till identitetsleverantören Account användaren `SignUpOrSignIn` användaren resa. Om du vill göra den tillgänglig, kan vi skapa en dubblett av en befintlig mall användaren resa.  Vi Lägg sedan till identitetsleverantören Account:

> [!NOTE]
>
>Om du tidigare har kopierat den `<UserJourneys>` element från bas-filen för din princip för tilläggsfilen `TrustFrameworkExtensions.xml`, du kan hoppa till det här avsnittet.

1.  Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
2.  Hitta de `<UserJourneys>` element och kopiera hela innehållet i `<UserJourneys>` nod.
3.  Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
4.  Klistra in hela innehållet i `<UserJournesy>` nod som du kopierade som underordnad till den `<UserJourneys>` element.

### <a name="display-the-button"></a>Visa knappen
Den `<ClaimsProviderSelections>` elementet definierar en lista över alternativ för val av anspråk providern och deras inbördes ordning.  `<ClaimsProviderSelection>`elementet är detsamma som knappen identity-providern på en sign-upp/inloggningssidan. Om du lägger till en `<ClaimsProviderSelection>` element för Microsoft-konto, en ny knapp visas när en användare de hamnar på sidan. Lägg till det här elementet:

1.  Hitta de `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i transporten användare som du kopierade.
2.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
3.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
Nu när du har en knapp på plats måste länka till en åtgärd. Åtgärden är i det här fallet för Azure AD B2C att kommunicera med Account ta emot en token. Länka knappen till en åtgärd genom att länka tekniska profilen för din Account anspråksleverantör:

1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt
>   * Se till att `TechnicalProfileReferenceId` -ID har angetts för teknisk profilen du skapade tidigare (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient
1.  I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2.  Välj **identitet upplevelse Framework**.
3.  Öppna den **alla principer** bladet.
4.  Välj **överföra princip**.
5.  Kontrollera **skriva över principen om den finns** rutan.
6.  **Överför** TrustFrameworkExtensions.xml och se till att inte misslyckas verifieringen

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen genom att använda Kör nu

1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.
> [!NOTE]
>
>**Kör nu** kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.
2.  Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med Microsoft-konto.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Valfritt] Registrera Account anspråksprovidern för profil-Redigera användare resa
Du kanske vill lägga till identitetsleverantören Account också till dina användare `ProfileEdit` användare resa. Vi Upprepa de två sista stegen för att göra den tillgänglig:

### <a name="display-the-button"></a>Visa knappen
1.  Öppna filen för tillägg av principen (till exempel TrustFrameworkExtensions.xml).
2.  Hitta de `<UserJourney>` nod som innehåller `Id="ProfileEdit"` i transporten användare som du kopierade.
3.  Leta upp den `<OrchestrationStep>` nod som innehåller`Order="1"`
4.  Lägg till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
1.  Hitta de `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` nod.
2.  Lägg till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testa en anpassad profil-Redigera princip genom att använda Kör nu
1.  Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.
2.  Öppna **B2C_1A_ProfileEdit**, förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.
3.  Du ska kunna logga in med Microsoft-konto.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna
Valfritt: Vi rekommenderar att du skapar ditt scenario med en egen anpassad princip för filer när du har slutfört komma igång med principer för anpassade igenom istället för att använda dessa exempelfiler.  [Exempelfiler för principen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
