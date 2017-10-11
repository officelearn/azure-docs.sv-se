---
title: "Azure Active Directory B2C: Komma igång med anpassade principer | Microsoft Docs"
description: "Hur du kommer igång med Azure Active Directory B2C anpassade principer"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 4f14dbf4b66f10290cd4f98d56a005f97cc6a207
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: Komma igång med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

När du slutför stegen i den här artikeln är en anpassad princip stöder ”lokalt konto” registrering eller inloggning via en e-postadress och lösenord. Du kommer också att förbereda din miljö för att lägga till identitetsleverantörer (till exempel Facebook eller Azure Active Directory). Vi rekommenderar att du ska kunna slutföra de här stegen innan du läsa mer om annan användning av Azure Active Directory (AD Azure) B2C identitet upplevelse Framework.

## <a name="prerequisites"></a>Krav

Innan du fortsätter bör du kontrollera att du har en Azure AD B2C-klient som är en behållare för alla användare, appar, principer och mer. Om du inte har någon redan, behöver du [skapa en Azure AD B2C-klient](active-directory-b2c-get-started.md). Vi rekommenderar starkt att alla utvecklare att slutföra genomgångar Azure AD B2C inbyggd princip och konfigurera sina program med inbyggt principer innan du fortsätter. Dina program fungerar med båda typer av principer när du gör en mindre ändring i principnamn att anropa den anpassade principen.

>[!NOTE]
>För att komma åt anpassad princip för redigering, måste en giltig Azure-prenumeration länkad till din klient. Om du inte gjort [länkade Azure AD B2C-klient till en Azure-prenumeration](active-directory-b2c-how-to-enable-billing.md) eller Azure-prenumerationen är inaktiverad, knappen identitet upplevelse Framework är inte tillgängligt.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Lägg till nycklar för signering och kryptering för din B2C-klient för användning av anpassade principer

1. Öppna den **identitet upplevelse Framework** bladet i inställningarna för Azure AD B2C-klient.
2. Välj **princip nycklar** att visa nycklarna som är tillgängliga i din klient.
3. Skapa B2C_1A_TokenSigningKeyContainer om det inte finns:<br>
    a. Välj **Lägg till**. <br>
    b. Välj **generera**.<br>
    c. För **namn**, Använd `TokenSigningKeyContainer`. <br> 
    Prefixet `B2C_1A_` kan läggas till automatiskt.<br>
    d. För **nyckeltyp**, använda **RSA**.<br>
    e. För **datum**, använda standardinställningarna. <br>
    f. För **nyckelanvändning**, använda **signatur**.<br>
    g. Välj **Skapa**.<br>
4. Skapa B2C_1A_TokenEncryptionKeyContainer om det inte finns:<br>
 a. Välj **Lägg till**.<br>
 b. Välj **generera**.<br>
 c. För **namn**, Använd `TokenEncryptionKeyContainer`. <br>
   Prefixet `B2C_1A`_ kan läggas till automatiskt.<br>
 d. För **nyckeltyp**, använda **RSA**.<br>
 e. För **datum**, använda standardinställningarna.<br>
 f. För **nyckelanvändning**, använda **kryptering**.<br>
 g. Välj **Skapa**.<br>
5. Skapa B2C_1A_FacebookSecret. <br>
Om du redan har en hemlighet för Facebook-programmet kan lägga till den som en princip för din klient. Annars måste du skapa nyckeln med ett platshållarvärde så att dina principer valideras.<br>
 a. Välj **Lägg till**.<br>
 b. För **alternativ**, använda **manuell**.<br>
 c. För **namn**, Använd `FacebookSecret`. <br>
 Prefixet `B2C_1A_` kan läggas till automatiskt.<br>
 d. I den **hemlighet** ange din FacebookSecret från developers.facebook.com eller `0` som platshållare. *Detta är inte din Facebook app-ID.* <br>
 e. För **nyckelanvändning**, använda **signatur**. <br>
 f. Välj **skapa** och bekräfta skapas.

## <a name="register-identity-experience-framework-applications"></a>Registrera identiteten upplevelse Framework-program

Azure AD B2C måste du registrera två extra program som används av motorn för att registrera och logga in användare.

>[!NOTE]
>Du måste skapa två program för att aktivera inloggning med lokala konton: IdentityExperienceFramework (webbprogram) och ProxyIdentityExperienceFramework (en intern app) med delegerad behörighet från IdentityExperienceFramework app. Lokala konton finns bara i din klient. Användarna logga med en unik e-postadressen och lösenordet för åtkomst till din klient-registrerade program.

### <a name="create-the-identityexperienceframework-application"></a>Skapa IdentityExperienceFramework-program

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md).
2. Öppna den **Azure Active Directory** bladet (inte den **Azure AD B2C** bladet). Du kan behöva välja **fler tjänster** att hitta den.
3. Välj **App registreringar**.
4. Välj **nya appregistrering**.
   * För **namn**, Använd `IdentityExperienceFramework`.
   * För **programtyp**, använda **webb-app/API**.
   * För **inloggnings-URL**, använda `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, där `yourtenant` är ditt domännamn i Azure AD B2C-klient.
5. Välj **Skapa**.
6. När den har skapats, Välj nyligen skapade programmet **IdentityExperienceFramework**.<br>
   * Välj **egenskaper**.<br>
   * Kopiera program-ID och spara den för senare.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Skapa ProxyIdentityExperienceFramework-program

1. Välj **App registreringar**.
1. Välj **nya appregistrering**.
   * För **namn**, Använd `ProxyIdentityExperienceFramework`.
   * För **programtyp**, använda **interna**.
   * För **omdirigerings-URI**, använda `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, där `yourtenant` är Azure AD B2C-klient.
1. Välj **Skapa**.
1. När den har skapats, Välj programmet **ProxyIdentityExperienceFramework**.<br>
   * Välj **egenskaper**. <br>
   * Kopiera program-ID och spara den för senare.
1. Välj **nödvändiga behörigheter**.
1. Välj **Lägg till**.
1. Välj **väljer en API**.
1. Sök efter namnet IdentityExperienceFramework. Välj **IdentityExperienceFramework** i resultaten och klickar sedan på **Välj**.
1. Markera kryssrutan bredvid **åtkomst IdentityExperienceFramework**, och klicka sedan på **Välj**.
1. Välj **klar**.
1. Välj **bevilja med**, och sedan bekräfta genom att välja **Ja**.

## <a name="download-starter-pack-and-modify-policies"></a>Hämta startpaket och ändra principer

Anpassade principer är en uppsättning XML-filer som behöver överföras till din Azure AD B2C-klient. Vi ger starter hanteringspaket för att komma igång snabbare. Varje startpaket i följande lista innehåller det lägsta antalet tekniska profiler och användaren resor som behövs för att uppnå scenarier som beskrivs:
 * LocalAccounts. Använda lokala konton.
 * SocialAccounts. Tillåter användningen av sociala (eller externa) konton.
 * **SocialAndLocalAccounts**. Vi använder den här filen för den här genomgången.
 * SocialAndLocalAccountsWithMFA. Alternativ för social, lokal och Multifaktorautentisering ingår.

Varje startpaket innehåller:

* Den [grundläggande filen](active-directory-b2c-overview-custom.md#policy-files) av principen. Några ändringar krävs för basen.
* Den [tilläggsfilen](active-directory-b2c-overview-custom.md#policy-files) av principen.  Den här filen är där de flesta konfigurationsändringar görs.
* [Förlitande part filer](active-directory-b2c-overview-custom.md#policy-files) uppgiftsspecifika filer som anropas av ditt program.

>[!NOTE]
>Validera filerna mot TrustFrameworkPolicy_0.3.0.0.xsd XML-schemat som finns i rotkatalogen för startpaket om XML-redigerare har stöd för verifiering. XML-schemavalideringen identifierar fel innan du laddar upp.

 Nu sätter vi igång:

1. Hämta active-directory-b2c-custom-policy-starterpack från GitHub. [Hämta ZIP-filen](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) eller köra

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Öppna mappen SocialAndLocalAccounts.  Bas-fil (TrustFrameworkBase.xml) i den här mappen innehåller innehåll som behövs för både lokala och social företagets konton. Sociala innehållet stör inte stegen för att hämta lokala konton och körs.
3. Öppna TrustFrameworkBase.xml. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), en enkel plattformsoberoende redigerare.
4. I roten `TrustFrameworkPolicy` element, uppdatera det `TenantId` och `PublicPolicyUri` attribut, ersätter `yourtenant.onmicrosoft.com` med domännamn för din Azure AD B2C-klient:
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
   >`PolicyId`är namnet som den här principfilen refererar till andra principfiler och principnamn som visas i portalen.

5. Spara filen.
6. Öppna TrustFrameworkExtensions.xml. Gör samma två ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient. Gör samma ersättningen i den `<TenantId>` element för totalt tre ändringar. Spara filen.
7. Öppna SignUpOrSignIn.xml. Göra samma ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient på tre platser. Spara filen.
8. Öppna lösenordsåterställningen och redigera filer-profilen. Göra samma ändringar genom att ersätta `yourtenant.onmicrosoft.com` med din Azure AD B2C-klient på tre platser i varje fil. Spara filerna.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Lägg till program-ID: N i en anpassad princip
Lägg till program-ID i tilläggsfilen (`TrustFrameworkExtensions.xml`):

1. Hitta element i tilläggsfilen (TrustFrameworkExtensions.xml) `<TechnicalProfile Id="login-NonInteractive">`.
2. Ersätt båda förekomster av `IdentityExperienceFrameworkAppId` med program-ID för identitet upplevelse Framework-program som du skapade tidigare. Här är ett exempel:

   ```xml
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Ersätt båda förekomster av `ProxyIdentityExperienceFrameworkAppId` med program-ID för Proxy identitet upplevelse Framework-program som du skapade tidigare.
4. Spara tilläggsfilen.

## <a name="upload-the-policies-to-your-tenant"></a>Ladda upp principer till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och öppna den **Azure AD B2C** bladet.
2. Välj **identitet upplevelse Framework**.
3. Välj **överföra princip**.

    >[!WARNING]
    >Anpassad princip-filer måste överföras i följande ordning:

1. Överför TrustFrameworkBase.xml.
2. Överför TrustFrameworkExtensions.xml.
3. Överför SignUpOrSignin.xml.
4. Ladda upp dina övriga principfiler.

När en fil har överförts, namnet på principfilen inledd med `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testa den anpassade principen genom att använda Kör nu

1. Öppna **Azure AD B2C inställningar** och gå till **identitet upplevelse Framework**.

   >[!NOTE]
   >**Kör nu** kräver minst ett program till preregistered för innehavaren. Program som måste registreras i B2C-klient, antingen genom att använda den **program** Menyvalet i Azure AD B2C eller med hjälp av ramverket identitet erfarenhet för att anropa både inbyggda och anpassade principer. Bara en registrering per program krävs.<br><br>
   Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.  

2. Öppna B2C_1A_signup_signin förlitande part (RP) anpassade principer som du har överfört. Välj **kör nu**.

3. Du ska kunna logga med en e-postadress.

4. Logga in med samma konto för att bekräfta att du har rätt konfiguration.

>[!NOTE]
>En vanlig orsak till inloggningsfel är en felaktigt konfigurerad IdentityExperienceFramework app.


## <a name="next-steps"></a>Nästa steg

### <a name="add-facebook-as-an-identity-provider"></a>Lägg till Facebook som en identitetsleverantör
Att ställa in Facebook:
1. [Konfigurera en Facebook-programmet för developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Lägg till Facebook-apphemligheten för din Azure AD B2C-klient](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. Ersätt värdet för principfilen TrustFrameworkExtensions `client_id` med Facebook program-ID:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Överför principfilen TrustFrameworkExtensions.xml till din klient.
5. Testa med **kör nu** eller genom att aktivera principen direkt från din registrerade programmet.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Lägg till Azure Active Directory som en identitetsleverantör
Grundläggande filen används redan i den här Kom igång-guide innehåller del av det innehåll som du behöver för att lägga till andra identitetsleverantörer. Information om hur du konfigurerar inloggningar finns i [Azure Active Directory B2C: Logga in med hjälp av Azure AD-konton](active-directory-b2c-setup-aad-custom.md) artikel.

En översikt över anpassade principer som använder ramverket identitet upplevelse i Azure AD B2C finns i [Azure Active Directory B2C: anpassade policys](active-directory-b2c-overview-custom.md) artikel. 
