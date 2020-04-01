---
title: Strömma videofiler med Azure Media Services - Node.js | Microsoft-dokument
description: Följ stegen i den här självstudien för att skapa ett nytt Azure Media Services-konto, koda en fil och strömma den till Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, strömma
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "69639397"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon - Node.js

Den här självstudien visar hur enkelt det är att koda och börja strömma videor på en mängd olika webbläsare och enheter med Hjälp av Azure Media Services. Ett indatainnehåll kan anges med HTTP-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage.

Exemplet i den här artikeln kodar innehåll som du gör tillgängligt via en HTTPS-URL. Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er.

I slutet av handledningen kommer du att kunna strömma en video.  

![Spela upp videon](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

- Installera [nod.js](https://nodejs.org/en/download/)
- [Skapa ett Media Services-konto](create-account-cli-how-to.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du behöver använda dem för att få åtkomst till API.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-databas som innehåller exemplet strömmande nod.js till datorn med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Exemplet finns i mappen [StreamFilesSample.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Öppna [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) i du hämtade projektet. Ersätt `endpoint config` värdena med autentiseringsuppgifter som du har fått [från åtkomst till API:er](access-api-cli-how-to.md).

Exemplet utför följande åtgärder:

1. Skapar en **transformering** (kontrollerar först om den angivna transformeringen finns). 
2. Skapar en **utdatatillgång** som används som kodning **av jobbutdata.**
3. Skapar **jobbets** indata som baseras på en HTTPS-URL.
4. Skickar **kodningsjobbet** med de indata och utdata som skapades tidigare.
5. Kontrollerar jobbets status.
6. Skapa en **positionerare för direktuppspelning**.
7. Skapar strömnings-URL:er.

## <a name="run-the-sample-app"></a>Kör exempelappen

1. Appen hämtar kodade filer. Skapa en mapp där du vill att utdatafilerna ska gå och uppdatera värdet för **variabeln outputFolder** i [index.js-filen.](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39)
1. Öppna **kommandotolken,** bläddra till exemplets katalog och kör följande kommandon.

    ```
    npm install 
    node index.js
    ```

När det är klart bör du se liknande utdata:

![Kör](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testa med Azure Media Player

I den här artikeln används Azure Media Player till att testa strömningen. 

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)navigera till .
2. I rutan **URL:** klistrar du in ett av de strömmande URL-värden som du fick när du körde programmet. 
 
     Du kan klistra in URL:en i formatet HLS, Dash eller Smooth så växlar Azure Media Player automatiskt till ett lämpligt strömningsprotokoll för uppspelning på din enhet.
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive mediatjänster och lagringskonton som du skapade för den här självstudien, tar du bort resursgruppen.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Se även

[Jobbfelkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Koncept för Media Services](concepts-overview.md)
