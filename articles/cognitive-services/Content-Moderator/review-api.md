---
title: Granskningar, arbetsflöden, och jobb begrepp - Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig mer om granskningar och arbetsflöden som jobb
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607269"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>Innehållsmoderering granskningar och arbetsflöden som jobb

Content Moderator kombinerar datorstödd moderering med human-i-the-loop funktioner för att skapa en optimal moderering process för verkliga scenarier. Detta sker via den molnbaserade [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com). I den här guiden lär du dig grundkoncepten i granskningsverktyget: granskningar, arbetsflöden och jobb.

## <a name="reviews"></a>Omdömen

I en granskning innehåll överförs till granskningsverktyget och visas under den **granska** fliken. Härifrån kan användare ändra tillämpade taggar och lägga till sina egna anpassade taggar efter behov. När en användare skickar en granskning, resultaten skickas till en slutpunkt för angivna motringning och innehållet tas bort från platsen.

![Granska verktyget webbplatsen öppnas i en webbläsare, på fliken Granska](./Review-Tool-user-Guide/images/image-workflow-review.png)

Finns i den [granska verktyget guide](./review-tool-user-guide/review-moderated-images.md) att komma igång och skapa granskningar eller finns i den [REST API-guiden](./try-review-api-review.md) information om hur du gör programmässigt.

## <a name="workflows"></a>Arbetsflöden

Ett arbetsflöde är en molnbaserad anpassade filter för innehåll. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Med Content Moderator-anslutningen kan ett arbetsflöde automatiskt lägga till taggar för moderering och skapa granskningar med innehållet som skickas.

### <a name="view-workflows"></a>Visa arbetsflöden

Du kan visa dina befintliga arbetsflöden genom att gå till den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) och välj **inställningar** > **arbetsflöden**.

![Standardarbetsflöde](images/default-workflow-listed.PNG)

Arbetsflöden kan beskrivas helt som JSON-strängar, vilket gör dem tillgängliga via programmering. Om du väljer den **redigera** för arbetsflödet och välj sedan den **JSON** fliken ser du en JSON-uttryck som liknar följande:

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

Finns i den [granska verktyget guide](./review-tool-user-guide/workflows.md) att börja skapa och använda arbetsflöden eller finns i den [REST API-guiden](./try-review-api-workflow.md) information om hur du gör programmässigt.

## <a name="jobs"></a>Jobb

Ett jobb för moderering fungerar som en sorts Omslutning för funktionerna i innehållsmoderering, arbetsflöden och granskningar. Jobbet söker igenom ditt innehåll med bildmoderering Content Moderator API eller API för moderering av text och kontrollerar sedan mot avsedda arbetsflödet. Baserat på resultaten arbetsflöde, den kan eller kan inte skapa en granskning för innehållet i den [granskningsverktyget](./review-tool-user-guide/human-in-the-loop.md). Även om både granskningar och arbetsflöden kan skapas och konfigureras med deras respektive API: er, kan du få en detaljerad rapport över hela processen (som kan skickas till en slutpunkt för angivna återanrop) jobbet API.

Se den [REST API-guiden](./try-review-api-job.md) att komma igång med jobb.

## <a name="next-steps"></a>Nästa steg

* Testkör den [jobbet API-konsol](try-review-api-job.md), och använda REST API-kodexempel. Om du är bekant med Visual Studio och C# kan också Kolla den [Snabbstart för jobb .NET](moderation-jobs-quickstart-dotnet.md). 
* För granskning, Kom igång med den [granska API-konsol](try-review-api-review.md), och använda REST API-kodexempel. Då se den [Snabbstart för granskningar .NET](moderation-reviews-quickstart-dotnet.md).
* Video granskningar använder den [Video granska Snabbstart](video-reviews-quickstart-dotnet.md), och lär dig hur du [lägga till betyg i video granskningen](video-transcript-reviews-quickstart-dotnet.md).
