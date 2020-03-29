---
title: Begreppen Recensioner, Arbetsflöden och Jobb - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig mer om de centrala begreppen i granskningsverktyget. granskningar, arbetsflöden och jobb.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221154"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Granskningar, arbetsflöden och jobb för innehållsmoderering

Innehållsmoderator kombinerar datorstödd moderering med funktioner som används i loopen för att skapa en optimal modereringsprocess för verkliga scenarier. Det gör detta genom det molnbaserade [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). I den här guiden får du lära dig mer om de grundläggande begreppen i granskningsverktyget: recensioner, arbetsflöden och jobb.

## <a name="reviews"></a>Omdömen

I en granskning överförs innehållet till granskningsverktyget och visas under fliken **Granska.** Härifrån kan användarna ändra de använda taggarna och använda sina egna anpassade taggar efter behov. När en användare skickar en granskning skickas resultaten till en angiven motringningsslutpunkt och innehållet tas bort från webbplatsen.

![Webbplatsen för granskningsverktyg som är öppen i en webbläsare på fliken Granska](./Review-Tool-user-Guide/images/image-workflow-review.png)

Se [guiden Granska verktyg](./review-tool-user-guide/review-moderated-images.md) för att komma igång med att skapa recensioner, eller se [REST API-guiden](./try-review-api-review.md) för att lära dig hur du gör det programmässigt.

## <a name="workflows"></a>Arbetsflöden

Ett arbetsflöde är ett molnbaserat anpassat filter för innehåll. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och sedan vidta lämpliga åtgärder. Med Content Moderator-kopplingen kan ett arbetsflöde automatiskt tillämpa modereringstaggar och skapa recensioner med inskickat innehåll.

### <a name="view-workflows"></a>Visa arbetsflöden

Om du vill visa dina befintliga arbetsflöden går du till [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och väljer **Inställningar** > **Arbetsflöden**.

![Standardarbetsflöde](images/default-workflow-listed.PNG)

Arbetsflöden kan beskrivas fullständigt som JSON-strängar, vilket gör dem tillgängliga programmässigt. Om du väljer alternativet **Redigera** för arbetsflödet och sedan väljer fliken **JSON** visas ett JSON-uttryck som följande:

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

Läs [verktygsguiden](./review-tool-user-guide/workflows.md) för granskning för att komma igång med att skapa och använda arbetsflöden, eller se [REST API-guiden](./try-review-api-workflow.md) för att lära dig hur du gör det programmässigt.

## <a name="jobs"></a>Jobb

Ett modereringsjobb fungerar som ett slags omslag för funktionaliteten för innehållsmoderering, arbetsflöden och recensioner. Jobbet söker igenom ditt innehåll med hjälp av API:et för bildmoderering av innehållsmoderererering eller API för textmoderering och kontrollerar det sedan mot det angivna arbetsflödet. Baserat på arbetsflödesresultaten kan det hända att det skapar en recension av innehållet i [granskningsverktyget](./review-tool-user-guide/human-in-the-loop.md). Även om både granskningar och arbetsflöden kan skapas och konfigureras med sina respektive API:er, kan du med jobb-API:et få en detaljerad rapport över hela processen (som kan skickas till en angiven motringningsslutpunkt).

Se [REST API-guiden](./try-review-api-job.md) för att komma igång med jobb.

## <a name="next-steps"></a>Nästa steg

* Testa enheten för [JOBB-API-konsolen](try-review-api-job.md)och använd rest-API-kodexemplen. Om du är bekant med Visual Studio och C#, kolla också in [jobben .NET snabbstart](moderation-jobs-quickstart-dotnet.md). 
* Om du vill granska kan du komma igång med [API-konsolen Granska](try-review-api-review.md)och använda exempel på REST API-kod. Se sedan avsnittet recensioner i [snabbstarten .NET](dotnet-sdk-quickstart.md).
* För videorecensioner använder du [snabbstarten Videogranskning](video-reviews-quickstart-dotnet.md)och lär dig hur du [lägger till utskrifter i videogranskningen](video-transcript-reviews-quickstart-dotnet.md).
