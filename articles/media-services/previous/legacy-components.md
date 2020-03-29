---
title: Äldre Azure Media Services-komponenter | Microsoft-dokument
description: I det här avsnittet beskrivs äldre Azure Media Services-komponenter.
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
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921087"
---
# <a name="azure-media-services-legacy-components"></a>Äldre Azure Media Services-komponenter

Med tiden har det skett stadiga förbättringar och förbättringar av Media Service-komponenter. Som ett resultat har vissa äldre komponenter dragits tillbaka. Du hittar instruktionerna för hur du migrerar programmet från den äldre komponenten till en aktuell komponent i följande artiklar.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Pensionsplaner för äldre komponenter och migrationsvägledning

Vi meddelar att *mediaprocessorerna (Windows Azure Media Encoder)* och *Azure Media Encoder* (AME) har inaktiverats. Dessa processorer går i pension den 31 mars 2020.

* [Migrera från Windows Azure Media Encoder till Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrera från Azure Media Encoder till Media Encoder Standard](migrate-azure-media-encoder.md)

Vi tillkännager också pensionering av följande Media Analytics media processorer: 
 
|Namn på mediebearbetare|Pensioneringsdatum|Ytterligare information|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|Den 1 januari 2020|Den här medieprocessorn ersätts av [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Mer information finns i [Migrera från Azure Media Indexer 2 till Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Azure Media Indexer](media-services-index-content.md)|Den 1 mars 2023|Den här medieprocessorn ersätts av [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Mer information finns i [Migrera från Azure Media Indexer till Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)|
|[Rörelsedetektering](media-services-motion-detection.md)|Den 1 juni 2020|Inga ersättningsplaner just nu.|
|[Video summarisering](media-services-video-summarization.md)|Den 1 juni 2020|Inga ersättningsplaner just nu.|
|[Video optisk teckenigenkänning](media-services-video-optical-character-recognition.md)|Den 1 juni 2020|Den här medieprocessorn ersätts av [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Överväg också att använda [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Se [Jämföra Azure Media Services v3-förinställningar och videoindexerare](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Ansiktsigenkänning](media-services-face-and-emotion-detection.md)|Den 1 juni 2020|Den här medieprocessorn ersätts av [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Överväg också att använda [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Se [Jämföra Azure Media Services v3-förinställningar och videoindexerare](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Content Moderator](media-services-content-moderation.md)|Den 1 juni 2020|Den här medieprocessorn ersätts av [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Överväg också att använda [Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Se [Jämföra Azure Media Services v3-förinställningar och videoindexerare](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Nästa steg

[Migreringsvägledning för flyttning från Media Services v2 till v3](../latest/migrate-from-v2-to-v3.md)
