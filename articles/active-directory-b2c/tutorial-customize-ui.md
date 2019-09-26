---
title: Självstudie – anpassa gränssnittet för användar upplevelser – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du anpassar användar gränssnittet för dina program i Azure Active Directory B2C att använda Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 08edf6e841dc7d389573d5e5b5ea7e043f750e76
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71291096"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Självstudier: Anpassa gränssnittet för användar upplevelser i Azure Active Directory B2C

För vanliga användar upplevelser, till exempel registrering, inloggning och profil redigering, kan du använda [användar flöden](active-directory-b2c-reference-policies.md) i Azure Active Directory B2C (Azure AD B2C). Informationen i den här självstudien hjälper dig att lära dig hur du [anpassar användar gränssnittet (UI)](customize-ui-overview.md) för dessa upplevelser med hjälp av dina egna HTML-och CSS-filer.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa anpassade användar gränssnitts filer
> * Uppdatera användar flödet för att använda filerna
> * Testa det anpassade användar gränssnittet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett användar flöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in i programmet.

## <a name="create-customization-files"></a>Skapa anpassnings filer

Du skapar ett Azure Storage-konto och en behållare och placerar sedan grundläggande HTML-och CSS-filer i behållaren.

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Även om du kan lagra dina filer på många sätt kan du i den här självstudien lagra dem i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure-prenumeration. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din prenumeration. Den här katalogen skiljer sig åt från den som innehåller din Azure B2C-klient.
3. Välj alla tjänster i det övre vänstra hörnet av Azure Portal, Sök efter och välj lagrings **konton**.
4. Välj **Lägg till**.
5. Under **resurs grupp**väljer du **Skapa ny**, anger ett namn för den nya resurs gruppen och klickar sedan på **OK**.
6. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure, mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
7. Välj platsen för lagrings kontot eller acceptera standard platsen.
8. Acceptera alla andra standardvärden, Välj **Granska + skapa**och klicka sedan på **skapa**.
9. När lagrings kontot har skapats väljer **du gå till resurs**.

### <a name="create-a-container"></a>Skapa en container

1. På sidan Översikt för lagrings kontot väljer du **blobbar**.
2. Välj **behållare**, ange ett namn för behållaren, Välj **BLOB (endast anonym Läs åtkomst för blobbar)** och klicka sedan på **OK**.

### <a name="enable-cors"></a>Aktivera CORS

 Azure AD B2C kod i en webbläsare använder en modern och standard metod för att läsa in anpassat innehåll från en URL som du anger i ett användar flöde. Resurs delning mellan ursprung (CORS) gör att begränsade resurser på en webb sida kan begäras från andra domäner.

1. I menyn väljer du **CORS**.
2. För **tillåtna ursprung**anger `https://your-tenant-name.b2clogin.com`du. Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com`. Du måste använda små bokstäver när du anger ditt klient namn.
3. För **tillåtna metoder**väljer `GET`du,`PUT`och `OPTIONS`.
4. För **tillåtna huvuden**anger du en asterisk (*).
5. För **exponerade rubriker**anger du en asterisk (*).
6. Ange 200 för **högsta ålder**.

    ![Konfigurations sidan CORS i Azure Blob Storage i Azure Portal](./media/tutorial-customize-ui/enable-cors.png)

5. Klicka på **Spara**.

### <a name="create-the-customization-files"></a>Skapa anpassnings filerna

Om du vill anpassa gränssnittet för registrerings upplevelsen börjar du med att skapa en enkel HTML-och CSS-fil. Du kan konfigurera HTML-koden som du vill, men den måste ha ett **div** -element med en identifierare `api`för. Till exempel `<div id="api"></div>`. Azure AD B2C infogar element i `api` behållaren när sidan visas.

1. Skapa följande fil i en lokal mapp och se till att du byter `your-storage-account` namn på lagrings kontot och `your-container` till namnet på den behållare som du har skapat. Till exempel `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Sidan kan utformas på det sätt som du vill, men **API** -DIV-elementet krävs för alla HTML-anpassningar som du skapar.

3. Spara filen som *Custom-UI. html*.
4. Skapa följande enkla CSS som centrerar alla element på registrerings-eller inloggnings sidan, inklusive de element som Azure AD B2C inmatning.

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

5. Spara filen som *Style. CSS*.

### <a name="upload-the-customization-files"></a>Ladda upp anpassnings filerna

I den här självstudien lagrar du de filer som du skapade i lagrings kontot så att Azure AD B2C kan komma åt dem.

1. Välj **alla tjänster** i det övre vänstra hörnet av Azure Portal, Sök efter och välj lagrings **konton**.
2. Välj det lagrings konto som du har skapat, Välj **blobbar**och välj sedan den behållare som du skapade.
3. Välj **överför**, navigera till och välj filen *Custom-UI. html* och klicka sedan på **Ladda upp**.

    ![Ladda upp BLOB-sidan i portalen med knappen Ladda upp och filer markerade](./media/tutorial-customize-ui/upload-blob.png)

4. Kopiera URL: en för filen som du laddade upp för att använda senare i självstudien.
5. Upprepa steg 3 och 4 för filen *Style. CSS* .

## <a name="update-the-user-flow"></a>Uppdatera användar flödet

1. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
2. Välj **användar flöden (principer)** och välj sedan användar flödet *B2C_1_signupsignin1* .
3. Välj **sidlayouter**och klicka sedan på **Ja** under **enhetlig registrering eller inloggnings sida**för att **använda anpassat sid innehåll**.
4. Ange URI: n för den *Custom-UI. html-* fil som du spelat in tidigare i **URI för anpassad sida**.
5. Välj **Spara**längst upp på sidan.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. I Azure AD B2C klient väljer du **användar flöden** och väljer användar flödet *B2C_1_signupsignin1* .
2. Klicka på **Kör användar flöde**längst upp på sidan.
3. Klicka på knappen **Kör användar flöde** .

    ![Sidan kör användar flöde för användar flödet för registrering eller inloggning](./media/tutorial-customize-ui/run-user-flow.png)

    Du bör se en sida som liknar följande exempel med elementen centrerade baserat på CSS-filen som du skapade:

    ![Webbläsare som visar registrerings-eller inloggnings sida med anpassade GRÄNSSNITTs element](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa anpassade användar gränssnitts filer
> * Uppdatera användar flödet för att använda filerna
> * Testa det anpassade användar gränssnittet

> [!div class="nextstepaction"]
> [Språk anpassning i Azure Active Directory B2C](active-directory-b2c-reference-language-customization.md)
