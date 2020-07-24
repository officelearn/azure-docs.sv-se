---
title: Azure Media Services äldre komponenter | Microsoft Docs
description: I det här avsnittet beskrivs Azure Media Services äldre komponenter.
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
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: d8d961ab58e900a6d619ec64297c783abdb7b6ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091699"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services äldre komponenter

Med tiden har du konstanta förbättringar och förbättringar av media service-komponenter. Därför har vissa äldre komponenter dragits tillbaka. Du hittar anvisningar om hur du migrerar ditt program från den äldre komponenten till en aktuell komponent i följande artiklar.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Indragnings planer för äldre komponenter och vägledning för migrering

Vi presenterar utfasningen av medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn). Dessa processorer dras tillbaka den 31 mars 2020.

* [Migrera från Windows Azure Media Encoder till Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrera från Azure Media Encoder till Media Encoder Standard](migrate-azure-media-encoder.md)

Vi meddelar också att följande Medieanalys medie processorer tas ur bruk: 
 
|Namn på mediebearbetare|Datum för indragning|Ytterligare information|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1 januari 2020|Medie processorn ersätts av [Azure Media Services video Indexer](../video-indexer/index.yml). Mer information finns i [Migrera från Azure Media Indexer 2 till Azure Media Services video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|1 mars 2023|Medie processorn ersätts av [Azure Media Services video Indexer](../video-indexer/index.yml). Mer information finns i [Migrera från Azure Media Indexer till Azure Media Services video Indexer](migrate-indexer-v1-v2.md)|
|[Rörelse identifiering](media-services-motion-detection.md)|1 juni 2020|Inga ersättnings planer för tillfället.|
|[Video Sammanfattning](media-services-video-summarization.md)|1 juni 2020|Inga ersättnings planer för tillfället.|
|[Optisk typsnitts igenkänning i video](media-services-video-optical-character-recognition.md)|1 juni 2020|Medie processorn ersätts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3-för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Ansiktsigenkänning](media-services-face-and-emotion-detection.md)|1 juni 2020|Medie processorn ersätts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3-för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|1 juni 2020|Medie processorn ersätts av [Azure Media Services video Indexer](../video-indexer/index.yml). Överväg också att använda [Azure Media Services v3 API](../latest/analyzing-video-audio-files-concept.md). <br/>Se [jämföra Azure Media Services v3-för inställningar och video Indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>Nästa steg

[Vägledning för migrering för att flytta från Media Services v2 till v3](../latest/migrate-from-v2-to-v3.md)
