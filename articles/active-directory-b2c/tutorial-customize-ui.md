---
title: 'Självstudiekurs: Anpassa användargränssnittet'
titleSuffix: Azure AD B2C
description: Lär dig hur du anpassar användargränssnittet för dina program i Azure Active Directory B2C med hjälp av Azure-portalen.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b2b2bc8dd4e60348553228b8b418df252a8c426a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186258"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>Självstudiekurs: Anpassa gränssnittet för användarupplevelser i Azure Active Directory B2C

Om du vill ha fler vanliga användarupplevelser, till exempel registrering, inloggning och profilredigering, kan du använda [användarflöden](user-flow-overview.md) i Azure Active Directory B2C (Azure AD B2C). Informationen i den här självstudien hjälper dig att lära dig hur du [anpassar användargränssnittet (UI)](customize-ui-overview.md) av dessa upplevelser med hjälp av dina egna HTML- och CSS-filer.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa anpassningsfiler för användargränssnitt
> * Uppdatera användarflödet för att använda filerna
> * Testa det anpassade användargränssnittet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

[Skapa ett användarflöde](tutorial-create-user-flows.md) så att användarna kan registrera sig och logga in på ditt program.

## <a name="create-customization-files"></a>Skapa anpassningsfiler

Du skapar ett Azure-lagringskonto och en behållare och placerar sedan grundläggande HTML- och CSS-filer i behållaren.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Även om du kan lagra dina filer på många sätt, för den här självstudien, du lagra dem i [Azure Blob lagring](../storage/blobs/storage-blobs-introduction.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure-prenumeration. Välj **katalog + prenumerationsfilter** i den övre menyn och välj den katalog som innehåller din prenumeration. Den här katalogen skiljer sig från den som innehåller din Azure B2C-klient.
3. Välj Alla tjänster i det övre vänstra hörnet på Azure-portalen, sök efter och välj **Lagringskonton**.
4. Välj **Lägg till**.
5. Under **Resursgrupp**väljer du **Skapa nytt**, anger ett namn för den nya resursgruppen och klickar sedan på **OK**.
6. Ange ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure, mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
7. Välj lagringskontots plats eller acceptera standardplatsen.
8. Acceptera alla andra standardvärden, välj **Granska + skapa**och klicka sedan på **Skapa**.
9. När lagringskontot har skapats väljer du **Gå till resurs**.

### <a name="create-a-container"></a>Skapa en container

1. På översiktssidan för lagringskontot väljer du **Blobbar**.
2. Välj **Behållare**, ange ett namn för behållaren, välj **Blob (endast anonym läsåtkomst för blobbar)** och klicka sedan på **OK**.

### <a name="enable-cors"></a>Aktivera CORS

 Azure AD B2C-kod i en webbläsare använder en modern och standardmetod för att läsa in anpassat innehåll från en URL som du anger i ett användarflöde. Med direktresursdelning (CORS) kan begränsade resurser på en webbsida begäras från andra domäner.

1. Välj **CORS**i menyn .
2. För **Tillåtna**ursprung `https://your-tenant-name.b2clogin.com`anger du . Ersätt `your-tenant-name` med namnet på din Azure AD B2C-klient. Till exempel `https://fabrikam.b2clogin.com`. Du måste använda alla gemener när du anger ditt klientnamn.
3. För **tillåtna** `GET`metoder`PUT`väljer `OPTIONS`du , och .
4. För **tillåtna rubriker**anger du en asterisk (*).
5. För **exponerade rubriker**anger du en asterisk (*).
6. För **Max ålder**anger du 200.

    ![KONFIGURATIONS-sida för CORS i Azure Blob-lagring i Azure-portalen](./media/tutorial-customize-ui/enable-cors.png)

5. Klicka på **Spara**.

### <a name="create-the-customization-files"></a>Skapa anpassningsfilerna

Om du vill anpassa användargränssnittet för registreringsupplevelsen börjar du med att skapa en enkel HTML- och CSS-fil. Du kan konfigurera HTML-koden som du vill, men den måste `api`ha ett **div-element** med en identifierare för . Till exempel `<div id="api"></div>`. Azure AD B2C injicerar `api` element i behållaren när sidan visas.

1. I en lokal mapp skapar du följande fil `your-storage-account` och kontrollerar att `your-container` du ändrar till namnet på lagringskontot och namnet på behållaren som du skapade. Till exempel `https://store1.blob.core.windows.net/b2c/style.css`.

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

    Sidan kan utformas på något sätt du vill, men **api** div-elementet krävs för alla HTML-anpassningsfiler som du skapar.

3. Spara filen som *anpassad ui.html*.
4. Skapa följande enkla CSS som centrerar alla element på registrerings- eller inloggningssidan, inklusive de element som Azure AD B2C injicerar.

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

### <a name="upload-the-customization-files"></a>Ladda upp anpassningsfilerna

I den här självstudien lagrar du de filer som du skapade i lagringskontot så att Azure AD B2C kan komma åt dem.

1. Välj **Alla tjänster** i det övre vänstra hörnet på Azure-portalen, sök efter och välj **Lagringskonton**.
2. Välj det lagringskonto som du skapade, välj **Blobbar**och välj sedan den behållare som du skapade.
3. Välj **Ladda upp**, navigera till och välj filen *custom-ui.html* och klicka sedan på Ladda **upp**.

    ![Ladda upp blob-sida i portalen med knappen Ladda upp och Filer markerade](./media/tutorial-customize-ui/upload-blob.png)

4. Kopiera URL:en för den fil som du laddade upp för att använda senare i självstudien.
5. Upprepa steg 3 och 4 för *filen style.css.*

## <a name="update-the-user-flow"></a>Uppdatera användarflödet

1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
2. Välj **Användarflöden (principer)** och välj sedan *B2C_1_signupsignin1* användarflödet.
3. Välj **Sidlayouter**och klicka sedan på **Ja** för Använd **anpassat sidinnehåll**under Enhetlig **registrering eller inloggningssida**.
4. I **Anpassad siduri**anger du URI för filen *custom-ui.html* som du spelade in tidigare.
5. Högst upp på sidan väljer du **Spara**.

## <a name="test-the-user-flow"></a>Testa användarflödet

1. I din Azure AD B2C-klient väljer du **Användarflöden** och väljer *B2C_1_signupsignin1* användarflöde.
2. Klicka på **Kör användarflöde**högst upp på sidan .
3. Klicka på knappen **Kör användarflöde.**

    ![Kör användarflödessida för användarflödet för registrering eller inloggning](./media/tutorial-customize-ui/run-user-flow.png)

    Du bör se en sida som liknar följande exempel med elementen centrerade baserat på CSS-filen som du skapade:

    ![Webbläsare som visar registrerings- eller inloggningssida med anpassade gränssnittselement](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa anpassningsfiler för användargränssnitt
> * Uppdatera användarflödet för att använda filerna
> * Testa det anpassade användargränssnittet

> [!div class="nextstepaction"]
> [Språkanpassning i Azure Active Directory B2C](user-flow-language-customization.md)
