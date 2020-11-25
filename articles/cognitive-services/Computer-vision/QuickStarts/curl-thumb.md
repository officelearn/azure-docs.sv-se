---
title: 'Snabb start: skapa en thumbnail-REST, sväng'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 03bd1b444c174c73f9444aece34bb56bb0898bde
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96006191"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Snabb start: skapa en miniatyr med hjälp av Visuellt innehåll REST API och svängen

I den här snabb starten skapar du en miniatyr bild från en avbildning med hjälp av Visuellt innehåll REST API. Du anger önskad höjd och bredd, vilket kan skilja sig från bild-breddförhållandet från indatabilden. Visuellt innehåll använder Smart beskärning för att intelligent identifiera intresse området och skapa beskärnings koordinater runt regionen.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/) 
* [cURL](https://curl.haxx.se/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
  * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
  * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="create-and-run-the-sample-command"></a>Skapa och köra exempelkommandot

Så här skapar du och kör exemplet:

1. Kopiera följande kommando till en textredigerare.
1. Gör följande ändringar i kommandot där det behövs:
    1. Ersätt värdet för `<subscriptionKey>` med din prenumerationsnyckel.
    1. Ersätt värdet för `<thumbnailFile>` med sökvägen och namnet på den fil där du kan spara miniatyren.
    1. Ersätt den första delen av URL: en för begäran ( `westcentralus` ) med texten i din egen slut punkts-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Du kan också ändra bild-URL i begärandetexten (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) till URL-adressen till en annan bild från vilken du kan skapa en miniatyrbild.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från text redigeraren i kommando tolkens fönster.
1. Kör programmet genom att trycka på RETUR.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar skriver miniatyrbilden till filen som anges i `<thumbnailFile>`. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel. Om begäran verkar lyckas men den skapade miniatyren inte är en giltig bildfil, kan det bero på att din prenumerations nyckel inte är giltig.

## <a name="next-steps"></a>Nästa steg

Utforska API för visuellt innehåll för att analysera en avbildning, identifiera kändisar och landmärken, skapa en miniatyr och extrahera skriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
