---
title: Måttlig bilder med Azure innehåll kontrollant | Microsoft Docs
description: Testkör avbildningen avbrottsmoderering i innehåll kontrollant API-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: fec54826c70ae10e56c68406f629c56639985295
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351660"
---
# <a name="moderate-images-from-the-api-console"></a>Måttlig bilder från konsolen API

Använd den [bild Avbrottsmoderering API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) Azure innehåll kontrollant att starta genomsökningen och granska avbrottsmoderering arbetsflöden för innehållet i operativsystemsavbildningen. Jobbet avbrottsmoderering söker igenom innehållet bort olämpligt material och jämför den med anpassade och delade blacklists.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan testa API i konsolen online måste din nyckel för prenumerationen. Den finns på den **inställningar** fliken den **Ocp-Apim-prenumeration-nyckeln** rutan. Mer information finns i [översikt](overview.md).

1.  Gå till [bild Avbrottsmoderering API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

  Den **bild – utvärdera** bild avbrottsmoderering sidan öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Försök bild – utvärdera val av region](images/test-drive-region.png)
  
  Den **bild – utvärdera** API-konsolen öppnas.

3. I den **Ocp-Apim-prenumeration-nyckeln** ange din prenumeration nyckel.

  ![Försök bild – utvärdera konsolen prenumeration nyckel](images/try-image-api-1.PNG)

4. I den **Begärandetext** rutan, Använd exemplet standardbilden eller ange en bild som ska genomsökas. Du kan skicka själva bilden som binary bit data, eller ange en offentligt tillgänglig URL för en bild. 

  Det här exemplet använder den angivna sökvägen i den **Begärandetext** och välj sedan **skicka**. 

   ![Försök bild – utvärdera konsolen begärandetexten](images/try-image-api-2.PNG)

  Detta är bilden på denna Webbadress:

  ![Försök bild – utvärdera konsolen exempelbild](images/sample-image.jpg) 

5. Välj **Skicka**.

6. API: N returnerar sannolikheten poängen för varje klassificering. Returnerar en bestämning av om avbildningen uppfyller villkoren (**SANT** eller **FALSKT**). 

  ![Försök bild – utvärdera konsolen sannolikhet poängsätta och villkor bestämning](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Ansiktsspårning

Du kan använda det bild Avbrottsmoderering API för att hitta ytor i en bild. Det här alternativet kan vara användbart när du har sekretessen och vill förhindra att en specifik yta från att anslås på din plattform. 

1.  I den [bild Avbrottsmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn under **bild**väljer **hitta står**. 

  Den **Image - hitta står** öppnas.

2.  För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Försök Image - hitta ytor val av region](images/test-drive-region.png)

  Den **Image - hitta står** API-konsolen öppnas.

3. Ange en bild som ska genomsökas. Du kan skicka själva bilden som binary bit data, eller ange en offentligt tillgänglig URL till en bild. Det här exemplet innehåller länkar till en bild som används i en artikel CNN.

  ![Försök Image - hitta ytor exempelbild](images/try-image-api-face-image.jpg)

  ![Försök Image - hitta ytor exempelbegäran](images/try-image-api-face-request.png)

4. Välj **Skicka**. I det här exemplet API: T hittar två ytor och returnerar deras koordinater i avbildningen.

   ![Försök Image - hitta ytor svar innehåll i rutan exempel](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Text identifiering via funktionen för OCR

Du kan använda innehåll kontrollant OCR möjlighet för att identifiera text i bilder.

1. I den [bild Avbrottsmoderering API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), i den vänstra menyn under **bild**väljer **OCR**. 

  Den **Image - OCR** öppnas.

2. För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats. 

  ![Bild - OCR val av region](images/test-drive-region.png)

  Den **Image - OCR** API-konsolen öppnas.

3. I den **Ocp-Apim-prenumeration-nyckeln** ange din prenumeration nyckel.

4. I den **Begärandetext** gör du exempel standardavbildningen. Det här är samma avbildning som används i föregående avsnitt.

5. Välj **Skicka**. Den extraherade texten visas i JSON:

  ![Bild - OCR svar innehåll i rutan exempel](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [bild avbrottsmoderering .NET quickstart](image-moderation-quickstart-dotnet.md) att integrera med ditt program.
