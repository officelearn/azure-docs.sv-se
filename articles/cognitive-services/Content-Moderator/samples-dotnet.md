---
title: Kodexempel – Content Moderator, .NET
description: Använda Content Moderator i dina program
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5465a2de0924037eb93b9de652e93e85f166737
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223159"
---
# <a name="net-sdk-samples"></a>.NET SDK-exempel

Följande lista innehåller länkar till kodexemplen som skapats med Azure Content Moderator SDK för .NET.

- **Hjälpbibliotek**: [Skapa en Content Moderator-klient för användning i andra exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Se [snabbstarten](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderering

- **Bildmoderering**: [Utvärdera en bild och se om den innehåller olämpligt innehåll, text eller ansikten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Se [snabbstarten](image-moderation-quickstart-dotnet.md).
- **Anpassade bilder**: [Moderera med anpassade bildlistor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Se [snabbstarten](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

- **Textmoderering**: [Sök igenom text efter svordomar och personligt identifierbar information (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Se [snabbstarten](text-moderation-quickstart-dotnet.md).
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
