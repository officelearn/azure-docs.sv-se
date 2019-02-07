---
title: Konfigurera Postman för Azure Media Services REST API-anrop
description: Lär dig hur du konfigurerar Postman för Media Services REST API-anrop.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: 24ee0083ec79265f0ac4dfc0a2e235bc72522831
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813899"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Konfigurera Postman för Media Services REST API-anrop

Den här artikeln visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services (AMS) REST API: er. Artikeln beskriver hur du importerar miljö och samling filerna till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST API: er. Miljö-filen innehåller variabler som används av samlingen.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppens namn och namnet på Media Services-konto. 
- Få information som behövs för att [åtkomst API: er](access-api-cli-how-to.md)
- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med plugin-programmet för REST eller **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Hämta Postman-filer

Klona en GitHub-lagringsplats som innehåller Postman-samlingen och miljöfilerna.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurera Postman

Det här avsnittet konfigurerar Postman.

### <a name="configure-the-environment"></a>Konfigurera miljön 

1. Öppna **Postman**.
2. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Hantera miljö](./media/develop-with-postman/postman-import-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
2. Bläddra till `Azure Media Service v3 Environment.postman_environment.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Miljön **Azure Media Service v3-miljö** läggs till.

    > [!Note]
    > Uppdatera åtkomstvariablerna med värden som du fick från avsnittet **Åtkomst till Media Services API** ovan.

7. Dubbelklicka på den valda filen och ange värden som du har fått genom att följa åtkomst till API: et.
8. Stäng dialogrutan.
9. Välj miljön **Azure Media Service v3-miljö** från listmenyn.

    ![Välj miljö](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurera samlingen

1. Klicka på **Importera** för att importera samlingsfilen.
1. Bläddra till `Media Services v3.postman_collection.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Välj filen **Media Services v3.postman_collection.json**.

    ![Importera en fil](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Hämta Azure AD-token 

Innan du börjar att manipulera AMS v3 resurser behöver du hämta och ange Azure AD-Token för autentisering av tjänstens huvudnamn.

1. I det vänstra fönstret i Postman, väljer du ”Steg 1: Hämta AAD-autentiseringstoken”.
2. Välj sedan Hämta Azure AD-token för autentisering för tjänstens huvudnamn.
3. Tryck på **Skicka**.

    Följande **POST**-åtgärd skickas.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Svaret kommer tillbaka med token och anger miljövariabeln AccessToken till token-värdet.  

    ![Hämta AAD-token](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="next-steps"></a>Nästa steg

[Stream filer med REST](stream-files-tutorial-with-rest.md).  
