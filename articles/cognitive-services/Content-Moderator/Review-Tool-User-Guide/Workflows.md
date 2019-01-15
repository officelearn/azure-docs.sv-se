---
title: Definiera och Använd innehållsmoderering arbetsflöden – Content Moderator
titlesuffix: Azure Cognitive Services
description: 'Du kan använda Azure Content Moderator arbetsflödesdesigner och API: er för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina principer för innehåll.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 265d5a2bd6a256d287e6ae164e883ade783f960f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262412"
---
# <a name="define-test-and-use-workflows"></a>Definiera, testa och använda arbetsflöden

Du kan använda Azure Content Moderator arbetsflödesdesigner och API: er för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina principer för innehåll.

Arbetsflöden Anslut ”” Content Moderator-API: et via anslutningar. Du kan använda API: er om det finns en koppling för det API: et. I det här exemplet använder Content Moderator connector som ingår som standard.

## <a name="browse-to-the-workflows-section"></a>Bläddra till avsnittet om arbetsflöden

På den **inställningar** fliken **arbetsflöden**.

  ![Arbetsflöden som inställning](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>Starta ett nytt arbetsflöde

Välj **lägga till arbetsflödet**.

  ![Lägg till ett arbetsflöde](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>Tilldela ett namn och beskrivning

Namnge ditt arbetsflöde, ange en beskrivning och välj om arbetsflödet hanterar bilder eller text.

  ![Arbetsflödesnamn och beskrivning](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>Definiera utvärderingskriterierna (”villkor”)

I följande skärmbild ser du fälten och If-Then-Else valen som du behöver definiera för dina arbetsflöden. Välj en anslutning. Det här exemplet används **Content Moderator**. De tillgängliga alternativen för utdata ändras beroende på den koppling som du väljer.

  ![Definiera arbetsflödesvillkor](images/ocr-workflow-step-2-condition.PNG)

När du har valt kopplingen och dess utdata som du vill välja en operator och värde för villkoret.

## <a name="define-the-action-to-take"></a>Definiera åtgärd att vidta

Välj åtgärden som ska vidtas och som uppfyller villkoret. I följande exempel skapar en avbildning granskning, tilldelar en tagg `a`, och visar det villkoret som visas. Du kan också kombinera flera villkor för att få de resultat du vill ha. Du kan också lägga till en alternativ sökväg (annars).

  ![Definiera arbetsflödesåtgärden](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>Spara ditt arbetsflöde

Slutligen kan du spara arbetsflödet och Observera Arbetsflödesnamnet. Du behöver namnet som ska starta ett jobb för moderering med hjälp av API för granskning.

## <a name="test-the-workflow"></a>Testa arbetsflödet

Nu när du har definierat ett anpassat arbetsflöde kan du testa den innehåll.

Välj sedan motsvarande **köra arbetsflödet** knappen.

  ![Arbetsflödet test](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>Överför en fil

Spara den [exempelbild](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png) till din lokala enhet. Om du vill testa arbetsflödet, Välj **väljer fil(er)** och ladda upp avbildningen.

  ![Ladda upp avbildningsfilen](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>Spåra arbetsflödet

Spåra arbetsflödet när den körs.

  ![Spåra arbetsflödeskörning](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>Granska alla bilder som har flaggats för mänskliga moderering

Att se avbildningen granska, gå till den **bild** fliken **granska**.

  ![Granska bilder](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>Nästa steg 

För att anropa arbetsflödet från kod använder du anpassade arbetsflöden med den [ `Job` API konsolen Snabbstart](../try-review-api-job.md) och [Snabbstart för .NET SDK](../moderation-jobs-quickstart-dotnet.md).
