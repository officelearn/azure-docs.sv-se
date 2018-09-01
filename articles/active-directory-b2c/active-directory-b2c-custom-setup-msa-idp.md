---
title: Lägg till Microsoft-konto (MSA) som en identitetsprovider med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Exempel med hjälp av Microsoft som identitetsprovider med OpenID Connect (OIDC)-protokollet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6a981f112c97ee35b476c92f6f698e68a12a1363
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336829"
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Lägga till Microsoft-konto (MSA) som en identitetsprovider med hjälp av anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för användare från Microsoft-konto (MSA) med [anpassade principer](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Förutsättningar
Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

De här stegen är:

1.  Skapa ett program för Microsoft-konto.
2.  Att lägga till programnyckel för Microsoft-konto i Azure AD B2C
3.  Att lägga till anspråksprovidern för en princip
4.  Registrera Account anspråksprovidern för en användarresa
5.  Ladda upp principen till en Azure AD B2C-klient och testar det.

## <a name="create-a-microsoft-account-application"></a>Skapa ett program för Microsoft-konto
Om du vill använda Microsoft-konto som identitetsprovider i Azure Active Directory (Azure AD) B2C, måste du skapa ett program för Microsoft-konto och ange rätt parametrar. Du behöver ett microsoftkonto. Om du inte har något, gå till [ https://www.live.com/ ](https://www.live.com/).

1.  Gå till den [Microsoft Programregistreringsportalen](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) och logga in med autentiseringsuppgifterna för ditt Microsoft-konto.
2.  Klicka på **lägga till en app**.

    ![Microsoft-konto – lägga till en app](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Ange en **namn** för ditt program, **e-postadress**, avmarkera **berätta kommer du igång** och klicka på **skapa**.

    ![Microsoft-konto – registrera ditt program](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Kopiera värdet för **program-Id**. Du behöver den för att konfigurera Microsoft-konto som en identitetsprovider i din klient.

    ![Microsoft-konto – Kopiera värdet för program-Id](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Klicka på **Lägg till plattform**

    ![Microsoft-konto – Lägg till plattform](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Välj plattformslistan **Web**.

    ![Microsoft-konto – från plattformslistan över väljer Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Ange `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` i den **omdirigerings-URI: er** fält. Ersätt **{klient}** med klientens namn (till exempel contosob2c).

    ![Microsoft-konto – ange omdirigerings-URL: er](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Klicka på **generera nytt lösenord** under den **Programhemligheter** avsnittet. Kopiera det nya lösenordet som visas på skärmen. Du behöver den för att konfigurera Microsoft-konto som en identitetsprovider i din klient. Det här lösenordet är en viktig säkerhetsuppgift för autentisering.

    ![Microsoft-konto – generera ett nytt lösenord](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Microsoft-konto – kopiera det nya lösenordet](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Markera rutan **Live SDK stöd** under den **avancerade alternativ** avsnittet. Klicka på **Spara**.

    ![Microsoft-konto – stöd för Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Lägg till programnyckel för Microsoft-konto i Azure AD B2C
Federation med Microsoft-konton kräver en klienthemlighet för Microsoft-konto förtroende Azure AD B2C åt programmet. Du behöver lagra secert för programmet ditt Microsoft-konto i Azure AD B2C-klient:   

1.  Gå till din Azure AD B2C-klient och välj **B2C inställningar** > **Identitetsupplevelse**
2.  Välj **Principnycklar** att visa nycklarna som är tillgängliga i din klient.
3.  Klicka på **+ Lägg till**.
4.  För **alternativ**, använda **manuell**.
5.  För **namn**, använda `MSASecret`.  
    Prefixet `B2C_1A_` kan läggas till automatiskt.
6.  I den **hemlighet** anger du din Microsoft-programhemlighet från https://apps.dev.microsoft.com
7.  För **nyckelanvändning**, använda **signatur**.
8.  Klicka på **Skapa**
9.  Bekräfta att du har skapat nyckeln `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg
Om du vill att användarna ska logga in med Account, måste du definiera Account som en anspråksprovider. Med andra ord måste du ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera Account som en anspråksprovider genom att lägga till `<ClaimsProvider>` nod i ditt tillägg principfil:

1.  Öppna filen över princip tillägget (TrustFrameworkExtensions.xml) från din arbetskatalog. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), ett enkelt redigeringsprogram för olika plattformar.
2.  Hitta den `<ClaimsProviders>` avsnittet
3.  Lägga till följande XML-kodstycke under den `ClaimsProviders` element:

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

4.  Ersätt `client_id` värdet med ditt Account program klient-Id

5.  Spara filen.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrera Account anspråk providern för att logga in eller logga in användarresa

Nu identitetsprovidern har ställts in, men det finns inte i någon av skärmarna registrerings-registreringen/logga in. Nu måste du lägga till identitetsprovider Account användaren `SignUpOrSignIn` användarresa. Om du vill göra den tillgänglig, kan vi skapa en dubblett av en befintlig mall för användarresan.  Sedan lägger vi till identitetsprovider Account:

> [!NOTE]
>
>Om du tidigare har kopierat den `<UserJourneys>` element från bas-fil i din princip att tilläggsfilen `TrustFrameworkExtensions.xml`, du kan gå vidare till det här avsnittet.

1.  Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
2.  Hitta den `<UserJourneys>` element och kopiera hela innehållet i `<UserJourneys>` noden.
3.  Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och hitta den `<UserJourneys>` element. Om elementet inte finns kan du lägga till en.
4.  Klistra in hela innehållet i `<UserJourneys>` nod som du kopierade som underordnad till den `<UserJourneys>` element.

### <a name="display-the-button"></a>Visa knappen
Den `<ClaimsProviderSelections>` elementet definierar en lista med alternativ för val av anspråk providern och deras inbördes ordning.  `<ClaimsProviderSelection>` elementet är detsamma som en identitet provider-knappen på en registrerings-registreringen /-inloggningssida. Om du lägger till en `<ClaimsProviderSelection>` element för Microsoft-konto, en ny knapp visas när en användare finns på sidan. Lägga till det här elementet:

1.  Hitta den `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"` i användarresan som du kopierade.
2.  Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`
3.  Lägga till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med Account ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din Account anspråksprovidern:

1.  Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
2.  Lägga till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt
>   * Se till att `TechnicalProfileReferenceId` -ID har angetts till den tekniska profilen du skapade tidigare (MSA-OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient
1.  I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2.  Välj **Identitetsramverk**.
3.  Öppna den **alla principer** bladet.
4.  Välj **överföra princip**.
5.  Kontrollera **Skriv över principen om den finns** box.
6.  **Ladda upp** TrustFrameworkExtensions.xml och se till att inte misslyckas verifieringen

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av kör nu

1.  Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.
> [!NOTE]
>
>**Kör nu** kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.
2.  Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**.
3.  Du bör kunna logga in med Microsoft-konto.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Valfritt] Registrera Account anspråksprovidern för profilredigering användarresa
Du kanske vill lägga till identitetsprovider Account även användaren `ProfileEdit` användarresa. Om du vill göra den tillgänglig, upprepa vi de två sista stegen:

### <a name="display-the-button"></a>Visa knappen
1.  Öppna tilläggsfilen av din princip (till exempel TrustFrameworkExtensions.xml).
2.  Hitta den `<UserJourney>` nod som innehåller `Id="ProfileEdit"` i användarresan som du kopierade.
3.  Leta upp den `<OrchestrationStep>` nod som innehåller `Order="1"`
4.  Lägga till följande XML-kodstycke under `<ClaimsProviderSelections>` nod:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd
1.  Hitta den `<OrchestrationStep>` som innehåller `Order="2"` i den `<UserJourney>` noden.
2.  Lägga till följande XML-kodstycke under `<ClaimsExchanges>` nod:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Testa den anpassade profilredigering principen med hjälp av kör nu
1.  Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.
2.  Öppna **B2C_1A_ProfileEdit**, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**.
3.  Du bör kunna logga in med Microsoft-konto.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna
Valfritt: Vi rekommenderar att du skapar ditt scenario med en egen anpassad princip för filer när du har slutfört komma igång med anpassade principer igenom istället för att använda dessa exempelfilerna.  [Exempel-principfiler för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
