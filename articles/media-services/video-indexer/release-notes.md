---
title: Viktig information om Azure Media Services Video Indexer | Microsoft Docs
description: Den här artikeln innehåller de senaste uppdateringarna på Azure Media Services Video Indexer för att hålla dig uppdaterad med den senaste utvecklingen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.openlocfilehash: a7d178972a8c8b413e3c6336c403348f1eb78abb
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232609"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Viktig information om Azure Media Services Video Indexer

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion

## <a name="august-2019"></a>2019 augusti
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribuerat i Storbritannien, södra

Nu kan du skapa ett Video Indexer betalt konto i regionen Storbritannien, södra.

### <a name="new-editorial-shot-type-insights-available"></a>Nya insikter för text insikter tillgängliga

Nya taggar som läggs till i videoklipp innehåller redaktionella "tagnings typer" för att identifiera dem med vanliga redaktionella fraser som används i arbets flödet för innehålls skapande, till exempel: Extreme närbild, närbild, wide, medium, två bilder, Utomhus, inomhus, vänster och höger (tillgänglig i JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extrahering av enheter för nya personer och platser tillgängliga

Video Indexer identifierar namngivna platser och personer via naturlig språk bearbetning (NLP) från videons OCR och avskrift. Video Indexer använder Machine Learning-algoritmen för att identifiera när vissa platser (till exempel Eiffel Tower) eller personer (till exempel John berg) anropas i en video.

### <a name="keyframes-extraction-in-native-resolution"></a>Extrahering av nyckel rutor i intern upplösning

Nyckel rutor som extraheras av Video Indexer är tillgängliga i den ursprungliga upplösningen av videon.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA för att träna anpassade ansikts modeller från bilder

Träna från bilder som flyttats från förhands gransknings läge till GA (tillgängligt via API och i portalen).

> [!NOTE]
> Ingen prissättnings påverkan är relaterad till över gången "för hands version till GA".

### <a name="hide-gallery-toggle-option"></a>Göm alternativ för Galleri växling

Användaren kan välja att dölja fliken Galleri från portalen (liknar fliken exempel).
 
### <a name="maximum-url-size-increased"></a>Maximal URL-storlek ökad

Stöd för URL-frågesträngen 4096 (i stället för 2048) vid indexering av en video.
 
### <a name="support-for-multi-lingual-projects"></a>Stöd för flerspråkiga projekt

Nu kan du skapa projekt baserat på videor som indexerats på olika språk (endast API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Redigeraren som en widget

Video Indexer AI-redigeraren är nu tillgänglig som en widget som är inbäddad i kund program.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Uppdatera anpassad språk modell från fil med dold textning från portalen

Kunder kan tillhandahålla VTT-, SRT-och TTML-filformat som indata för språk modeller på sidan anpassning i portalen.

## <a name="june-2019"></a>2019 juni

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribuerad till Japan, öst

Nu kan du skapa ett Video Indexer betalt konto i regionen Japan, östra.

### <a name="create-and-repair-account-api-preview"></a>Skapa och reparera konto-API (för hands version)

Lade till ett nytt API som gör att du kan [Uppdatera Azure Media service-anslutningens slut punkt eller nyckel](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Förbättra fel hanteringen vid uppladdning 

Ett beskrivande meddelande returneras om det underliggande Azure Media Services kontot felkonfigureras.

### <a name="player-timeline-keyframes-preview"></a>För hands versionen av nyckel rutor för Player-tidslinje 

Nu kan du se en förhands granskning av bilden för varje gång på spelarens tids linje.

### <a name="editor-semi-select"></a>Redigeraren, halv markering

Nu kan du se en förhands granskning av alla insikter som valts till följd av att du väljer en viss Insight-tidsram i redigeraren.

## <a name="may-2019"></a>Maj 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Uppdatera anpassad språk modell från fil med dold textning

[Skapa anpassad språk modell](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) och [uppdatera anpassade språk](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) modells-API: er stöder nu VTT-, SRT-och ttml-filformat som indata för språk modeller.

När du anropar uppdaterings video avskrifts- [API: t](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)läggs avskriften till automatiskt. Den utbildnings modell som är associerad med videon uppdateras också automatiskt. Information om hur du anpassar och tränar dina språk modeller finns i [Anpassa en språk modell med video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nya format för att ladda ned avskrifter – TXT och CSV

Förutom formatet för dold textning som redan stöds (SRT, VTT och TTML) stöder nu Video Indexer att ladda ned avskriften i TXT-och CSV-format.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
