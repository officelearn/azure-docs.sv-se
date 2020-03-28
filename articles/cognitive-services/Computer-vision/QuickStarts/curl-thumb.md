---
title: 'Snabbstart: Generera en miniatyrbild - REST, cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten genererar du en miniatyrbild från en bild med hjälp av API för visuellt innehåll och cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cb9dadc107c6907f1ccb28a876270e577f10395
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977309"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Snabbstart: Generera en miniatyrbild med REST API FÖR visuellt innehåll och cURL

I den här snabbstarten skapar du en miniatyrbild från en bild med REST-API:et för visuellt innehåll. Du anger önskad höjd och bredd, som kan skilja sig i aspektranson från indatabilden. Computer Vision använder smart beskärning för att på ett intelligent sätt identifiera intresseområdet och generera beskärningskoordinater runt den regionen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) konto innan du börjar.

## <a name="prerequisites"></a>Krav

- Du måste ha [cURL](https://curl.haxx.se/windows).
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan få en kostnadsfri testversionsnyckel från [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Du kan också följa instruktionerna i [Skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Datorseende och få din nyckel.

## <a name="get-thumbnail-request"></a>Begäran om att hämta miniatyrbild

Med [metoden Hämta miniatyr](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb)kan du skapa en miniatyrbild av en bild.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ersätt `<File>` med sökvägen och filnamnet för att spara miniatyrbilden.
1. Ändra URL:en för begäran (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.1`) till den plats där du hämtade dina prenumerationsnycklar, om det behövs.
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
    1. Ersätt den första delen av`westcentralus`url:en för begäran ( ) med texten i din egen slutpunkts-URL.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Du kan också ändra bild-URL i begärandetexten (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) till URL-adressen till en annan bild från vilken du kan skapa en miniatyrbild.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster och kör sedan kommandot.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar skriver miniatyrbilden till filen som anges i `<thumbnailFile>`. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel. Om begäran verkar lyckas men den skapade miniatyren inte är en giltig bildfil kan det bero på att prenumerationsnyckeln inte är giltig.

## <a name="next-steps"></a>Nästa steg

Utforska API:et för visuellt innehåll för att analysera en bild, upptäcka kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
