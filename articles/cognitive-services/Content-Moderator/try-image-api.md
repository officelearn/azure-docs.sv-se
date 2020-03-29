---
title: Moderera bilder med API-konsolen - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd API:et för bildmoderering i Azure Content Moderator för att initiera arbetsflöden för att söka efter moderering för bildinnehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448265"
---
# <a name="moderate-images-from-the-api-console"></a>Moderera bilder från API-konsolen

Använd [API:et för bildmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) i Azure Content Moderator för att initiera arbetsflöden för att söka efter moderering för bildinnehåll. Modereringsjobbet söker igenom ditt innehåll efter svordomar och jämför det med anpassade och delade blockeringslistor.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan provköra API:et i onlinekonsolen behöver du din prenumerationsnyckel. Detta finns på fliken **Inställningar** i rutan **Ocp-Apim-Subscription-Key.** Mer information finns i [Översikt](overview.md).

1. Gå till [API-referens för bildmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Sidan **Bild - Utvärdera** bildmoderering öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Prova bild - Utvärdera markering av sidregion](images/test-drive-region.png)
  
   **Api-konsolen Image - Utvärdera** öppnas.

3. Ange din prenumerationsnyckel i rutan **Ocp-Apim-Subscription-Key.**

   ![Prova image - utvärdera konsolprenumerationsnyckel](images/try-image-api-1.PNG)

4. I **rutan Begär** använder du standardprovbilden eller anger en bild som ska skannas. Du kan skicka själva bilden som binära bitdata eller ange en url som är allmänt tillgänglig för en bild. 

   I det här exemplet använder du sökvägen i rutan **Begäran** och väljer sedan **Skicka**. 

   ![Prova bild - Utvärdera konsolen Begäran kroppen](images/try-image-api-2.PNG)

   Det här är bilden på den webbadressen:

   ![Prova bild - Utvärdera exempelbild för konsolen](images/sample-image.jpg) 

5. Välj **Skicka**.

6. API:et returnerar en sannolikhetspoäng för varje klassificering. Det returnerar också en bestämning av om bilden uppfyller villkoren **(sant** eller **falskt).** 

   ![Prova bild - Utvärdera konsolsannolikhetspoäng och villkorsbestämning](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Ansiktsspårning

Du kan använda API:et för bildmoderering för att hitta ansikten i en avbildning. Det här alternativet kan vara användbart när du har integritetsfrågor och vill förhindra att ett visst ansikte publiceras på din plattform. 

1. Välj **Sök ansikten**under **Bild**moderering API-referens i [api-referensen för bildmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)i den vänstra menyn . 

   Sidan **Bild - Sök ansikten** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Val av bildsida – sök efter sidoresområde](images/test-drive-region.png)

   API-konsolen **Image - Find Faces** öppnas.

3. Ange en bild som ska skannas. Du kan skicka själva bilden som binära bitdata eller ange en url som är allmänt tillgänglig för en bild. Det här exemplet länkar till en bild som används i en CNN-händelse.

   ![Prova bild - Exempelbild för Sök ansikten](images/try-image-api-face-image.jpg)

   ![Prova bild - Hitta ansiktsexempel begäran](images/try-image-api-face-request.png)

4. Välj **Skicka**. I det här exemplet hittar API:et två ansikten och returnerar deras koordinater i avbildningen.

   ![Prova bild - rutan Exempel på svarsinnehåll för sök ansikten](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Textidentifiering via OCR-kapacitet

Du kan använda ocr-funktionen Innehållsmoderator för att identifiera text i bilder.

1. Välj **OCR**under **Bild**i [API-referensen för bildmoderering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)i den vänstra menyn . 

   Sidan **Bild - OCR** öppnas.

2. För **Open API-testkonsol**väljer du den region som bäst beskriver din plats. 

   ![Bild - VAL AV OCR-sidregion](images/test-drive-region.png)

   **API-konsolen Image - OCR** öppnas.

3. Ange din prenumerationsnyckel i rutan **Ocp-Apim-Subscription-Key.**

4. Använd standardprovbilden i rutan **Begäran.** Det här är samma bild som används i föregående avsnitt.

5. Välj **Skicka**. Den extraherade texten visas i JSON:

   ![Bild - OCR-exempel responsinnehållsruta](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller följ [snabbstarten .NET SDK](dotnet-sdk-quickstart.md) för att lägga till bildmoderation i programmet.
