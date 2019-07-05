---
title: Azure Media Services Video Indexer viktig information | Microsoft Docs
description: Om du vill hålla dig uppdaterad med den senaste utvecklingen, ger dig med de senaste uppdateringarna på Azure Media Services Video Indexer i den här artikeln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: f1c5f43316292f17547b84d970a0f03a1a2c366f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454027"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Viktig information om Azure Media Services Video Indexer

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer som distribuerats till östra Japan

Du kan nu skapa en Video Indexer betald konto i regionen Japan, östra.

### <a name="create-and-repair-account-api-preview"></a>Skapa och reparera konto API (förhandsversion)

Lagt till ett nytt API som gör det möjligt att [uppdatera Azure Media Service-anslutningsslutpunkten eller nyckeln](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Förbättra felhantering vid överföring 

Ett beskrivande meddelande returneras vid felaktig konfiguration av det underliggande Azure Media Services-kontot.

### <a name="player-timeline-keyframes-preview"></a>Player tidslinje nyckelrutor förhandsversion 

Du kan nu se en förhandsvisning för varje gång på spelarens tidslinje.

### <a name="editor-semi-select"></a>Redigeraren för halvval

Du kan nu se en förhandsgranskning av de insikter som är markerade på grund av att välja en viss insikt tidsram i redigeraren.

## <a name="may-2019"></a>Maj 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Uppdatera anpassad språkmodell från undertextfil

[Skapa anpassad språkmodell](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) och [uppdatera anpassade språkmodeller](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API: er stöder nu VTT SRT, och TTML filformat som indata för språkmodeller.

När du anropar den [uppdatering Video-avskrift API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript), avskriften läggs till automatiskt. Utbildningsmodell som är associerade med en video är uppdateras automatiskt. Information om hur du anpassar och träna din språkmodeller finns [anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nya download avskrift-format – TXT och CSV

Förutom dold textning formatet stöds redan (SRT VTT och TTML) stöder Video Indexer nu ladda ned avskriften i TXT- och CSV-format.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
