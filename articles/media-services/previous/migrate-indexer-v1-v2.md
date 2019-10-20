---
title: Migrera från indexerare v1 och v2 till Azure Media Services Video Indexer | Microsoft Docs
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Indexer v1 och v2 för att Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2b8553c4c4b27e70eb5e4aeff447da9526c4ed1e
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598297"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrera från Media Indexer och Media Indexer 2 till Video Indexer

[Azure Media Indexer](media-services-index-content.md) medie processorn kommer att dras tillbaka den 1 oktober 2020. Processorerna för för [hands versionen av Azure Media Indexer 2](media-services-process-content-with-indexer2.md) kommer att dras tillbaka den 1 januari 2020.  [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter dessa äldre medie processorer.

Azure Media Services Video Indexer bygger på Azure-medieanalys, Azure Search, Cognitive Services (till exempel Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). Programmet gör det möjligt att extrahera insikter från dina videor med video- och ljudmodeller för Video Indexer. Om du vill se vilka scenarier Video Indexer kan användas i, vilka funktioner det erbjuder och hur du kommer igång, se [video Indexer video-och ljud modeller](../video-indexer/video-indexer-overview.md). 

Du kan extrahera insikter från dina video-och ljudfiler med hjälp av [Azure Media Services v3 Analyzer-för inställningar](../latest/analyzing-video-audio-files-concept.md) eller direkt med hjälp av [video Indexer API: er](https://api-portal.videoindexer.ai/). För närvarande finns det en överlappning mellan funktioner som erbjuds av Video Indexer-API: er och Media Services v3-API: er.

> [!NOTE]
> Om du vill veta när du vill använda Video Indexer jämfört med Media Services Analyzer-för inställningar, kan du läsa [jämförelse dokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

I den här artikeln beskrivs stegen för att migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migrations alternativ 

|Om du behöver  |Dra |
|---|---|
|en lösning som ger en tal-till-text-avskrift för alla media fil format i fil format med fil namns tillägg: VTT, SRT eller TTML<br/>samt ytterligare ljud insikter som: nyckelord, ämne inferencing, akustiska händelser, talare diarization, extrahering och översättning av enheter| uppdatera dina program så att de använder Azure Video Indexer-funktionerna via Video Indexer v2-REST API eller Azure Media Services v3-inställningarna för ljud analys.|
|tal till text-funktioner| Använd Cognitive Services Speech API direkt.|  

## <a name="getting-started-with-video-indexer"></a>Komma igång med Video Indexer

Följande avsnitt pekar på relevanta länkar: [Hur kan jag komma igång med video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Komma igång med Media Services v3-API: er

Med Azure Media Services v3 API kan du extrahera insikter från dina video-och ljudfiler via [Azure Media Services v3 Analyzer-för hands inställningarna](../latest/analyzing-video-audio-files-concept.md). 

Med **AudioAnalyzerPreset** kan du extrahera flera ljud insikter från en ljud-eller video fil. Utdata innehåller en VTT-eller TTML-fil för ljud avskriften och en JSON-fil (med alla ytterligare ljud insikter). Ljud insikter innehåller nyckelord, talare indexering och analys av tal sentiment. AudioAnalyzerPreset har även stöd för språk identifiering för specifika språk. Detaljerad information finns i [transformeringar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Kom i gång

För att komma igång, se:

* [Självstudie](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Komma igång med Cognitive Services Speech Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) tillhandahåller en tal-till-text-tjänst som beskrivar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Du kan använda tal-till-text för att [Anpassa din egen akustiska modell, språk modell eller uttals modell](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Mer information finns i [Cognitive Services tal-till-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Tal-till-text-tjänsten tar inte med video fil format och tar bara med [vissa ljud format](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Mer information om tjänsten för text till tal och hur du kommer igång finns i [Vad är tal-till-text?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Kända skillnader jämfört med inaktuella tjänster 

Du kommer att se att Video Indexer, Azure Media Services v3-AudioAnalyzerPreset och Cognitive Services Speech Services-tjänster är mer pålitliga och ger bättre kvalitet på utdata än de pensionerade Azure Media Indexer 1-och Azure Media Indexer 2-processorerna.  

Några kända skillnader är: 

* Filer med dold textning i SAMI-format stöds inte längre. Detta är ett äldre bild text format som inte längre används ofta. Den ersätts av TTML, WebVTT och SRT.  
* AIB-filer (Audio Indexing BLOB) stöds inte längre. Den här funktionen var unik för Indexer 1-tekniken och erbjuds inte längre.  
* Cognitive Services Speech Services stöder inte nyckelords extrahering. Men Video Indexer och Media Services v3-AudioAnalyzerPreset erbjuder båda en robust uppsättning nyckelord i JSON-filformat. 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Sidan prissättning](https://azure.microsoft.com/pricing/details/media-services/#encoding)


