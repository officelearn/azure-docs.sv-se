---
title: Kodexempel – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur du använder Azure Cognitive Services Content Moderator i dina .NET-program via SDK: n.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912930"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK-exempel

Följande lista innehåller länkar till kodexemplen som skapats med Azure Content Moderator SDK för .NET.

## <a name="moderation"></a>Ändring

- **Bildmoderering** : [Utvärdera en bild och se om den innehåller olämpligt innehåll, text eller ansikten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Se snabb starten för [.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Anpassade bilder** : [Moderera med anpassade bildlistor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Se snabb starten för[.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder** .
>

- **Text redigering** : [skärm text för svordomar och personliga data](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Se snabb starten för[.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Anpassade termer** : [Moderera med anpassade termlistor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Se snabb starten för [.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer** .
>

- **Videomoderering** : [Sök igenom en video efter olämpligt innehåll och visa resultatet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Se [snabb start](video-moderation-api.md).

## <a name="review"></a>Genomgång

- **Bildjobb** : [Starta ett modereringsjobb som söker av och skapar omdömen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Se [snabb start](moderation-jobs-quickstart-dotnet.md).
- **Bildomdömen** : [Skapa omdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Se [snabb start](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Videoomdömen** : [Skapa videoomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Se [snabbstarten](video-reviews-quickstart-dotnet.md).
- **Videoavskriftsomdömen** : [Skapa videoavskriftsomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Se [snabbstarten](video-reviews-quickstart-dotnet.md)

Se alla .NET-exempel i [Content Moderator .NET-exemplen på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).