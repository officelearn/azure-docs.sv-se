---
title: Definiera och använder arbetsflöden i Azure Content kontrollant | Microsoft Docs
description: Lär dig hur du skapar anpassade arbetsflöden som baserats på principer för innehåll.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351597"
---
# <a name="define-test-and-use-workflows"></a>Definiera, testa och använder arbetsflöden

Du kan använda Azure innehåll kontrollant workflow designer och API: er för att definiera anpassade arbetsflöden och tröskelvärden baserat på principer för innehåll.

Arbetsflöden Anslut ”” innehåll kontrollant-API: et med kopplingar. Du kan använda andra API: er om en koppling för detta API är tillgänglig. I det här exemplet använder innehåll kontrollant connector som ingår som standard.

## <a name="browse-to-the-workflows-section"></a>Bläddra till avsnittet om arbetsflöden

På den **inställningar** väljer **arbetsflöden**.

  ![Ange arbetsflöden](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Starta ett nytt arbetsflöde

Välj **Lägg till arbetsflöde**.

  ![Lägga till ett arbetsflöde](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Tilldela ett namn och beskrivning

Namnge ditt arbetsflöde, ange en beskrivning och välj om arbetsflödet hanterar bilder eller text.

  ![Arbetsflödesnamn och beskrivning](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Definiera utvärderingskriterierna (”villkor”)

I följande skärmbild visas fälten och If-Then-Else valen som du behöver definiera dina arbetsflöden. Välj en koppling. Det här exemplet används **innehåll kontrollant**. Beroende på anslutningen som du kan ändra alternativen för utdata.

  ![Definiera arbetsflödesvillkor](images/ocr-workflow-step-2-condition.PNG)

När du har valt kopplingen och dess utdata som du vill välja en operator och värdet för villkoret.

## <a name="define-the-action-to-take"></a>Definiera vad som ska göras

Välj åtgärden som ska vidtas och villkoret för att uppfylla. I följande exempel skapar en avbildning granskning, tilldelar en tagg `a`, och en beskrivning för det villkor som visas. Du kan också kombinera flera villkor för att få önskat resultat. Du kan också lägga till en alternativ sökväg (annars).

  ![Definiera arbetsflödesåtgärd](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Spara ditt arbetsflöde

Slutligen spara arbetsflödet och notera namnet på arbetsflödet. Du måste namnet för att starta ett jobb med måtta med granska API.

## <a name="test-the-workflow"></a>Testa arbetsflödet

Nu när du har definierat ett anpassat arbetsflöde kan du testa den innehåll.

Välj den motsvarande **köra arbetsflödet** knappen.

  ![Arbetsflödet test](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Överför en fil

Spara den [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) till din lokala enhet. Om du vill testa arbetsflödet, Välj **Välj fil(er)** och laddar upp avbildningen.

  ![Överför avbildningsfil](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Spåra arbetsflödet

Spåra arbetsflödet som körs.

  ![Spåra arbetsflödeskörning](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Granska eventuella avbildningar som flaggats för mänsklig avbrottsmoderering

Att se bilden granska, gå till den **bild** fliken **granska**.

  ![Granska bilder](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Nästa steg 

Om du vill anropa arbetsflödet från kod använder anpassade arbetsflöden med den [ `Job` API konsolen quickstart](../try-review-api-job.md) och [.NET SDK quickstart](../moderation-jobs-quickstart-dotnet.md).
