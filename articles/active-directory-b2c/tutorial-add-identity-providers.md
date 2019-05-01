---
title: Självstudiekurs – lägga till Identitetsproviders i dina program – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till Identitetsproviders i dina program i Azure Active Directory B2C med Azure portal.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c1bd78442f6a7218dde3710236766117816c42fe
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710129"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Självstudier: Lägg till Identitetsproviders i dina program i Azure Active Directory B2C

Du kanske vill att användarna ska kunna logga in på dina program med olika identitetsprovidrar. En *identitetsprovider* skapar, underhåller och hanterar identitetsinformation samt autentiseringstjänster till program. Du kan lägga till identitetsleverantörer som stöds av Azure Active Directory (Azure AD) B2C till din [användarflöden](active-directory-b2c-reference-policies.md) med Azure portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa identiteten provider-program
> * Lägga till identitetsleverantör man i din klient
> * Lägg till identitetsleverantör man i ditt användarflöde

Du använder vanligtvis bara en identitetsprovider i dina program, men du har möjlighet att lägga till fler. Den här självstudien visar hur du lägger till en identitetsprovider i Azure AD och en identitetsprovider i Facebook till ditt program. Det är valfritt att lägga till båda dessa Identitetsproviders i ditt program. Du kan också lägga till andra identitetsleverantörer, till exempel [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), eller [Twitter](active-directory-b2c-setup-twitter-app.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera dig och logga in på ditt program. 

## <a name="create-applications"></a>Skapa program

Identitet provider-program innehåller identifieraren och nyckel för att möjliggöra kommunikation med din Azure AD B2C-klient. I det här avsnittet av kursen skapar du en Azure AD-program och en Facebook-programmet som du får ID: n och enhetsnycklar som ska lägga till identitetsleverantör man i din klient. Om du vill lägga till bara en av identitetsprovidrarna, behöver du bara skapa program för providern.

### <a name="create-an-azure-active-directory-application"></a>Skapa ett Azure Active Directory-program

Om du vill aktivera inloggning för användare från Azure AD, som du behöver registrera ett program i Azure AD-klient. Azure AD-klienten är inte samma som din Azure AD B2C-klient.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller Azure AD-klienten genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD-klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **appregistreringar**.
4. Välj **Ny programregistrering**.
5. Ange ett namn för ditt program. Till exempel `Azure AD B2C App`.
6. För den **programtyp**väljer `Web app / API`.
7. För den **inloggnings-URL**, ange följande URL i alla gemener, där `your-B2C-tenant-name` ersätts med namnet på din Azure AD B2C-klient.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Till exempel `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Alla URL: er bör nu använda [b2clogin.com](b2clogin.md).

8. Klicka på **Skapa**. Kopiera den **program-ID** som ska användas senare.
9. Välj programmet och välj sedan **inställningar**.
10. Välj **nycklar**, ange nyckelbeskrivningen, Välj en varaktighet och klicka sedan på **spara**. Kopiera värdet för nyckeln så att du kan använda den senare i den här självstudien.

### <a name="create-a-facebook-application"></a>Skapa ett Facebook-program

Om du vill använda en Facebook-konto som identitetsprovider i Azure AD B2C måste du skapa ett program på Facebook. Om du inte redan har ett Facebook-konto, kan du hämta den på [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Logga in på [Facebook för utvecklare](https://developers.facebook.com/) med dina autentiseringsuppgifter för Facebook-konto.
2. Om du inte redan gjort det, behöver du registrera som Facebook-utvecklare. Om du vill registrera, Välj **registrera** acceptera Facebooks principer i det övre högra hörnet på sidan och gör registreringen.
3. Välj **Mina appar** och klicka sedan på **lägga till en ny App**. 
4. Ange en **visningsnamn** och en giltig **kontakta e-post**.
5. Klicka på **skapa App-ID**. Du kan krävas för att acceptera Facebook-plattformen principer och slutföra en online säkerhetskontrollen.
6. Välj **inställningar** > **grundläggande**.
7. Välj en **kategori**, till exempel `Business and Pages`. Det här värdet krävs av Facebook, men inte använts för Azure AD B2C.
8. Längst ned på sidan Välj **Lägg till plattform**, och välj sedan **webbplats**.
9. I **webbplatsens URL**, ange `https://your-tenant-name.b2clogin.com/` ersätter `your-tenant-name` med namnet på din klient. Ange en URL för den **princip Sekretesswebbadress**, till exempel `http://www.contoso.com`. Princip-URL är en sida som ska underhållas för att tillhandahålla Sekretessinformation för ditt program.
10. Välj **Spara ändringar**.
11. Kopiera värdet för längst ned på sidan **App-ID**. 
12. Klicka på **visa** och kopiera värdet för **Apphemlighet**. Du kan använda båda för att konfigurera Facebook som en identitetsprovider i din klient. **Apphemlighet** är en viktig säkerhetsuppgift för autentisering.
13. Välj **produkter**, och välj sedan **konfigurera** under **Facebook-inloggning**.
14. Välj **inställningar** under **Facebook-inloggning**.
15. I **giltig OAuth omdirigerings-URI: er**, ange `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Ersätt `your-tenant-name` med namnet på din klient. Klicka på **spara ändringar** längst ned på sidan.
16. För att ditt Facebook-program tillgängliga för Azure AD B2C, klickar du på den **Status** väljare längst upp höger på sidan och ge den värdet **på**. Klicka på **Bekräfta**. Nu bör statusen ändras från **utveckling** till **Live**.

## <a name="add-the-identity-providers"></a>Lägg till identitetsprovidrarna

När du har skapat programmet för den identitetsprovider som du vill lägga till, lägger du till identitetsleverantören din klient.

### <a name="add-the-azure-active-directory-identity-provider"></a>Lägg till Azure Active Directory-identitetsprovider

1. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
2. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
3. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
4. Ange en **namn**. Ange till exempel *Contoso Azure AD*.
5. Välj **identifiera providertyp**väljer **öppna ID Connect (förhandsversion)**, och klicka sedan på **OK**.
6. Klicka på **ställa in den här identitetsprovidern**
7. För **metadata_url**, ange följande URL ersätter `your-AD-tenant-domain` med domännamnet för din Azure AD-klient.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Till exempel `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. För **klient-ID**, ange program-ID som du sparade tidigare och för **klienthemlighet**, Ange nyckelvärdet som du antecknade tidigare.
9. Alternativt kan du ange ett värde för **Domain_hint**. Till exempel `ContosoAD`. 
10. Klicka på **OK**.
11. Välj **mappa den här identitetsproviderns anspråk** och ange följande anspråk:
    
    - För **användar-ID**, ange `oid`.
    - För **visningsnamn**, ange `name`.
    - För **Förnamn**, ange `given_name`.
    - För **efternamn**, ange `family_name`.
    - För **e-post**, ange `unique_name`.

12. Klicka på **OK**, och klicka sedan på **skapa** att spara din konfiguration.

### <a name="add-the-facebook-identity-provider"></a>Lägg till Facebook-identitetsprovider

1. Välj **identitetsprovidrar**, och välj sedan **Lägg till**.
2. Ange en **namn**. Ange till exempel *Facebook*.
3. Välj **identifiera providertyp**väljer **Facebook**, och klicka på **OK**.
4. Välj **ställa in den här identitetsprovidern** och ange det ID som du antecknade tidigare som den **klient-ID**. Ange App-hemlighet som du registrerade som den **klienthemlighet**.
5. Klicka på **OK** och klicka sedan på **skapa** att spara din Facebook-konfiguration.

## <a name="update-the-user-flow"></a>Uppdatera användarflödet

Du har skapat ett användarflöde i självstudien som du har slutfört som en del av förutsättningarna för registrering och inloggning med namnet *B2C_1_signupsignin1*. I det här avsnittet ska du lägga till identitetsleverantörer till den *B2C_1_signupsignin1* användarflödet.

1. Välj **användarflöden (principer)**, och välj sedan den *B2C_1_signupsignin1* användarflödet.
2. Välj **identitetsprovidrar**väljer den **Facebook** och **Contoso Azure AD** identitetsleverantörer som du har lagt till.
3. Välj **Spara**.

### <a name="test-the-user-flow"></a>Testa användarflödet

1. På sidan översikt av användarflöde som du har skapat väljer **kör användarflödet**.
2. För **programmet**, Välj webbprogrammet med namnet *webapp1* som du tidigare har registrerat. Den **svars-URL** ska visa `https://jwt.ms`.
3. Klicka på **kör användarflödet**, och sedan logga in med en identitetsprovider som du tidigare lade till.
4. Upprepa steg 1 till 3 för andra identitetsleverantörer som du har lagt till.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa identiteten provider-program
> * Lägga till identitetsleverantör man i din klient
> * Lägg till identitetsleverantör man i ditt användarflöde

> [!div class="nextstepaction"]
> [Anpassa användargränssnittet för dina program i Azure Active Directory B2C](tutorial-customize-ui.md)