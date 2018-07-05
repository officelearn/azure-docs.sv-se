---
title: Kom igång med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Hur du kommer igång med Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e25103d2fcbfc70be7f96f5c0e5fa6abe13fe393
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446749"
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: Kom igång med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du slutför stegen i den här artikeln är en anpassad princip har stöd för ”lokalt konto” registrering eller inloggning via ett e-postadress och lösenord. Du kommer också att förbereda din miljö för att lägga till identitetsleverantörer (till exempel Facebook eller Azure Active Directory). Vi rekommenderar att du slutfört de här stegen innan du läsa mer om andra användningsområden för Identitetsramverk för Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter bör du kontrollera att du har en Azure AD B2C-klient, som är en behållare för alla användare, appar, principer och mer. Om du inte har ett redan, måste du [skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). Vi rekommenderar starkt att alla utvecklare att slutföra Azure AD B2C inbyggd princip genomgångar och konfigurera sina program med inbyggda principer innan du fortsätter. Dina program fungerar med båda typerna av principer när du gör en mindre ändring principnamnet för att anropa en anpassad princip.

>[!NOTE]
>För att komma åt anpassad princip för redigering, behöver du en giltig Azure-prenumeration länkad till din klient. Om du inte gjort [länkat din Azure AD B2C-klient till en Azure-prenumeration](active-directory-b2c-how-to-enable-billing.md) eller din Azure-prenumeration är inaktiverad, knappen Identitetsramverk är inte tillgängligt.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Lägg till nycklar för signering och kryptering till din B2C-klient för användning av anpassade principer

1. Öppna den **Identitetsramverk** bladet i inställningarna för din Azure AD B2C-klient.
2. Välj **Principnycklar** att visa nycklarna som är tillgängliga i din klient.
3. Skapa B2C_1A_TokenSigningKeyContainer om det inte finns:<br>
    a. Välj **Lägg till**. <br>
    b. Välj **generera**.<br>
    c. För **namn**, använda `TokenSigningKeyContainer`. <br> 
    Prefixet `B2C_1A_` kan läggas till automatiskt.<br>
    d. För **nyckeltyp**, använda **RSA**.<br>
    e. För **datum**, Använd standardinställningarna. <br>
    f. För **nyckelanvändning**, använda **signatur**.<br>
    g. Välj **Skapa**.<br>
4. Skapa B2C_1A_TokenEncryptionKeyContainer om det inte finns:<br>
 a. Välj **Lägg till**.<br>
 b. Välj **generera**.<br>
 c. För **namn**, använda `TokenEncryptionKeyContainer`. <br>
   Prefixet `B2C_1A`_ kan läggas till automatiskt.<br>
 d. För **nyckeltyp**, använda **RSA**.<br>
 e. För **datum**, Använd standardinställningarna.<br>
 f. För **nyckelanvändning**, använda **kryptering**.<br>
 g. Välj **Skapa**.<br>
5. Skapa B2C_1A_FacebookSecret. <br>
Om du redan har en hemlighet för Facebook-program, lägga till den som en principnyckel till din klient. I annat fall måste du skapa nyckeln med ett platshållarvärde så att dina principer valideras.<br>
 a. Välj **Lägg till**.<br>
 b. För **alternativ**, använda **manuell**.<br>
 c. För **namn**, använda `FacebookSecret`. <br>
 Prefixet `B2C_1A_` kan läggas till automatiskt.<br>
 d. I den **hemlighet** anger din FacebookSecret från developers.facebook.com eller `0` som platshållare. *Detta är inte ditt Facebook-app-ID.* <br>
 e. För **nyckelanvändning**, använda **signatur**. <br>
 f. Välj **skapa** och bekräfta skapas.

## <a name="register-identity-experience-framework-applications"></a>Registrera Identitetsramverk program

Azure AD B2C måste du registrera två extra program som används av motorn för att registrera dig och logga in användare.

>[!NOTE]
>Du måste skapa två program för att aktivera inloggning med lokala konton: IdentityExperienceFramework (en webbapp) och ProxyIdentityExperienceFramework (en inbyggd app) med delegerad behörighet från IdentityExperienceFramework app. Lokala konton finns bara i din klientorganisation. Dina användare registrera sig med en unik e-postadress/lösenord kombination åtkomst till din klient-registrerade program.

### <a name="create-the-identityexperienceframework-application"></a>Skapa IdentityExperienceFramework-program

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md).
2. Öppna den **Azure Active Directory** bladet (inte den **Azure AD B2C** bladet). Du kan behöva välja **fler tjänster** att hitta den.
3. Välj **Appregistreringar**.
4. Välj **Ny programregistrering**.
   * För **namn**, använda `IdentityExperienceFramework`.
   * För **programtyp**, använda **Web app/API**.
   * För **inloggnings-URL**, använda `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, där `yourtenant` är ditt domännamn i Azure AD B2C-klient.
5. Välj **Skapa**.
6. När den har skapats väljer du det nyligen skapade programmet **IdentityExperienceFramework**.<br>
   * Välj **egenskaper**.<br>
   * Kopiera program-ID och spara den till senare.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Skapa ProxyIdentityExperienceFramework-program

1. Välj **Appregistreringar**.
1. Välj **Ny programregistrering**.
   * För **namn**, använda `ProxyIdentityExperienceFramework`.
   * För **programtyp**, använda **interna**.
   * För **omdirigerings-URI**, använda `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, där `yourtenant` är din Azure AD B2C-klient.
1. Välj **Skapa**.
1. När den har skapats, väljer du programmet **ProxyIdentityExperienceFramework**.<br>
   * Välj **egenskaper**. <br>
   * Kopiera program-ID och spara den till senare.
1. Välj **behörigheter som krävs för**.
1. Välj **Lägg till**.
1. Välj **Välj en API**.
1. Sök efter namnet IdentityExperienceFramework. Välj **IdentityExperienceFramework** i resultatet och klicka sedan på **Välj**.
1. Markera kryssrutan bredvid **åtkomst IdentityExperienceFramework**, och klicka sedan på **Välj**.
1. Välj **Done** (Klar).
1. Välj **bevilja behörigheter**, och bekräfta sedan genom att välja **Ja**.

## <a name="download-starter-pack-and-modify-policies"></a>Ladda ned startpaket och ändra principer

Anpassade principer är en uppsättning XML-filer som behöver överföras till din Azure AD B2C-klient. Vi tillhandahåller starter hanteringspaket för att komma igång snabbt. Varje startpaket i följande lista innehåller det lägsta antalet tekniska profiler och användaren resor som behövs för att uppnå de scenarier som beskrivs:
 * LocalAccounts. Kan du använda lokala konton.
 * SocialAccounts. Möjliggör användning av sociala (eller federerade) konton.
 * **SocialAndLocalAccounts**. Vi använder den här filen för den här genomgången.
 * SocialAndLocalAccountsWithMFA. Alternativ för social, lokal och Multi-Factor Authentication ingår.

Varje startpaket innehåller:

* Den [grundläggande filen](active-directory-b2c-overview-custom.md#policy-files) av principen. Det krävs några ändringar för basen.
* Den [tilläggsfil](active-directory-b2c-overview-custom.md#policy-files) av principen.  Den här filen är där de flesta konfigurationsändringar görs.
* [Förlitande part filer](active-directory-b2c-overview-custom.md#policy-files) är uppgiftsspecifika filer som anropas av ditt program.

>[!NOTE]
>Om XML-redigerare har stöd för verifiering, verifiera filer mot TrustFrameworkPolicy_0.3.0.0.xsd XML-schemat som finns i rotkatalogen för startpaket. XML-schemavalideringen identifierar fel innan du laddar upp.

 Vi sätter igång:

1. Hämta active-directory-b2c-custom-policy-starterpack från GitHub. [Hämta ZIP-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller köra

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Öppna mappen SocialAndLocalAccounts.  Grundläggande filen (TrustFrameworkBase.xml) i den här mappen innehåller innehåll som behövs för både lokala och sociala/företagets konton. Sociala innehållet stör inte stegen för lokala konton konfigurera och komma igång.
3. Öppna TrustFrameworkBase.xml. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), ett enkelt redigeringsprogram för olika plattformar.
4. I roten `TrustFrameworkPolicy` element, uppdatera den `TenantId` och `PublicPolicyUri` attribut, ersätta `yourtenant.onmicrosoft.com` med domännamnet för din Azure AD B2C-klient:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` är namnet på principen som visas i portalen och det namn som den här principfilen refererar till andra principfiler.

5. Spara filen.
6. Öppna TrustFrameworkExtensions.xml. Gör samma två ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient. Gör samma ersättningen i den `<TenantId>` element i tre ändringar totalt. Spara filen.
7. Öppna SignUpOrSignIn.xml. Göra samma ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient på tre platser. Spara filen.
8. Öppna lösenordsåterställning och profilera redigera filer. Göra samma ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient på tre platser i varje fil. Spara filerna.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Lägg till program-ID: N i en egen princip
Lägg till program-ID i tilläggsfilen (`TrustFrameworkExtensions.xml`):

1. Hitta elementet i tilläggsfilen (TrustFrameworkExtensions.xml) `<TechnicalProfile Id="login-NonInteractive">`.
2. Ersätt båda förekomsterna av `IdentityExperienceFrameworkAppId` med program-ID för programmet för Identitetsupplevelse som du skapade tidigare. Här är ett exempel:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Ersätt båda förekomsterna av `ProxyIdentityExperienceFrameworkAppId` med program-ID för det Identitetsramverk för Proxy-program som du skapade tidigare.
4. Spara tilläggsfilen.

## <a name="upload-the-policies-to-your-tenant"></a>Ladda upp principer till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2. Välj **Identitetsramverk**.
3. Välj **överföra princip**.

    >[!WARNING]
    >Anpassad princip-filer måste överföras i följande ordning:

1. Ladda upp TrustFrameworkBase.xml.
2. Ladda upp TrustFrameworkExtensions.xml.
3. Ladda upp SignUpOrSignin.xml.
4. Ladda upp din andra principfiler.

När en fil har överförts, namnet på principfilen som är tillagt med `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen med hjälp av kör nu

1. Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.

   >[!NOTE]
   >**Kör nu** kräver minst ett program att vara förväg registrerade på klienten. Program måste registreras i B2C-klienten, antingen genom att använda den **program** val av menyn i Azure AD B2C eller genom att använda den Identitetsramverk för att anropa både inbyggda och anpassade principer. Det krävs bara en registrering per program.<br><br>
   Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.  

2. Öppna B2C_1A_signup_signin, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**.

3. Du ska kunna registrera dig med en e-postadress.

4. Logga in med samma konto för att bekräfta att du har korrekt konfiguration.

>[!NOTE]
>En vanlig orsak till inloggningsfel är en felaktigt konfigurerad IdentityExperienceFramework-app.


## <a name="next-steps"></a>Nästa steg

### <a name="add-facebook-as-an-identity-provider"></a>Lägg till Facebook som identitetsprovider
Så här skapar Facebook:
1. [Konfigurera en Facebook-programmet i developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Lägg till Facebook-programhemlighet i din Azure AD B2C-klient](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. Ersätt värdet i policyfilen TrustFrameworkExtensions `client_id` med Facebook program-ID:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Ladda upp principfilen som TrustFrameworkExtensions.xml till din klient.
5. Testa med **kör nu** eller genom att aktivera principen direkt från ditt registrerade program.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Lägg till Azure Active Directory som identitetsprovider
Grundläggande filen används i den här guiden för att komma igång redan innehåller några av det innehåll som du behöver för att lägga till andra identitetsleverantörer. Information om hur du konfigurerar inloggningar finns i den [Azure Active Directory B2C: Logga in med hjälp av Azure AD-konton](active-directory-b2c-setup-aad-custom.md) artikeln.

En översikt över anpassade principer i Azure AD B2C som använder den Identitetsramverk finns i den [Azure Active Directory B2C: anpassade principer](active-directory-b2c-overview-custom.md) artikeln. 
