---
title: 'Innehåll Avbrottsmoderering SDK: er och exempel för Azure innehåll kontrollanten | Microsoft Docs'
description: 'Hämta SDK: er och exempel för innehåll kontrollant'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351738"
---
# <a name="content-moderator-sdks-and-samples"></a>Innehåll kontrollant SDK: er och exempel

## <a name="sdks-for-python-java-nodejs-and-net"></a>SDK för Python, Java, Node.js och .NET

- [Innehåll kontrollant SDK för Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Innehåll kontrollant SDK för Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Innehåll kontrollant SDK för Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Innehåll kontrollant SDK för .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>.NET SDK-exempel

Följande lista innehåller länkar till kodexempel som skapats med Azure innehåll kontrollant SDK för .NET.

- **Hjälpbibliotek**: [skapa en innehåll kontrollant klient för användning i andra exempel](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Se [quickstart](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Avbrottsmoderering 
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

### <a name="review"></a>Granska
- **Bild jobb**: [starta ett jobb med måtta som genomsöker och skapar granskningar](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Se [quickstart](moderation-jobs-quickstart-dotnet.md).
- **Bild granskningar**: [skapa granskning av hr-i-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Se [quickstart](moderation-reviews-quickstart-dotnet.md).
- **Video granskar**: [skapa video granskar hr-i-the-slingan](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Se [Snabbstart](video-reviews-quickstart-dotnet.md)
- **Video betyg granskar**: [skapa video betyg granskar hr-i-the-slingan](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) finns [Snabbstart](video-reviews-quickstart-dotnet.md)

Se alla .NET-exempel på den [innehåll kontrollant .NET-exemplen på GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>REST API-exempel i C#

- [Bildmoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Textmoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Videomoderering](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Bildomdömen](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Bild-jobb](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Genomgångar av exemplen finns på den [på begäran webbseminariet](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Självstudier
- [e-handel katalogen avbrottsmoderering](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Se [kursen](ecommerce-retail-catalog-moderation.md).
- [Facebook innehåll avbrottsmoderering](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Se [kursen](facebook-post-moderation.md).
- [Video och betyg avbrottsmoderering och granska lösningen](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) finns [självstudiekursen](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webbseminarier på begäran
- [Dator-stödd innehåll gruppering i stor skala med innehåll kontrollant](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
