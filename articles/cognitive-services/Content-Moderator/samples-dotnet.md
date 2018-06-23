---
title: Azure innehåll kontrollant-kodexempel | Microsoft Docs
description: Använda innehåll kontrollant i dina program
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352569"
---
# <a name="net-sdk-samples"></a>.NET SDK-exempel

Följande lista innehåller länkar till kodexempel som skapats med Azure innehåll kontrollant SDK för .NET.

- **Hjälpbibliotek**: [skapa en innehåll kontrollant klient för användning i andra exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Se [quickstart](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Avbrottsmoderering

- **Bild av avbrottsmoderering**: [utvärdera en bild för innehåll för vuxna och dyr, text och ytor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Se [quickstart](image-moderation-quickstart-dotnet.md).
- **Anpassade avbildningar**: [måttlig med anpassad avbildning listor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Se [quickstart](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Det finns en övre gräns för **5 bild visar** med varje lista till **inte överstiga 10 000 bilder**.
>

- **Text avbrottsmoderering**: [skärmen för svordomar och personligt identifierbar information (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Se [quickstart](text-moderation-quickstart-dotnet.md).
- **Anpassade villkor**: [måttlig med anpassade termen listor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Se [quickstart](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Det finns en övre gräns för **5 termen visar** med varje lista till **inte överstiga 10 000 villkoren**.
>

- **Video avbrottsmoderering**: [skanna en video för innehåll för vuxna och dyr och få resultat](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Se [quickstart](video-moderation-api.md).

## <a name="review"></a>Granska

- **Bild jobb**: [starta ett jobb med måtta som genomsöker och skapar granskningar](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Se [quickstart](moderation-jobs-quickstart-dotnet.md).
- **Bild granskningar**: [skapa granskning av hr-i-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Se [quickstart](moderation-reviews-quickstart-dotnet.md).
- **Video granskar**: [skapa video granskar hr-i-the-slingan](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Se [Snabbstart](video-reviews-quickstart-dotnet.md)
- **Video betyg granskar**: [skapa video betyg granskar hr-i-the-slingan](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) finns [Snabbstart](video-reviews-quickstart-dotnet.md)

Se alla .NET-exempel på den [innehåll kontrollant .NET-exemplen på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
