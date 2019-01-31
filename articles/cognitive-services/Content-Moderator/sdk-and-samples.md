---
title: SDK:er och exempel – Content Moderator, Python, Java, Node.js och .NET
titlesuffix: Azure Cognitive Services
description: Hämta SDK:er och exempel för Content Moderator
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: e05058be5b1ea8aa8faee3f3328a1b84935a3ac7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228584"
---
# <a name="content-moderator-sdks-and-samples"></a>SDK:er och exempel för Content Moderator

## <a name="sdks-for-python-java-nodejs-and-net"></a>SDK:er för Python, Java, Node.js och .NET

- [Content Moderator SDK för Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Content Moderator SDK för Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Content Moderator SDK för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Content Moderator SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK-exempel

Följande lista innehåller länkar till kodexemplen som skapats med Azure Content Moderator SDK för .NET.

- **Hjälpbibliotek**: [Skapa en Content Moderator-klient för användning i andra exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Se [snabbstarten](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderering 
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

### <a name="review"></a>Granska
- **Bildjobb**: [Starta ett modereringsjobb som söker av och skapar omdömen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Se [snabbstarten](moderation-jobs-quickstart-dotnet.md).
- **Bildomdömen**: [Skapa omdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Se [snabbstarten](moderation-reviews-quickstart-dotnet.md).
- **Videoomdömen**: [Skapa videoomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Se [snabbstarten](video-reviews-quickstart-dotnet.md).
- **Videoavskriftsomdömen**: [Skapa videoavskriftsomdömen för HITL (Human-In-The-Loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Se [snabbstarten](video-reviews-quickstart-dotnet.md)

Se alla .NET-exempel i [Content Moderator .NET-exemplen på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>REST API-exempel i C#

- [Bildmoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Textmoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Videomoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Bildomdömen](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Bildjobb](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Genomgångar av de här exemplen finns i [webbseminariet som är tillgängligt på begäran](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Självstudier
- [Katalogmoderering för eCommerce](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Se [självstudien](ecommerce-retail-catalog-moderation.md).
- [Innehållsmoderering på Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Se [självstudien](facebook-post-moderation.md).
- [Lösning för video- och avskriftsmoderering och omdömen](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Se [självstudien](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webbseminarier på begäran
- [Maskinassisterad innehållsmoderering i stor skala med Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
