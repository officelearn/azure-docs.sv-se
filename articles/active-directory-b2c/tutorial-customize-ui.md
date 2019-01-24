---
title: Självstudiekurs – anpassa användargränssnittet för dina program i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du anpassar användargränssnittet i dina program i Azure Active Directory B2C med Azure portal.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5d97a313c347aefbdda14b70e78aa09188da59ef
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855796"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>Självstudier: Anpassa användargränssnittet för dina program i Azure Active Directory B2C

För allt vanligare användarupplevelser, som registrering, inloggning och profilredigering, du kan använda [användarflöden](active-directory-b2c-reference-policies.md) i Azure Active Directory (Azure AD) B2C. Informationen i den här självstudien hjälper dig att lära dig hur du [anpassa användargränssnittet (UI)](customize-ui-overview.md) av dessa upplevelser med din egen HTML och CSS-filer.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa filer för anpassning av Användargränssnittet
> * Skapa ett användarflöde i registrera dig och logga in som använder filerna som
> * Testa det anpassade Användargränssnittet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har skapat dina egna [Azure AD B2C-klient](tutorial-create-tenant.md), skapa en nu. Du kan använda en befintlig klient om du har skapat en i en tidigare självstudie.

## <a name="create-customization-files"></a>Skapa anpassningsfiler

Du skapar ett Azure storage-konto och en behållare och placera grundläggande HTML och CSS-filer i behållaren.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Även om du kan lagra dina filer på många sätt för den här självstudien, lagra dem i [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md).

1. Kontrollera att du använder den katalog som innehåller din Azure-prenumeration. Välj den **katalog- och prenumerationsfilter** på den översta menyn och välj den katalog som innehåller din prenumeration. Den här katalogen är annorlunda än det som innehåller din Azure B2C-klient.

    ![Växla till prenumerationskatalogen](./media/tutorial-customize-ui/switch-directories.png)

2. Välj alla tjänster i det övre vänstra hörnet i Azure Portal, Sök efter och välj **lagringskonton**. 
3. Välj **Lägg till**.
4. Under **resursgrupp**väljer **Skapa nytt**, ange ett namn för den nya resursgruppen och klicka sedan på **OK**.
5. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure, mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
6. Välj platsen för lagringskontot eller acceptera standardplatsen. 
7. Acceptera alla övriga standardvärden, Välj **granska + skapa**, och klicka sedan på **skapa**.
8. När lagringskontot har skapats kan du välja **gå till resurs**.

### <a name="create-a-container"></a>Skapa en container

1. På översiktssidan för lagringskontot väljer **Blobar**.
2. Välj **behållare**, ange ett namn för behållaren, Välj **Blob (anonym läsåtkomst endast för blobbar)**, och klicka sedan på **OK**.

### <a name="enable-cors"></a>Aktivera CORS

 Azure AD B2C-kod i en webbläsare använder en modern och standard-metod för att läsa in anpassat innehåll från en URL som du anger i ett användarflöde. Cross-origin resource sharing (CORS) tillåter begränsade resurser på en webbsida att begäras från andra domäner.

1. I menyn, Välj **CORS**.
2. För **tillåtna ursprung**, ange `https://your-tenant-name.b2clogin.com`. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com`. Du måste använda gemener när du anger namnet på din klientorganisation.
3. För **tillåtna metoder**, markerar du båda `GET` och `OPTIONS`.
4. För **tillåtna huvuden**, anger du en asterisk (*).
5. För **exponerade rubriker**, anger du en asterisk (*).
6. För **maximal ålder**, ange 200.

    ![Aktivera CORS](./media/tutorial-customize-ui/enable-cors.png)

5. Klicka på **Spara**.

### <a name="create-the-customization-files"></a>Skapa anpassningsfiler

För att anpassa Användargränssnittet för inloggning, börja med att skapa en enkel HTML och CSS-fil. Du kan konfigurera din HTML som du vill, men det måste ha en **div** element med en identifierare för `api`. Till exempel `<div id="api"></div>`. Azure AD B2C lägger in element i den `api` behållaren när sidan visas.

1. Skapa följande fil i en lokal mapp, och se till att du ändrar `your-storage-account` till namnet på lagringskontot och `your-container` till namnet på behållaren som du skapade. Till exempel `https://store1.blob.core.windows.net/b2c/style.css`.

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    Sidan kan utformas på något sätt som du vill, men **api** olika element som krävs för en anpassning HTML-fil som du skapar. 

3. Spara filen som *anpassade ui.html*.
4. Skapa följande enkla CSS som Datacenter alla element på sidan registrering eller inloggning, inklusive de element som Azure AD B2C lägger in.

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. Spara filen som *style.css*.

### <a name="upload-the-customization-files"></a>Ladda upp anpassningsfiler

I den här självstudien får lagra du de filer som du skapade i storage-konto så att Azure AD B2C kan komma åt dem.

1. Välj **alla tjänster** i det övre vänstra hörnet i Azure Portal Sök efter och välj **lagringskonton**.
2. Välj det lagringskonto du skapade, Välj **Blobar**, och välj sedan den behållare som du skapade.
3. Välj **överför**, navigera till och markera den *anpassade ui.html* filen och klicka sedan på **överför**.

    ![Ladda upp anpassningsfiler](./media/tutorial-customize-ui/upload-blob.png)

4. Kopiera URL: en för filen du laddade upp för att använda senare i självstudien.
5. Upprepa steg 3 och 4 för den *style.css* fil.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Skapa ett användarflöde för registrering och logga in

För att slutföra stegen i den här självstudien måste du skapa ett flöde för test-program och registrera dig eller logga in användaren i Azure AD B2C. Du kan använda de principer som beskrivs i den här kursen och andra användarupplevelser, till exempel profilredigering.

### <a name="create-an-azure-ad-b2c-application"></a>Skapa ett Azure AD B2C-program

Kommunikation med Azure AD B2C sker via ett program som du skapar i din klient. Följande steg skapar ett program som omdirigerar den autentiseringstoken som returneras till [ https://jwt.ms ](https://jwt.ms).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrollera att du använder den katalog som innehåller din Azure AD B2C-klient genom att klicka på den **katalog- och prenumerationsfilter** i den översta menyn och välja den katalog som innehåller din klient.
3. Välj **alla tjänster** i det övre vänstra hörnet av Azure-portalen och Sök efter och välj **Azure AD B2C**.
4. Välj **program**, och välj sedan **Lägg till**.
5. Ange ett namn för programmet, till exempel *testapp1*.
6. För **Webbapp / webb-API**väljer `Yes`, och ange sedan `https://jwt.ms` för den **svars-URL**.
7. Klicka på **Skapa**.

### <a name="create-the-user-flow"></a>Skapa användarflödet

Om du vill testa din anpassningsfiler kan du skapa ett användarflöde för inbyggda registrerings- eller logga in som använder det program som du skapade tidigare.

1. I din Azure AD B2C-klient väljer **användarflöden**, och klicka sedan på **nytt användarflöde**.
2. På fliken **Rekommenderat** klickar du på **Sign up and sign in** (Registrera och logga in).
3. Ange ett namn för användarflödet. Till exempel *signup_signin*. Prefixet *B2C_1* läggs automatiskt till i namnet när användarflödet har skapats.
4. Under **identitetsprovidrar**väljer **e-post registrering**.
5. Under **användarattribut och anspråk**, klickar du på **visa fler**.
6. I den **samla in attributet** kolumn, Välj de attribut som du vill samla in från kunden under registreringen. Till exempel **Land/Region**, **visningsnamn**, och **postnummer**.
7. I den **returnerar anspråk** kolumnen välja anspråk som du vill ska returneras i de auktoriseringstoken som skickas tillbaka till programmet efter en lyckad registrering eller inloggning upplevelse. Välj till exempel **visningsnamn**, **identitetsprovidrar**, **postnummer**, **ny användare** och **användarobjekt-id**.
8. Klicka på **OK**.
9. Klicka på **Skapa**.
10. Under **anpassa**väljer **sidan layouter**. Välj **enhetliga sidan för registrering eller inloggning**, och klicka på **Ja** för **Använd anpassat sidinnehåll**.
11. I **anpassad sida URI**, anger du Webbadressen för den *anpassade ui.html* -fil som du antecknade tidigare.
12. Överst på sidan klickar du på **spara**.

## <a name="test-the-user-flow"></a>Testa användarflödet

1. I din Azure AD B2C-klient väljer **användarflöden** och välj det användarflöde som du skapade. Till exempel *B2C_1_signup_signin*.
2. Överst på sidan klickar du på **kör användarflödet**.
3. Klicka på den **kör användarflödet** knappen.

    ![Kör registrering eller inloggning användarflödet](./media/tutorial-customize-ui/run-user-flow.png)

    Du bör se en sida som liknar följande exempel med de element som inriktade baserat på CSS-filen som du skapade:

    ![Användaren flow resultat](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du:

> [!div class="checklist"]
> * Skapa filer för anpassning av Användargränssnittet
> * Skapa ett användarflöde i registrera dig och logga in som använder filerna som
> * Testa det anpassade Användargränssnittet

> [!div class="nextstepaction"]
> [Språkanpassning i Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)