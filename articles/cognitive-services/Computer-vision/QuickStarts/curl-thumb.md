---
title: 'Snabbstart: Skapa en miniatyrbild – REST, cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 350bc95e08aa994e4cb70db6bf1f08d53bbec5a3
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177289"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Snabbstart: Skapa en miniatyr med hjälp av Visuellt innehåll REST API och svängen

I den här snabb starten skapar du en miniatyr bild från en avbildning med hjälp av Visuellt innehåll REST API. Du anger önskad höjd och bredd, vilket kan skilja sig från bild-breddförhållandet från indatabilden. Visuellt innehåll använder Smart beskärning för att intelligent identifiera intresse området och skapa beskärnings koordinater runt regionen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha [cURL](https://curl.haxx.se/windows).
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnads fri utvärderings nyckel från [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på visuellt innehåll och hämta din nyckel.

## <a name="get-thumbnail-request"></a>Begäran om att hämta miniatyrbild

Med metoden [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Hämta miniatyrbild) kan du skapa en miniatyrbild av en bild.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ersätt `<File>` med sökvägen och filnamnet för att spara miniatyrbilden.
1. Ändra URL:en för begäran (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`) till den plats där du hämtade dina prenumerationsnycklar om det behövs.
1. Du kan också ändra bilden (`{\"url\":\"...`) som ska analyseras om du vill.
1. Öppna ett kommandofönster på en dator där cURL är installerat.
1. Klistra in koden i fönstret och kör kommandot.

>[!NOTE]
>Du måste använda samma plats i REST-anropet som du använde för att hämta prenumerationsnycklarna. Om du till exempel hämtade prenumerationsnycklarna från regionen westus (USA, västra), ersätter du ”westcentralus” i URL:en nedan med ”westus”.

## <a name="create-and-run-the-sample-command"></a>Skapa och köra exempelkommandot

Så här skapar du och kör exemplet:

1. Kopiera följande kommando till en textredigerare.
1. Gör följande ändringar i kommandot där det behövs:
    1. Ersätt värdet för `<subscriptionKey>` med din prenumerationsnyckel.
    1. Ersätt värdet för `<thumbnailFile>` med sökvägen och namnet på den fil där du kan spara miniatyren.
    1. Ersätt den första delen av URL: en för begäran (`westcentralus`) med texten i din egen slut punkts-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Du kan också ändra bild-URL i begärandetexten (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) till URL-adressen till en annan bild från vilken du kan skapa en miniatyrbild.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster och kör sedan kommandot.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar skriver miniatyrbilden till filen som anges i `<thumbnailFile>`. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel. Om begäran verkar lyckas men den skapade miniatyren inte är en giltig bildfil, kan det bero på att din prenumerations nyckel inte är giltig.

## <a name="next-steps"></a>Nästa steg

Utforska API för visuellt innehåll för att analysera en avbildning, identifiera kändisar och landmärken, skapa en miniatyr och extrahera skriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
