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
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0b6430a31f31f85f01f67994f6b6fa0f30af9575
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879930"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Snabbstart: Generera en miniatyrbild med hjälp av REST API:et och cURL i Visuellt innehåll

I den här snabbstarten har generera du en miniatyr från en avbildning med hjälp av REST-API för visuellt innehåll. Du kan ange önskad höjd och bredd, som kan skilja sig i aspekt ransonen från inmatad bild. Visuellt innehåll använder smart beskärning att identifierar ett intresseområde och generera beskärning koordinater runt den regionen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha [cURL](https://curl.haxx.se/windows).
- Du måste ha en prenumerationsnyckel för Visuellt innehåll. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Begäran om att hämta miniatyrbild

Med metoden [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Hämta miniatyrbild) kan du skapa en miniatyrbild av en bild.

För att köra exemplet följer du dessa steg:

1. Kopiera följande kod till ett redigeringsprogram.
1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Ersätt `<File>` med sökvägen och filnamnet för att spara miniatyrbilden.
1. Ändra URL:en för begäran (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) till den plats där du hämtade dina prenumerationsnycklar om det behövs.
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
    1. Ersätt fråge-URL (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) med slutpunktsadressen för metoden [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) från Azure-regionen där du fått dina prenumerationsnycklar om det behövs.
    1. Du kan också ändra bild-URL i begärandetexten (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) till URL-adressen till en annan bild från vilken du kan skapa en miniatyrbild.
1. Öppna ett kommandotolksfönster.
1. Klistra in kommandot från textredigeraren i kommandotolkens fönster och kör sedan kommandot.

    ```console
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar skriver miniatyrbilden till filen som anges i `<thumbnailFile>`. Om begäran misslyckas innehåller svaret en felkod och ett meddelande som beskriver vad som gick fel.

## <a name="next-steps"></a>Nästa steg

Utforska API för visuellt innehåll till att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera utskrivna och handskriven text. Du kan experimentera med API för visuellt innehåll i [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Utforska API för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
