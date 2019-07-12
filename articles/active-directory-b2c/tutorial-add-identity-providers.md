---
title: Självstudiekurs – lägga till Identitetsproviders i dina program – Azure Active Directory B2C
description: Lär dig hur du lägger till Identitetsproviders i dina program i Azure Active Directory B2C med Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655227"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Självstudier: Lägg till Identitetsproviders i dina program i Azure Active Directory B2C

Du kanske vill att användarna ska kunna logga in på dina program med olika identitetsprovidrar. En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. Du kan lägga till identitetsleverantörer som stöds av Azure Active Directory (Azure AD) B2C till din [användarflöden](active-directory-b2c-reference-policies.md) med Azure portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa identiteten provider-program
> * Lägga till identitetsleverantör man i din klient
> * Lägg till identitetsleverantör man i ditt användarflöde

Du använder vanligtvis bara en identitetsprovider i dina program, men du har möjlighet att lägga till fler. Den här självstudien visar hur du lägger till en identitetsprovider i Azure AD och en identitetsprovider i Facebook till ditt program. Det är valfritt att lägga till båda dessa Identitetsproviders i ditt program. Du kan också lägga till andra identitetsleverantörer, till exempel [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), eller [Twitter](active-directory-b2c-setup-twitter-app.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera dig och logga in på ditt program.

## <a name="create-applications"></a>Skapa program

Identitet provider-program innehåller identifieraren och nyckel för att möjliggöra kommunikation med din Azure AD B2C-klient. I det här avsnittet av kursen skapar du en Azure AD-program och en Facebook-programmet som du får ID: n och enhetsnycklar som ska lägga till identitetsleverantör man i din klient. Om du vill lägga till bara en av identitetsprovidrarna, behöver du bara skapa program för providern.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Om du vill aktivera inloggning för användare från Azure AD, som du behöver registrera ett program i Azure AD-klient. Azure AD-klienten är inte samma som din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Kontrollera att du använder den katalog som innehåller Azure AD-klienten genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD-klient.
1. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
1. Välj **ny registrering**.
1. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
1. Acceptera valet av **konton i den här organisationens katalogen** för det här programmet.
1. För den **omdirigerings-URI**, acceptera värdet för **Web** och ange följande URL i alla gemener, ersätta `your-B2C-tenant-name` med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Till exempel `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Alla URL: er bör nu använda [b2clogin.com](b2clogin.md).

1. Välj **registrera**sedan registrerar den **(klient)-ID: T** som du använder i ett senare steg.
1. Under **hantera** i program-menyn väljer du **certifikat och hemligheter**och välj sedan **nya klienthemligheten**.
1. Ange en **beskrivning** för klienthemligheten. Till exempel `Azure AD B2C App Secret`.
1. Välj giltighetsperiod. Det här programmet accepterar valet av **i 1 år**.
1. Välj **Lägg till**, registrera värdet för den nya klienthemligheten som du använder i ett senare steg.

### <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda en Facebook-konto som identitetsprovider i Azure AD B2C måste du skapa ett program på Facebook. Om du inte redan har ett Facebook-konto, kan du hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina autentiseringsuppgifter för Facebook-konto.
1. Om du inte redan gjort det, behöver du registrera som Facebook-utvecklare. Om du vill göra detta, Välj **börjar** acceptera Facebooks principer i det övre högra hörnet på sidan och gör registreringen.
1. Välj **Mina appar** och sedan **skapa App**.
1. Ange en **visningsnamn** och en giltig **kontakta e-post**.
1. Klicka på **skapa App-ID**. Du kan behöva du acceptera Facebook-plattformen principer och slutföra en online säkerhetskontrollen.
1. Välj **inställningar** > **grundläggande**.
1. Välj en **kategori**, till exempel `Business and Pages`. Det här värdet krävs av Facebook men används inte av Azure AD B2C.
1. Längst ned på sidan Välj **Lägg till plattform**, och välj sedan **webbplats**.
1. I **webbplatsens URL**, ange `https://your-tenant-name.b2clogin.com/` ersätter `your-tenant-name` med namnet på din klient.
1. Ange en URL för den **princip Sekretesswebbadress**, till exempel `http://www.contoso.com/`. Sekretesswebbadress för principen är en sida som ska underhållas för att tillhandahålla Sekretessinformation för ditt program.
1. Välj **Spara ändringar**.
1. Anteckna värdet för längst ned på sidan **App-ID**.
1. Bredvid **Apphemlighet**väljer **visa** och registrera värdet. Du kan använda både App-ID och Apphemlighet för att konfigurera Facebook som en identitetsprovider i din klient. **Apphemlighet** är en viktig autentiseringsuppgift som ska du lagra på ett säkert sätt.
1. Klicka på plustecknet bredvid **produkter**, sedan under **Facebook-inloggning**väljer **konfigurera**.
1. Under **Facebook-inloggning** i den vänstra menyn och väljer **inställningar**.
1. I **giltig OAuth omdirigerings-URI: er**, ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant-name` med namnet på din klient. Välj **spara ändringar** längst ned på sidan.
1. Frigör ditt Facebook-program till Azure AD B2C genom att klicka på den **Status** väljare längst upp höger på sidan och aktivera den **på** offentliggöra programmet och klicka sedan på **bekräfta** . Nu bör statusen ändras från **utveckling** till **Live**.

## <a name="add-the-identity-providers"></a>Lägg till identitetsprovidrarna

När du har skapat programmet för den identitetsprovider som du vill lägga till, lägger du till identitetsleverantören din klient.

### <a name="add-the-azure-active-directory-identity-provider"></a>Lägg till Azure Active Directory-identitetsprovider

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
1. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
1. Ange en **namn**. Ange till exempel *Contoso Azure AD*.
1. Välj **identifiera providertyp**väljer **öppna ID Connect (förhandsversion)** , och klicka sedan på **OK**.
1. Klicka på **ställa in den här identitetsprovidern**
1. För **metadata_url**, ange följande URL ersätter `your-AD-tenant-domain` med domännamnet för din Azure AD-klient.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. För **klient-ID**, ange den *(klient)-ID: T* som du antecknade tidigare.
1. För **klienthemlighet**, ange den *klienthemlighet* värde som du antecknade tidigare.
1. Alternativt kan du ange ett värde för **Domain_hint**. Till exempel `ContosoAD`. [Tips för domänen](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att påskynda användaren till sina federerad IdP-inloggningssida. Eller de kan användas av ett program för flera innehavare för att påskynda användaren direkt till den anpassade Azure AD-inloggningssida för klienten.
1. Välj **OK**.
1. Välj **mappa den här identitetsproviderns anspråk** och ange följande anspråk:

    - För **användar-ID**, ange `oid`.
    - För **visningsnamn**, ange `name`.
    - För **Förnamn**, ange `given_name`.
    - För **efternamn**, ange `family_name`.
    - För **e-post**, ange `unique_name`.

1. Välj **OK**och välj sedan **skapa** att spara din konfiguration.

### <a name="add-the-facebook-identity-provider"></a>Lägg till Facebook-identitetsprovider

1. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
1. Ange en **namn**. Ange till exempel *Facebook*.
1. Välj **identifiera providertyp**väljer **Facebook**och välj sedan **OK**.
1. Välj **ställa in den här identitetsprovidern** och ange den *App-ID* som du antecknade tidigare som den **klient-ID**.
1. Ange den *Apphemlighet* som du registrerade som den **klienthemlighet**.
1. Välj **OK** och välj sedan **skapa** att spara din Facebook-konfiguration.

## <a name="update-the-user-flow"></a>Uppdatera användarflödet

Du har skapat ett användarflöde i självstudien som du har slutfört som en del av förutsättningarna för registrering och inloggning med namnet *B2C_1_signupsignin1*. I det här avsnittet ska du lägga till identitetsleverantörer till den *B2C_1_signupsignin1* användarflödet.

1. Välj **användarflöden (principer)** , och välj sedan den *B2C_1_signupsignin1* användarflödet.
2. Välj **identitetsprovidrar**väljer den **Facebook** och **Contoso Azure AD** identitetsleverantörer som du har lagt till.
3. Välj **Spara**.

## <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan översikt av användarflöde som du har skapat väljer **kör användarflödet**.
1. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
1. Välj **kör användarflödet**, och sedan logga in med en identitetsprovider som du tidigare lade till.
1. Upprepa steg 1 till 3 för andra identitetsleverantörer som du har lagt till.

Om inloggningen åtgärden lyckas är du omdirigeras till `https://jwt.ms` som visar den avkodas Token, liknar:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa identiteten provider-program
> * Lägga till identitetsleverantör man i din klient
> * Lägg till identitetsleverantör man i ditt användarflöde

Lärt dig hur du anpassar Användargränssnittet för de sidor som visas för användare som en del av deras identitetsupplevelse i dina program:

> [!div class="nextstepaction"]
> [Anpassa användargränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md)
