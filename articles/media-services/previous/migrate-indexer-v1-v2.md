---
title: Migrera från Indexerare v1 och v2 till Azure Media Services Video Indexer | Microsoft-dokument
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Indexer v1 och v2 till Azure Media Services Video Indexer.
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
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513243"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrera från Media Indexer och Media Indexer 2 till Video Indexer

Azure Media Indexer-medieprocessorn och [Azure Media Indexer 2 Preview-medieprocessorer](media-services-process-content-with-indexer2.md) dras tillbaka. [Azure Media Indexer](media-services-index-content.md) För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter dessa äldre medieprocessorer.

Azure Media Services Video Indexer bygger på Azure Media Analytics, Azure Cognitive Search, Cognitive Services (till exempel Ansikts-API, Microsoft Translator, API FÖR datorseende och anpassad taltjänst). Programmet gör det möjligt att extrahera insikter från dina videor med video- och ljudmodeller för Video Indexer. För att se vilka scenarier Video Indexer kan användas i, vilka funktioner den erbjuder och hur du kommer igång, se [Video Indexer video- och ljudmodeller](../video-indexer/video-indexer-overview.md). 

Du kan extrahera insikter från dina video- och ljudfiler med hjälp av [Azure Media Services v3-analysatorförinställningar](../latest/analyzing-video-audio-files-concept.md) eller direkt med hjälp av [API:erna för videoindexerare](https://api-portal.videoindexer.ai/). För närvarande finns det en överlappning mellan funktioner som erbjuds av videoindexer API:er och Api:erna för Media Services v3.

> [!NOTE]
> Om du vill förstå när du vill använda videoindexerare jämfört med mediatjänstanalysatorförinställningar kan du läsa [jämförelsedokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

I den här artikeln beskrivs stegen för att migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Migreringsalternativ 

|Om du behöver  |sedan |
|---|---|
|en lösning som ger en utskrift mellan tal och text för alla mediefilformat i ett filformat med dold textning: VTT, SRT eller TTML<br/>samt ytterligare ljudinsikter som: nyckelord, ämnesinferens, akustiska händelser, högtalare diarization, enheter extraktion och översättning| uppdatera dina program för att använda Azure Video Indexer-funktionerna via VIDEO Indexer v2 REST API eller Azure Media Services v3 Audio Analyzer-förinställningen.|
|tal-till-text-funktioner| använda Cognitive Services Speech API direkt.|  

## <a name="getting-started-with-video-indexer"></a>Komma igång med Video Indexer

I följande avsnitt visas relevanta länkar: [Hur kommer jag igång med Video Indexer?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Komma igång med Api:er för Media Services v3

Med Azure Media Services v3 API kan du extrahera insikter från dina video- och ljudfiler via [Azure Media Services v3-analysatorförinställningarna](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** gör att du kan extrahera flera ljudinsikter från en ljud- eller videofil. Utdata innehåller en VTT eller TTML-fil för ljudavskriften och en JSON-fil (med alla ytterligare ljudinsikter). Ljudinsikterna inkluderar nyckelord, högtalarindexering och talsentimentanalys. AudioAnalyzerPreset stöder också språkidentifiering för specifika språk. Detaljerad information finns i [Transformeringar](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Komma igång

Så här kommer du igång:

* [Självstudier](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset-exempel: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) eller [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Komma igång med Cognitive Services Speech Services

[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) tillhandahåller en tal-till-text-tjänst som transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Du kan använda tal-till-text för att [anpassa din egen akustiska modell, språkmodell eller uttalsmodell](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Mer information finns i [Cognitive Services tal-till-text](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> Tal-till-text-tjänsten tar inte videofilformat och tar bara [vissa ljudformat](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Mer information om text-till-tal-tjänsten och hur du kommer igång finns i [Vad är tal-till-text?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Kända skillnader från inaktuella tjänster 

Du kommer att upptäcka att Video Indexer, Azure Media Services v3 AudioAnalyzerPreset och Cognitive Services Speech Services-tjänster är mer tillförlitliga och ger bättre utskrift än de pensionerade Azure Media Indexer 1- och Azure Media Indexer 2-processorerna.  

Några kända skillnader inkluderar: 

* Cognitive Services Speech Services stöder inte utvinning av nyckelord. Video Indexer och Media Services v3 AudioAnalyzerPreset erbjuder dock båda en mer robust uppsättning sökord i JSON-filformat. 

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/#encoding)


