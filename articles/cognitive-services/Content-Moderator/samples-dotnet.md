---
title: Kodexempel – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Använd Content Moderator i .NET-program via SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 23ef7b515f5391a30f6d1fa9fce1dcb856643c69
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757269"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK-exempel

Följande lista innehåller länkar till kodexemplen som skapats med Azure Content Moderator SDK för .NET.

## <a name="moderation"></a>Moderering

- **Bildmoderering**: [Utvärdera en bild och se om den innehåller olämpligt innehåll, text eller ansikten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Se [snabbstarten](image-moderation-quickstart-dotnet.md).
- **Anpassade bilder**: [Moderera med anpassade bildlistor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Se [snabbstarten](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

- **Text redigering**: [skärm text för svordomar och personliga data](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Se [snabbstarten](text-moderation-quickstart-dotnet.md).
- **Anpassade termer**: [Moderera med anpassade termlistor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Se [snabbstarten](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

- **Videomoderering**: [Sök igenom en video efter olämpligt innehåll och visa resultatet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Se [snabbstarten](video-moderation-api.md).

## <a name="review"></a>Granska

- **Bildjobb**: [Starta ett modereringsjobb som söker av och skapar omdömen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Se [snabbstarten](moderation-jobs-quickstart-dotnet.md).
- **Bildomdömen**: [Skapa omdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Se [snabbstarten](moderation-reviews-quickstart-dotnet.md).
- **Videoomdömen**: [Skapa videoomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Se [snabbstarten](video-reviews-quickstart-dotnet.md).
- **Videoavskriftsomdömen**: [Skapa videoavskriftsomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Se [snabbstarten](video-reviews-quickstart-dotnet.md)

Se alla .NET-exempel i [Content Moderator .NET-exemplen på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
