---
title: Kom igång med anpassade principer – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du kommer igång med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b414529d7756812f1e1e16d2d0184c8472c0c55f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317963"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Kom igång med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Anpassade principer](active-directory-b2c-overview-custom.md) är konfigurationsfiler som definierar beteendet för din Azure Active Directory (Azure AD) B2C-klient. I den här artikeln skapar du en anpassad princip som har stöd för lokalt konto registrerings- eller logga in med en e-postadress och lösenord. Du kan även förbereda din miljö för att lägga till identitetsleverantörer.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du inte har ett redan, måste du [skapa en Azure AD B2C-klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.
- [Registrera ditt program](tutorial-register-applications.md) i klienten som du skapade så att den kan kommunicera med Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Lägg till nycklar för signering och kryptering

1. Logga in på [Azure Portal](https://portal.azure.com/) som global administratör för din Azure AD B2C-klientorganisationen.
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient. Klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient. 
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer **Identitetsupplevelse – FÖRHANDSVERSION**.

### <a name="create-the-signing-key"></a>Skapa signeringsnyckeln

1. Välj **Principnycklar** och välj sedan **Lägg till**.
2. För **alternativ**, Välj `Generate`.
3. I **namn**, ange `TokenSigningKeyContainer`. Prefixet `B2C_1A_` kan läggas till automatiskt.
4. För **nyckeltyp**väljer **RSA**.
5. För **nyckelanvändning**väljer **signatur**.
6. Klicka på **Skapa**.

### <a name="create-the-encryption-key"></a>Skapa krypteringsnyckeln

1. Välj **Principnycklar** och välj sedan **Lägg till**.
2. För **alternativ**, Välj `Generate`.
3. I **namn**, ange `TokenEncryptionKeyContainer`. Prefixet `B2C_1A`_ kan läggas till automatiskt.
4. För **nyckeltyp**väljer **RSA**.
5. För **nyckelanvändning**väljer **kryptering**.
6. Klicka på **Skapa**.

### <a name="create-the-facebook-key"></a>Skapa Facebook-nyckel

Om du redan har en [Facebook programhemlighet](active-directory-b2c-setup-fb-app.md), lägga till den som en principnyckel i din klient. I annat fall måste du skapa nyckeln med ett platshållarvärde så att dina principer valideras.

1. Välj **Principnycklar** och välj sedan **Lägg till**.
2. För **alternativ**, Välj `Manual`.
3. För **namn**, ange `FacebookSecret`. Prefixet `B2C_1A_` kan läggas till automatiskt.
4. I **hemlighet**, ange din Facebook-hemlighet från developers.facebook.com eller `0` som platshållare. Det här värdet är hemligheten inte program-ID.
5. För **nyckelanvändning**väljer **signatur**.
6. Klicka på **Skapa**.

## <a name="register-identity-experience-framework-applications"></a>Registrera Identitetsramverk program

Azure AD B2C måste du registrera två program som används för att registrera dig och logga in användare: IdentityExperienceFramework (en webbapp) och ProxyIdentityExperienceFramework (en inbyggd app) med delegerad behörighet från IdentityExperienceFramework app. Lokala konton finns bara i din klientorganisation. Dina användare registrera sig med en unik e-postadress/lösenord kombination åtkomst till din klient-registrerade program.

### <a name="register-the-identityexperienceframework-application"></a>Registrera IdentityExperienceFramework-program

1. Välj **alla tjänster** i det övre vänstra hörnet i Azure Portal Sök efter och välj **appregistreringar**.
2. Välj **Ny programregistrering**.
3. För **namn**, ange `IdentityExperienceFramework`.
4. För **programtyp**, Välj **Web app/API**.
5. För **inloggnings-URL**, ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, där `your-tenant-name` är ditt domännamn i Azure AD B2C-klient.
6. Klicka på **Skapa**. 
7. När den har skapats kan du kopiera program-ID och spara den för senare användning.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrera ProxyIdentityExperienceFramework-program

1. Välj **appregistreringar**, och välj sedan **ny programregistrering**.
2. För **namn**, ange `ProxyIdentityExperienceFramework`.
3. För **programtyp**, Välj **interna**.
4. För **omdirigerings-URI**, ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, där `yourtenant` är din Azure AD B2C-klient.
5. Klicka på **Skapa**. När den har skapats kan du kopiera program-ID och spara den för senare användning.
6. På sidan Inställningar väljer **nödvändiga behörigheter**, och välj sedan **Lägg till**.
7. Välj **Välj en API**, Sök efter och välj **IdentityExperienceFramework**, och klicka sedan på **Välj**.
9. Markera kryssrutan bredvid **åtkomst IdentityExperienceFramework**, klickar du på **Välj**, och klicka sedan på **klar**.
10. Välj **bevilja behörigheter**, och bekräfta sedan genom att välja **Ja**.

## <a name="download-starter-pack-and-modify-policies"></a>Ladda ned startpaket och ändra principer

Anpassade principer är en uppsättning XML-filer som behöver överföras till din Azure AD B2C-klient. Starter packs av filer tillhandahålls för att komma igång snabbt. Varje startpaket i följande lista innehåller det lägsta antalet tekniska profiler och användaren resor som behövs för att uppnå de scenarier som beskrivs:

- LocalAccounts – kan du använda lokala konton.
- SocialAccounts - möjliggör användning av sociala (eller federerade) konton.
- SocialAndLocalAccounts - gör båda använda lokala konton och konton i sociala medier.
- SocialAndLocalAccountsWithMFA - aktiverar social, lokal och Multi-Factor Authentication-alternativ.

Varje startpaket innehåller:

- Grundläggande filen. Det krävs några ändringar för basen.
- Tilläggsfilen.  Den här filen är där de flesta konfigurationsändringar görs.
- Förlitande part-filer. Uppgiftsspecifika filer som anropas av ditt program.

>[!NOTE]
>Om XML-redigerare har stöd för verifiering, verifiera filer mot TrustFrameworkPolicy_0.3.0.0.xsd XML-schemat som finns i rotkatalogen för startpaket. XML-schemavalideringen identifierar fel innan du laddar upp.

1. [Hämta ZIP-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller köra:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Redigera alla från ersättningen av filer i mappen SocialAndLocalAccounts `yourtenant` med namnet på din klient. Till exempel `contosoTenant.onmicrosoft.com`. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), ett enkelt redigeringsprogram för olika plattformar.

### <a name="add-application-ids-to-the-custom-policy"></a>Lägg till program-ID: N till den anpassade principen

Lägg till program-ID i tilläggsfilen *TrustFrameworkExtensions.xml*.

1. Öppna den *TrustFrameworkExtensions.xml* filen och hitta elementet `<TechnicalProfile Id="login-NonInteractive">`.
2. Ersätt båda förekomsterna av `IdentityExperienceFrameworkAppId` med program-ID för programmet för Identitetsupplevelse som du skapade tidigare. Ersätt båda förekomsterna av `ProxyIdentityExperienceFrameworkAppId` med program-ID för det Identitetsramverk för Proxy-program som du skapade tidigare.
3. Spara tilläggsfilen.

## <a name="upload-the-policies"></a>Ladda upp principer

1. På sidan anpassade principer för Identitetsramverk väljer **ladda upp principen**.
1. I den här ordningen, överföra *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , och *PasswordReset.xml*. När en fil har överförts, namnet på principfilen som är tillagt med `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. På sidan anpassade principer väljer **B2C_1A_signup_signin**.
2. För **Välj program** på översiktssidan av den anpassade principen väljer du webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Se till att den **svars-URL** är `https://jwt.ms`.
3. Välj **kör nu**.
4. Du ska kunna registrera dig med en e-postadress.
5. Logga in med samma konto för att bekräfta att du har korrekt konfiguration.

## <a name="add-facebook-as-an-identity-provider"></a>Lägg till Facebook som identitetsprovider

1. Konfigurera en [Facebook-programmet](active-directory-b2c-setup-fb-app.md).
2. I den *TrustFrameworkExtensions.xml* filen och Ersätt värdet för `client_id` med Facebook program-ID:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Ladda upp den *TrustFrameworkExtensions.xml* filen till din klient.
4. Testa med **kör nu** eller genom att aktivera principen direkt från ditt registrerade program.

## <a name="next-steps"></a>Nästa steg

- Lägg till Azure Active Directory som en identitetsleverantör. Grundläggande filen används i den här guiden för att komma igång redan innehåller några av det innehåll som du behöver för att lägga till andra identitetsleverantörer. Information om hur du konfigurerar inloggningar finns i den [konfigurera registrera dig och logga in med ett Azure Active Directory-konto med hjälp av anpassade principer för Active Directory B2C](active-directory-b2c-setup-aad-custom.md) artikeln.
