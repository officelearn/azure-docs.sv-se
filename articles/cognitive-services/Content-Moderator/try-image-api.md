---
title: Måttlig bilder med API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd Image Moderering API i Azure Content Moderator för att initiera genomsökning och granska moderering arbetsflöden för innehållet.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 154a7cd4855429bfe3274974a0f4ae5c8be841f2
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265948"
---
# <a name="moderate-images-from-the-api-console"></a>Måttlig bilder från API-konsol

Använd den [Image Moderering API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) i Azure Content Moderator för att initiera genomsökning och granska moderering arbetsflöden för innehållet. Moderering av jobbet söker igenom innehållet med avseende på svordomar och jämför den med anpassade och delade svartlistor.

## <a name="use-the-api-console"></a>Använd API-konsol
Innan du kan testa API: et i online-konsolen, måste din prenumerationsnyckel. Den finns på den **inställningar** fliken den **Ocp-Apim-Subscription-Key** box. Mer information finns i [Översikt](overview.md).

1.  Gå till [Bildmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  Den **bild – utvärdera** bild moderering sida öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Försök bild – utvärdera val av region](images/test-drive-region.png)
  
  Den **bild – utvärdera** API-konsolen öppnas.

3. I den **Ocp-Apim-Subscription-Key** anger din prenumerationsnyckel.

  ![Försök bild – utvärdera konsolen prenumerationsnyckel](images/try-image-api-1.PNG)

4. I den **Begärandetext** rutan, Använd exemplet standardavbildningen eller ange en avbildning för att söka. Du kan skicka bilden som binary bit data, eller ange en offentligt tillgänglig URL för en bild. 

  Det här exemplet använder den sökväg som angavs i den **Begärandetext** och väljer sedan **skicka**. 

   ![Försök bild – utvärdera konsolen begärandetexten](images/try-image-api-2.PNG)

  Det här är bilden på URL: en:

  ![Försök bild – utvärdera Exempelbild för konsolen](images/sample-image.jpg) 

5. Välj **Skicka**.

6. API: et returnerar en sannolikhetspoäng för varje klassificering. Returnerar en bestämning av om avbildningen uppfyller villkor som (**SANT** eller **FALSKT**). 

  ![Försök bild – utvärdera konsolen sannolikhetspoäng för villkor har gjorts](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Ansiktsspårning

Du kan använda Image Moderering API för att hitta ansikten i en bild. Det här alternativet kan vara användbart när du har sekretessen och vill förhindra att ett visst ansikte publiceras på din plattform. 

1.  I den [Bildmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn under **bild**väljer **hitta ansikten**. 

  Den **bild - hitta ansikten** öppnas.

2.  För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Försök bild – hitta ansikten sidan val](images/test-drive-region.png)

  Den **bild - hitta ansikten** API-konsolen öppnas.

3. Ange en bild att skanna. Du kan skicka bilden som binary bit data, eller ange en offentligt tillgänglig URL till en bild. Det här exemplet innehåller länkar till en avbildning som används i en CNN-historia.

  ![Försök bild – hitta ansikten exempelbild](images/try-image-api-face-image.jpg)

  ![Försök bild – hitta ansikten exempelbegäran](images/try-image-api-face-request.png)

4. Välj **Skicka**. API: et i det här exemplet hittar två ansikten och returnerar deras koordinater i avbildningen.

   ![Försök bild – hitta ansikten exempel svar innehållsrutan](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Text identifiering via funktionen för OCR

Du kan använda funktionen för Content Moderator OCR för att identifiera text i bilder.

1. I den [Bildmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn under **bild**väljer **OCR**. 

  Den **bild - OCR** öppnas.

2. För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats. 

  ![Bild - OCR sidan val](images/test-drive-region.png)

  Den **bild - OCR** API-konsolen öppnas.

3. I den **Ocp-Apim-Subscription-Key** anger din prenumerationsnyckel.

4. I den **Begärandetext** använder exemplet standardavbildningen. Det här är samma avbildning som används i föregående avsnitt.

5. Välj **Skicka**. Extrahera texten visas i JSON:

  ![Bild - exemplet OCR svar innehållsrutan](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [bildmoderering .NET Snabbstart](image-moderation-quickstart-dotnet.md) att integrera med ditt program.
