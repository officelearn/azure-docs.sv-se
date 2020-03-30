---
title: Kom igång med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig hur du kommer igång med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189335"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Komma igång med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Anpassade principer](custom-policy-overview.md) är konfigurationsfiler som definierar beteendet för din Azure Active Directory B2C-klientorganisation (Azure AD B2C). I den här artikeln skapar du en anpassad princip som stöder lokal kontoanmälan eller inloggning med hjälp av en e-postadress och ett lösenord. Du förbereder också din miljö för att lägga till identitetsleverantörer.

## <a name="prerequisites"></a>Krav

- Om du inte redan har en [skapar du en Azure AD B2C-klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
- [Registrera ditt program](tutorial-register-applications.md) i klienten som du skapade så att det kan kommunicera med Azure AD B2C.
- Slutför stegen i [Konfigurera registrering och inloggning med ett Facebook-konto](identity-provider-facebook.md) för att konfigurera ett Facebook-program. Även om ett Facebook-program inte krävs för att använda anpassade principer används det i den här genomgången för att demonstrera aktivera social inloggning i en anpassad princip.

## <a name="add-signing-and-encryption-keys"></a>Lägga till signerings- och krypteringsnycklar

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **Katalog + Prenumeration** i portalverktygsfältet och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Sök efter och välj **Azure AD B2C**i Azure-portalen .
1. Välj **Identity Experience Framework**under **Principer**på översiktssidan.

### <a name="create-the-signing-key"></a>Skapa signeringsnyckeln

1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Generate`väljer du .
1. Skriv **Name**i `TokenSigningKeyContainer`Namn . Prefixet `B2C_1A_` kan läggas till automatiskt.
1. För **nyckeltyp**väljer du **RSA**.
1. För **nyckelanvändning**väljer du **Signatur**.
1. Välj **Skapa**.

### <a name="create-the-encryption-key"></a>Skapa krypteringsnyckeln

1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Generate`väljer du .
1. Skriv **Name**i `TokenEncryptionKeyContainer`Namn . Prefixet `B2C_1A`_ kan läggas till automatiskt.
1. För **nyckeltyp**väljer du **RSA**.
1. För **nyckelanvändning**väljer du **Kryptering**.
1. Välj **Skapa**.

### <a name="create-the-facebook-key"></a>Skapa Facebook-tangenten

Lägg till [apphemligheten för](identity-provider-facebook.md) ditt Facebook-program som en principnyckel. Du kan använda Apphemligheten för det program som du skapade som en del av den här artikelns förutsättningar.

1. Välj **Principnycklar** och välj sedan **Lägg till**.
1. För **Alternativ** `Manual`väljer du .
1. För **Namn** `FacebookSecret`anger du . Prefixet `B2C_1A_` kan läggas till automatiskt.
1. I **Secret**anger du *apphemligheten* för din Facebook-applikation från developers.facebook.com. Det här värdet är det hemliga, inte program-ID: et.
1. För **nyckelanvändning**väljer du **Signatur**.
1. Välj **Skapa**.

## <a name="register-identity-experience-framework-applications"></a>Registrera ramprogram för identitetsupplevelse

Azure AD B2C kräver att du registrerar två program som används för att registrera och logga in användare med lokala konton: *IdentityExperienceFramework*, ett webb-API och *ProxyIdentityExperienceFramework*, en inbyggd app med delegerad behörighet till Appen IdentityExperienceFramework. Dina användare kan registrera sig med en e-postadress eller ett användarnamn och ett lösenord för att komma åt dina klientregistrerade program, vilket skapar ett "lokalt konto". Lokala konton finns bara i din Azure AD B2C-klient.

Du behöver bara registrera dessa två program i din Azure AD B2C-klient en gång.

### <a name="register-the-identityexperienceframework-application"></a>Registrera identityexperienceframework-programmet

Om du vill registrera ett program i din Azure AD B2C-klient kan du använda **appregistreringarna (Legacy)** eller vår nya enhetliga **appregistreringar (förhandsversion)** upplevelse. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter och välj **Azure Active Directory**i Azure-portalen .
1. Välj **Appregistreringar (Legacy)** under Hantera på översiktsmenyn **i**Azure Active **Directory** .
1. Välj **Ny programregistrering**.
1. För **Namn** `IdentityExperienceFramework`anger du .
1. För **programtyp**väljer du **Webbapp/API**.
1. För **inloggnings-URL** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`anger `your-tenant-name` du , där är ditt Azure AD B2C-klientnamn. Alla webbadresser ska nu använda [b2clogin.com](b2clogin.md).
1. Välj **Skapa**. När det har skapats kopierar du program-ID:et och sparar det för att använda det senare.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. För **Namn** `IdentityExperienceFramework`anger du .
1. Under **Kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**.
1. Under **Omdirigera URI**väljer du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` **Webb**och anger sedan , var `your-tenant-name` är ditt Azure AD B2C-klientdomännamn.
1. Markera kryssrutan Bevilja *administratörsgodkännande för att öppna och offline_access behörigheter* under **Behörigheter.**
1. Välj **Registrera**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg.

Visa sedan API:et genom att lägga till ett scope:

1. Under **Hantera**väljer du **Exponera ett API**.
1. Välj **Lägg till ett scope**och välj sedan Spara och **fortsätt** att acceptera standardprogram-ID-URI.
1. Ange följande värden för att skapa ett scope som tillåter anpassad principkörning i din Azure AD B2C-klient:
    * **Namn på omfattning:**`user_impersonation`
    * **Visningsnamn för administratörsmedgivande:**`Access IdentityExperienceFramework`
    * **Beskrivning av administratörssamtycke:**`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Välj **Lägg till omfattning**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrera programmet ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Program](#tab/applications/)

1. I **Appregistreringar (Äldre)** väljer du **Ny programregistrering**.
1. För **Namn** `ProxyIdentityExperienceFramework`anger du .
1. För **programtyp**väljer du **Native**.
1. För **Omdirigera URI**anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, där `your-tenant-name` är din Azure AD B2C-klient.
1. Välj **Skapa**. När det har skapats kopierar du program-ID:et och sparar det för att använda det senare.
1. Välj **Inställningar**och välj sedan **Obligatoriska behörigheter**och välj sedan **Lägg till**.
1. Välj **Välj ett API,** sök efter och välj **IdentityExperienceFramework**och klicka sedan på **Välj**.
1. Markera kryssrutan bredvid Åtkomst till **IdentityExperienceFramework**, klicka på **Markera**och klicka sedan på **Klar**.
1. Välj **Bevilja behörigheter**och bekräfta sedan genom att välja **Ja**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan **Ny registrering**.
1. För **Namn** `ProxyIdentityExperienceFramework`anger du .
1. Under **Kontotyper som stöds**väljer du Konton i den här **organisationskatalogen**.
1. Under **Omdirigera URI**använder du listrutan för att välja **Offentlig klient/inbyggt (mobilt & skrivbord).**
1. För **Omdirigera URI**anger du `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, där `your-tenant-name` är din Azure AD B2C-klient.
1. Markera kryssrutan Bevilja *administratörsgodkännande för att öppna och offline_access behörigheter* under **Behörigheter.**
1. Välj **Registrera**.
1. Registrera **program-ID:t (klient)** för användning i ett senare steg.

Ange sedan att programmet ska behandlas som en offentlig klient:

1. Välj **Autentisering**under **Hantera**.
1. Välj **Prova den nya upplevelsen** (om den visas).
1. Aktivera **Behandla program som en offentlig klient** under Avancerade **inställningar**(välj **Ja**).
1. Välj **Spara**.

Nu bevilja behörigheter till API-scope som du avslöjade tidigare i *IdentityExperienceFramework-registreringen:*

1. Under **Hantera**väljer du **API-behörigheter**.
1. Under **Konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Mina API:er** och välj sedan programmet **IdentityExperienceFramework.**
1. Under **Behörighet**väljer du det **user_impersonation** omfång som du definierade tidigare.
1. Välj **Lägg till behörigheter**. Enligt anvisningarna väntar du några minuter innan du fortsätter till nästa steg.
1. Välj **Bevilja administratörsmedgivande för (ditt klientnamn)**.
1. Välj ditt inloggade administratörskonto eller logga in med ett konto i din Azure AD B2C-klient som har tilldelats åtminstone *rollen cloud application administrator.*
1. Välj **Acceptera**.
1. Välj **Uppdatera**och kontrollera sedan att "Beviljad för ..." visas under **Status** för båda scopea. Det kan ta några minuter innan behörigheterna sprids.

* * *

## <a name="custom-policy-starter-pack"></a>Startpaket för anpassad princip

Anpassade principer är en uppsättning XML-filer som du laddar upp till din Azure AD B2C-klient för att definiera tekniska profiler och användarresor. Vi erbjuder startpaket med flera färdiga policyer för att komma igång snabbt. Vart och ett av dessa startpaket innehåller det minsta antalet tekniska profiler och användarresor som behövs för att uppnå de beskrivna scenarierna:

- **LocalAccounts** - Aktiverar endast användning av lokala konton.
- **SocialAccounts** - Aktiverar endast användning av sociala (eller federerade) konton.
- **SocialAndLocalAccounts** - Möjliggör användning av både lokala och sociala konton.
- **SocialAndLocalAccountsWithMFA** - Aktiverar sociala, lokala och multifaktorautentiseringsalternativ.

Varje startpaket innehåller:

- **Basfil** - Få ändringar krävs för basen. Exempel: *TrustFrameworkBase.xml*
- **Tilläggsfil** - Den här filen är där de flesta konfigurationsändringar görs. Exempel: *TrustFrameworkExtensions.xml*
- **Förlitande part filer** - Uppgiftsspecifika filer som anropas av ditt program. Exempel: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

I den här artikeln redigerar du anpassade XML-principfiler i startpaketet **SocialAndLocalAccounts.** Om du behöver en XML-redigerare kan du prova [Visual Studio Code](https://code.visualstudio.com/download), en lätt plattformsoberoende redigerare.

### <a name="get-the-starter-pack"></a>Skaffa startpaketet

Hämta de anpassade startpaketen från GitHub och uppdatera sedan XML-filerna i startpaketet för SocialAndLocalAccounts med ditt Azure AD B2C-klientnamn.

1. [Hämta ZIP-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller klona databasen:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. I alla filer i katalogen **SocialAndLocalAccounts ersätter** du strängen `yourtenant` med namnet på din Azure AD B2C-klient.

    Om namnet på din B2C-klient är *kontossant*blir `yourtenant.onmicrosoft.com` alla `contosotenant.onmicrosoft.com`förekomster av .

### <a name="add-application-ids-to-the-custom-policy"></a>Lägga till program-ID:er i den anpassade principen

Lägg till program-ID:t i tilläggsfilen *TrustFrameworkExtensions.xml*.

1. Öppna `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** och hitta `<TechnicalProfile Id="login-NonInteractive">`elementet .
1. Ersätt båda instanserna med `IdentityExperienceFrameworkAppId` program-ID:et för IdentityExperienceFramework-programmet som du skapade tidigare.
1. Ersätt båda instanserna med `ProxyIdentityExperienceFrameworkAppId` program-ID:et för proxyidentityExperienceFramework-programmet som du skapade tidigare.
1. Spara filen.

## <a name="upload-the-policies"></a>Ladda upp policyerna

1. Välj menyalternativet **Identity Experience Framework** i din B2C-klient i Azure-portalen.
1. Välj **Ladda upp anpassad princip**.
1. I den här ordningen laddar du upp principfilerna:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *Registrera Dig Själv tillSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

När du laddar upp filerna lägger Azure till prefixet `B2C_1A_` till varje.

> [!TIP]
> Om XML-redigeraren stöder validering validerar du filerna mot `TrustFrameworkPolicy_0.3.0.0.xsd` XML-schemat som finns i startpaketets rotkatalog. XML-schemavaliderar fel innan du laddar upp.

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **Anpassade principer**väljer du **B2C_1A_signup_signin**.
1. För **Välj program** på översiktssidan för den anpassade principen väljer du webbprogrammet *webapp1* som du tidigare har registrerat.
1. Kontrollera att **svars-URL:en** är `https://jwt.ms`.
1. Välj **Kör nu**.
1. Registrera dig med en e-postadress.
1. Välj **Kör nu** igen.
1. Logga in med samma konto för att bekräfta att du har rätt konfiguration.

## <a name="add-facebook-as-an-identity-provider"></a>Lägga till Facebook som identitetsleverantör

Som nämnts i Förutsättningar krävs *inte* Facebook för att använda anpassade [policyer,](#prerequisites)men används här för att visa hur du kan aktivera federerad social inloggning i en anpassad policy.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** I filen ersätter du `client_id` värdet för med Facebook-applikations-ID:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Ladda upp *filen TrustFrameworkExtensions.xml* till din klientorganisation.
1. Under **Anpassade principer**väljer du **B2C_1A_signup_signin**.
1. Välj **Kör nu** och välj Facebook för att logga in med Facebook och testa den anpassade principen.

## <a name="next-steps"></a>Nästa steg

Försök sedan att lägga till Azure Active Directory (Azure AD) som identitetsprovider. Basfilen som används i den här komma igång-guiden innehåller redan en del av innehållet som du behöver för att lägga till andra identitetsleverantörer som Azure AD.

Information om hur du konfigurerar Azure AD som och identitetsprovider finns [i Konfigurera registrering och inloggning med ett Azure Active Directory-konto med active directory B2C-anpassade principer](identity-provider-azure-ad-single-tenant-custom.md).
