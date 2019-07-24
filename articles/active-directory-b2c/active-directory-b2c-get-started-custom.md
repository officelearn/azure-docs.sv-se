---
title: Kom igång med anpassade principer – Azure Active Directory B2C
description: Lär dig hur du kommer igång med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83269a5ae0d2e5fb7ae2651dbc27926c910a0e03
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302493"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Kom igång med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Anpassade principer](active-directory-b2c-overview-custom.md) är konfigurationsfiler som definierar beteendet för din Azure Active Directory (Azure AD) B2C-klient. I den här artikeln skapar du en anpassad princip som stöder registrering av lokalt konto eller inloggning med hjälp av en e-postadress och ett lösen ord. Du förbereder också din miljö för att lägga till identitets leverantörer.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte redan har en, måste du [skapa en Azure AD B2C-klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
- [Registrera ditt program](tutorial-register-applications.md) i klient organisationen som du skapade så att den kan kommunicera med Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Lägg till signerings-och krypterings nycklar

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Klicka på **katalogen och prenumerations filtret** på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.

### <a name="create-the-signing-key"></a>Skapa signerings nyckeln

1. Välj **princip nycklar** och välj sedan **Lägg till**.
2. För **alternativ**väljer `Generate`du.
3. I **namn**anger `TokenSigningKeyContainer`du. Prefixet `B2C_1A_` kan läggas till automatiskt.
4. För **nyckel typ**väljer du **RSA**.
5. För **nyckel användning**väljer du **signatur**.
6. Klicka på **Skapa**.

### <a name="create-the-encryption-key"></a>Skapa krypterings nyckeln

1. Välj **princip nycklar** och välj sedan **Lägg till**.
2. För **alternativ**väljer `Generate`du.
3. I **namn**anger `TokenEncryptionKeyContainer`du. Prefixet `B2C_1A`_ kan läggas till automatiskt.
4. För **nyckel typ**väljer du **RSA**.
5. För **nyckel användning**väljer du **kryptering**.
6. Klicka på **Skapa**.

### <a name="create-the-facebook-key"></a>Skapa Facebook-nyckeln

Om du redan har en [Facebook](active-directory-b2c-setup-fb-app.md)-programhemlighet, lägger du till den som en princip nyckel för din klient. Annars måste du skapa nyckeln med ett plats hållare-värde så att principerna klarar verifieringen.

1. Välj **princip nycklar** och välj sedan **Lägg till**.
2. För **alternativ**väljer `Manual`du.
3. Som **namn**anger `FacebookSecret`du. Prefixet `B2C_1A_` kan läggas till automatiskt.
4. I **hemlighet**anger du din Facebook-hemlighet från Developers.Facebook.com `0` eller som plats hållare. Det här värdet är hemligheten, inte program-ID: t.
5. För **nyckel användning**väljer du **signatur**.
6. Klicka på **Skapa**.

## <a name="register-identity-experience-framework-applications"></a>Registrera program för identitets upplevelse Framework

Azure AD B2C kräver att du registrerar två program som används för att registrera dig och logga in användare: IdentityExperienceFramework (en webbapp) och ProxyIdentityExperienceFramework (en inbyggd app) med delegerad behörighet från IdentityExperienceFramework-appen. Lokala konton finns bara i din klient. Användarna registrerar sig med en unik kombination av e-postadress/lösen ord för att få åtkomst till dina klient registrerade program.

### <a name="register-the-identityexperienceframework-application"></a>Registrera IdentityExperienceFramework-programmet

1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal.
1. Skriv `Azure Active Directory` i sökrutan.
1. Välj **Azure Active Directory** i sökresultatet.
1. Under **Hantera** i den vänstra menyn väljer du **Appregistreringar (bakåtkompatibelt)** .
1. Välj **Ny programregistrering**.
1. Som **namn**anger `IdentityExperienceFramework`du.
1. För **program typ**väljer du **webbapp/API**.
1. För **inloggnings-URL**anger `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`du, där `your-tenant-name` är ditt Azure AD B2C klient domän namn. Alla URL: er ska nu använda [b2clogin.com](b2clogin.md).
1. Klicka på **Skapa**. När den har skapats kopierar du program-ID: t och sparar det för att använda det senare.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrera ProxyIdentityExperienceFramework-programmet

1. I **Appregistreringar (bakåtkompatibelt)** väljer du **ny program registrering**.
2. Som **namn**anger `ProxyIdentityExperienceFramework`du.
3. Välj **intern**för **program typ**.
4. För omdirigerings- `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`URI anger `your-tenant-name` du, där är Azure AD B2C klient.
5. Klicka på **Skapa**. När den har skapats kopierar du program-ID: t och sparar det för att använda det senare.
6. På sidan Inställningar väljer du **nödvändiga behörigheter**och väljer sedan **Lägg till**.
7. Välj **Välj ett API**, Sök efter och välj **IdentityExperienceFramework**och klicka sedan på **Välj**.
9. Markera kryss rutan bredvid Access- **IdentityExperienceFramework**, klicka på **Välj**och sedan på **färdig**.
10. Välj **bevilja behörigheter**och bekräfta genom att välja **Ja**.

## <a name="custom-policy-starter-pack"></a>Start paket för anpassad princip

Anpassade principer är en uppsättning XML-filer som du överför till din Azure AD B2C-klient för att definiera tekniska profiler och användar resor. Vi tillhandahåller start paket med flera fördefinierade principer för att komma igång snabbt. Vart och ett av dessa start paket innehåller det minsta antalet tekniska profiler och användar resor som krävs för att uppnå de scenarier som beskrivs:

- **LocalAccounts** – tillåter endast lokala konton.
- **SocialAccounts** – tillåter endast användning av sociala (eller federerade) konton.
- **SocialAndLocalAccounts** – möjliggör användning av både lokala och sociala konton.
- **SocialAndLocalAccountsWithMFA** – aktiverar alternativ för sociala, lokala och Multi-Factor Authentication.

Varje start paket innehåller:

- **Grundläggande fil** – några ändringar krävs för basen. Exempel: *TrustFrameworkBase. XML*
- **Tilläggs fil** – den här filen är den plats där de flesta konfigurations ändringar görs. Exempel: *TrustFrameworkExtensions. XML*
- **Förlitande part-filer** – verksamhetsspecifika filer som anropas av ditt program. Exempel: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset original. XML*

I den här artikeln redigerar du anpassade XML-principfiler i **SocialAndLocalAccounts** -startpaketet. Om du behöver en XML-redigerare kan du prova [Visual Studio Code](https://code.visualstudio.com/download), en förenklad plattforms oberoende redigerare.

### <a name="get-the-starter-pack"></a>Hämta start paketet

Hämta start paketen för anpassad princip från GitHub och uppdatera sedan XML-filerna i SocialAndLocalAccounts-startpaketet med ditt Azure AD B2C klient namn.

1. [Hämta zip-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller klona databasen:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. I alla filer i katalogen **SocialAndLocalAccounts** ersätter du strängen `yourtenant` med namnet på din Azure AD B2C-klient.

    Om namnet på din B2C-klient till exempel är *contosotenant*, blir `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`alla instanser av.

### <a name="add-application-ids-to-the-custom-policy"></a>Lägga till program-ID: n i den anpassade principen

Lägg till program-ID: na i tillägg filen *TrustFrameworkExtensions. XML*.

1. Öppna `SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`och **hittaelementet.`TrustFrameworkExtensions.xml`**
1. Ersätt båda instanserna `IdentityExperienceFrameworkAppId` av med program-ID: t för det IdentityExperienceFramework-program som du skapade tidigare.
1. Ersätt båda instanserna `ProxyIdentityExperienceFrameworkAppId` av med program-ID: t för det ProxyIdentityExperienceFramework-program som du skapade tidigare.
1. Spara filen.

## <a name="upload-the-policies"></a>Överför principerna

1. Välj meny alternativet för **identitets miljö** i B2C-klienten i Azure Portal.
1. Välj **överför anpassad princip**.
1. I den här ordningen laddar du upp principfiler:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset original. XML*

När du överför filerna lägger Azure till prefixet `B2C_1A_` i varje.

> [!TIP]
> Om XML-redigeraren stöder verifiering, verifierar du filerna `TrustFrameworkPolicy_0.3.0.0.xsd` mot det XML-schema som finns i rot katalogen i Start paketet. Verifiering av XML-schema identifierar fel innan den laddas upp.

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Under **anpassade principer**väljer du **B2C_1A_signup_signin**.
1. För **Välj program** på sidan Översikt i den anpassade principen väljer du det webb program som heter *webapp1* som du tidigare har registrerat.
1. Se till att svars- **URL: en** är `https://jwt.ms`.
1. Välj **Kör nu**.
1. Registrera dig med en e-postadress.
1. Välj **Kör nu** igen.
1. Logga in med samma konto för att bekräfta att du har rätt konfiguration.

## <a name="add-facebook-as-an-identity-provider"></a>Lägg till Facebook som en identitets leverantör

1. Slutför stegen i Konfigurera [registrering och inloggning med ett Facebook-konto](active-directory-b2c-setup-fb-app.md) för att konfigurera ett Facebook-program.
1. Ersätt värdet för`client_id` med Facebook-programmets ID i **filen:`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Ladda upp filen *TrustFrameworkExtensions. XML* till din klient organisation.
1. Under **anpassade principer**väljer du **B2C_1A_signup_signin**.
1. Välj **Kör nu** och välj Facebook för att logga in med Facebook och testa den anpassade principen. Eller så anropar du principen direkt från ditt registrerade program.

## <a name="next-steps"></a>Nästa steg

Försök sedan att lägga till Azure Active Directory (Azure AD) som identitets leverantör. Bas filen som används i den här guiden för att komma igång innehåller redan en del av det innehåll som du behöver för att lägga till andra identitets leverantörer som Azure AD.

Information om hur du konfigurerar Azure AD som identitets leverantör finns i [Konfigurera registrering och inloggning med ett Azure Active Directory konto med hjälp av anpassade principer för Active Directory B2C](active-directory-b2c-setup-aad-custom.md).
