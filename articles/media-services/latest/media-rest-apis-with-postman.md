---
title: Konfigurera Postman för Azure Media Services v3 REST API-anrop
description: Den här artikeln visar hur du konfigurerar Postman så att den kan användas för att anropa AZURE Media Services (AMS) REST API:er.
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
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779645"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurera Postman för Media Services v3 REST API-anrop

Den här artikeln visar hur du konfigurerar **Postman** så att den kan användas för att anropa AZURE Media Services (AMS) REST API:er. Artikeln visar hur du importerar miljö- och samlingsfiler till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar AZURE Media Services (AMS) REST API:er. Miljöfilen innehåller variabler som används av samlingen.

Innan du börjar utveckla, granska [Utveckla med Media Services v3 API: er](media-services-apis-overview.md).

## <a name="prerequisites"></a>Krav

- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Se till att komma ihåg resursgruppsnamnet och mediatjänstkontonamnet. 
- Få information som behövs för att [komma åt API:er](access-api-cli-how-to.md)
- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med REST plugin eller **Telerik Fiddler**. 

> [!IMPORTANT]
> Granska [namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Hämta Postman-filer

Klona en GitHub-lagringsplats som innehåller Postman-samlingen och miljöfilerna.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurera Postman

### <a name="configure-the-environment"></a>Konfigurera miljön 

1. Öppna **postman-appen.**
2. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Hantera miljö](./media/develop-with-postman/postman-import-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
2. Bläddra till `Azure Media Service v3 Environment.postman_environment.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Miljön **Azure Media Service v3-miljö** läggs till.

    > [!Note]
    > Uppdatera åtkomstvariablerna med värden som du fick från avsnittet **Åtkomst till Media Services API** ovan.

7. Dubbelklicka på den valda filen och ange värden som du har fått genom att följa stegen i åtkomst till API.
8. Stäng dialogrutan.
9. Välj miljön **Azure Media Service v3-miljö** från listmenyn.

    ![Välj miljö](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurera samlingen

1. Klicka på **Importera** för att importera samlingsfilen.
1. Bläddra till `Media Services v3.postman_collection.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Välj filen **Media Services v3.postman_collection.json**.

    ![Importera en fil](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Hämta Azure AD-token 

Innan du börjar manipulera AMS v3-resurser måste du hämta och ange Azure AD-token för huvudansvarig autentisering av tjänst.

1. I det vänstra fönstret i Postman-appen väljer du "Steg 1: Get AAD Auth token".
2. Välj sedan Hämta Azure AD-token för autentisering för tjänstens huvudnamn.
3. Tryck på **Skicka**.

    Följande **POST**-åtgärd skickas.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Svaret kommer tillbaka med token och anger miljövariabeln AccessToken till token-värdet.  

    ![Hämta AAD-token](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Felsökning 

* Om ditt program misslyckas med "HTTP 504: Gateway Timeout" kontrollerar du att platsvariabeln inte uttryckligen har angetts till ett annat värde än den förväntade platsen för Media Services-kontot. 
* Om du får felmeddelandet "hittades inte" kontrollerar du också att platsegenskapen i Body JSON-meddelandet är inställd på den plats där Media Services-kontot finns. 

## <a name="see-also"></a>Se även

- [Ladda upp filer till ett Media Services-konto – REST](upload-files-rest-how-to.md)
- [Skapa filter med Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-baserat REST-API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Nästa steg

- [Strömma filer med REST](stream-files-tutorial-with-rest.md).  
- [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - REST](stream-files-tutorial-with-rest.md)
