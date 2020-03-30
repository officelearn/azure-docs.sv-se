---
title: Viktig information om Azure Media Services Video Indexer | Microsoft-dokument
description: För att hålla dig uppdaterad med den senaste utvecklingen innehåller den här artikeln de senaste uppdateringarna om Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 01/07/2020
ms.author: juliako
ms.openlocfilehash: f1387273f9736fea70682177d5d48dc2f141bbad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933862"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Viktig information om Azure Media Services Video Indexer

>Få ett meddelande om när du ska gå tillbaka till `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` den här sidan för uppdateringar genom att kopiera och klistra in den här webbadressen: i rss-flödesläsaren.

För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln dig information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

## <a name="january-2020"></a>Januari 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Anpassat språkstöd för ytterligare språk

Video Indexer stöder nu `ar-SY` anpassade `en-UK`språkmodeller för , och `en-AU` (endast API).
 
### <a name="delete-account-timeframe-action-update"></a>Ta bort åtgärdsuppdatering för kontotidsram

Ta bort kontoåtgärden tar nu bort kontot inom 90 dagar i stället för 48 timmar.
 
### <a name="new-video-indexer-github-repository"></a>Ny GitHub-databas för videoindexerare

En ny Video Indexer GitHub med olika projekt, komma igång guider och kodexempel är nu tillgänglig:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger uppdatering

Video Indexer enhetliga **autentiseringar** och **operationer** till en enda [Video Indexer OpenAPI Specifikation (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson). Develpers kan hitta API: er i [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>December 2019

### <a name="update-transcript-with-the-new-api"></a>Uppdatera avskrift med det nya API:et

Uppdatera ett visst avsnitt i avskriften med hjälp av [API:et för uppdaterings-videoindex.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Åtgärda kontokonfiguration från videoindexeringsportalen

Du kan nu uppdatera Media Services-anslutningskonfigurationen för att självhjälpa med frågor som: 

* felaktig Azure Media Services-resurs
* ändringar av lösenord
* Media Services-resurser flyttades mellan prenumerationer  

Om du vill åtgärda kontokonfigurationen navigerar du i videoindexeringsportalen till fliken Inställningar > konto (som ägare).

### <a name="configure-the-custom-vision-account"></a>Konfigurera det anpassade vision-kontot

Konfigurera kontot för anpassad sikt för betalda konton med hjälp av videoindexeringsportalen (tidigare stöddes detta bara av API). Det gör du genom att logga in på videoindexeringsportalen och välja Modellanpassning > animerade tecken > Konfigurera. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scener, bilder och nyckelrutor – nu i en insiktsruta

Scener, bilder och nyckelrutor slås nu samman till en insikt för enklare förbrukning och navigering. När du väljer önskad scen kan du se vilka bilder och nyckelrutor den består av. 

### <a name="notification-about-a-long-video-name"></a>Meddelande om ett långt videonamn

När ett videonamn är längre än 80 tecken visar Video Indexer ett beskrivande fel vid uppladdning.

### <a name="streaming-endpoint-is-disabled-notification"></a>Slutpunkt för direktuppspelning är inaktiverad avisering

När slutpunkten för direktuppspelning är inaktiverad visar Video Indexer ett beskrivande fel på spelarsidan.

### <a name="error-handling-improvement"></a>Förbättring av felhantering

Statuskod 409 returneras nu från [API:er](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) för video [index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) för om en video indexeras aktivt, för att förhindra att de aktuella återindexändringarna åsidosätts av misstag.

## <a name="november-2019"></a>November 2019
 
* Stöd för koreanska anpassade språkmodeller

    Videoindexerare stöder nu anpassade`ko-KR`språkmodeller på koreanska ( ) i både API och portal. 
* Nya språk som stöds för tal-till-text (STT)

    Video Indexer API:er stöder nu STT på arabiska Levantine (ar-SY), engelsk brittisk dialekt (en-GB) och engelsk australisk dialekt (en-AU).
    
    För videouppladdning ersatte vi zh-HANS till zh-CN, båda stöds men zh-CN rekommenderas och mer exakt.
    
## <a name="october-2019"></a>Oktober 2019
 
* Sök efter animerade figurer i galleriet

    När du indexerar animerade figurer kan du nu söka efter dem i kontots videokök. Mer information finns i [Tolkning av Animerade teckenigenkänning](animated-characters-recognition.md).

## <a name="september-2019"></a>September 2019
 
Flera framsteg tillkännagavs på IBC 2019:
 
* Animerad teckenigenkänning (offentlig förhandsgranskning)

    Möjlighet att identifiera gruppannons känner igen tecken i animerat innehåll, via integration med anpassad vision. Mer information finns i [Identifiering av animerade tecken](animated-characters-recognition.md).
* Identifiering av flera språk (offentlig förhandsversion)

    Identifiera segment på flera språk i ljudspåret och skapa en flerspråkig avskrift baserat på dem. Inledande support: engelska, spanska, tyska och franska. Mer information finns i [Identifiera och transkribera innehåll med flera språk](multi-language-identification-transcription.md)automatiskt .
* Namngiven enhetsutvinning för personer och plats

    Extraherar varumärken, platser och personer från tal- och visuell text via bearbetning av naturligt språk (NLP).
* Klassificering av redaktionell skotttyp

    Märkning av bilder med redaktionella typer som närbild, medium skott, två skott, inomhus, utomhus etc. Mer information finns i [Identifiering av redaktionell skotttyp](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Ämne inferencing förbättring - nu täcker nivå 2
    
    Ämnet inferencing modell stöder nu djupare granularitet i IPTC taxonomi. Läs fullständig information på [Azure Media Services ny AI-driven innovation](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Augusti 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribueras i storbritannien södra

Du kan nu skapa ett betalkonto för videoindexer i den brittiska södra regionen.

### <a name="new-editorial-shot-type-insights-available"></a>Nya redaktionella Shot Type insikter tillgängliga

Nya taggar läggs till videobilder ger redaktionella "skott typer" för att identifiera dem med gemensamma redaktionella fraser som används i arbetsflödet för att skapa innehåll som: extrem närbild, närbild, bred, medium, två skott, utomhus, inomhus, vänster ansikte och höger ansikte (Finns i JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Utvinning av nya personer och platser är tillgänglig

Video Indexer identifierar namngivna platser och personer via bearbetning av naturligt språk (NLP) från videons OCR och transkription. Video Indexer använder maskininlärningsalgoritm för att känna igen när specifika platser (till exempel Eiffeltornet) eller personer (till exempel John Doe) ropar ut i en video.

### <a name="keyframes-extraction-in-native-resolution"></a>Utvinning av nyckelrutor i inbyggd upplösning

Nyckelrutor som extraheras av Video Indexer finns i videons ursprungliga upplösning.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA för utbildning anpassade ansiktsmodeller från bilder

Träningsytorna från bilder som flyttas från förhandsgranskningsläge till GA (tillgängligt via API och i portalen).

> [!NOTE]
> Det finns ingen prispåverkan relaterad till övergången "Förhandsgranska till GA".

### <a name="hide-gallery-toggle-option"></a>Alternativet Dölj växlingsknapp för galleri

Användaren kan välja att dölja gallerifliken från portalen (på samma sätt som att dölja exempelfliken).
 
### <a name="maximum-url-size-increased"></a>Maximal URL-storlek har ökats

Stöd för URL-frågesträng på 4096 (i stället för 2048) för att indexera en video.
 
### <a name="support-for-multi-lingual-projects"></a>Stöd till flerspråkiga projekt

Projekt kan nu skapas baserat på videor indexerade på olika språk (endast API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Redigerare som widget

Video Indexer AI-redigeraren finns nu som en widget som ska bäddas in i kundprogram.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Uppdatera anpassad språkmodell från dold textningsfil från portalen

Kunder kan tillhandahålla filformat vtt, SRT och TTML som indata för språkmodeller på anpassningssidan i portalen.

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribueras till Japan East

Du kan nu skapa ett betalkonto för videoindexerare i regionen Östra Japan.

### <a name="create-and-repair-account-api-preview"></a>Api för skapa och reparera konto (förhandsversion)

Lade till ett nytt API som gör att du kan [uppdatera slutpunkten för Azure Media Service-anslutning eller -nyckel](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag).

### <a name="improve-error-handling-on-upload"></a>Förbättra felhantering vid uppladdning 

Ett beskrivande meddelande returneras vid felaktig konfiguration av det underliggande Azure Media Services-kontot.

### <a name="player-timeline-keyframes-preview"></a>Förhandsgranskning av nyckelrutor för spelartidslinjer 

Du kan nu se en förhandsgranskning av bilden för varje gång på spelarens tidslinje.

### <a name="editor-semi-select"></a>Redigerare halvval

Du kan nu se en förhandsgranskning av alla insikter som har valts som ett resultat av att välja en specifik tidsram för insikt i redigeraren.

## <a name="may-2019"></a>Maj 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Uppdatera anpassad språkmodell från filen med dold textning

[Skapa anpassade språkmodell-](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) och [uppdatera anpassade språkmodeller](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API:er stöder nu VTT-, SRT- och TTML-filformat som indata för språkmodeller.

När du anropar [API:et för uppdateringsvideoavskrift](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)läggs avskriften till automatiskt. Utbildningsmodellen som är associerad med videon uppdateras också automatiskt. Information om hur du anpassar och tränar dina språkmodeller finns i [Anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Ny nedladdning avskrift format - TXT och CSV

Förutom det format med dold textning som redan stöds (SRT, VTT och TTML) stöder Video Indexer nu att ladda ned avskriften i TXT- och CSV-format.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
