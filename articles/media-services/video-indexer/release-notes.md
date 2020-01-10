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
ms.date: 01/07/2020
ms.author: juliako
ms.openlocfilehash: 88effd58e807d39e5915aa41425ecf2e8ca8e3cc
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832313"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Viktig information om Azure Media Services Video Indexer

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in URL: en: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` i din RSS-feeds läsare.

Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

* Den senaste versionen
* Kända problem
* Felkorrigeringar
* Inaktuell funktion

## <a name="december-2019"></a>December 2019

### <a name="update-transcript-with-the-new-api"></a>Uppdatera avskrift med det nya API: et

Uppdatera ett särskilt avsnitt i avskriften med hjälp av API: t [Update-video-index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Åtgärda konto konfigurationen från Video Indexer Portal

Nu kan du uppdatera Media Services anslutnings konfiguration för att själv hjälpa dig med problem som: 

* felaktig Azure Media Services resurs
* lösen ords ändringar
* Media Services resurser flyttades mellan prenumerationer  

Om du vill åtgärda konto konfigurationen går du till Video Indexer Portal navigera till Inställningar > fliken konto (som ägare).

### <a name="configure-the-custom-vision-account"></a>Konfigurera det anpassade vision-kontot

Konfigurera det anpassade vision kontot på betalda konton med hjälp av Video Indexer portal (tidigare stöddes bara av API). Det gör du genom att logga in på Video Indexer Portal, välja modell anpassning > animerade tecken > Konfigurera. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scener, dum par och nyckel bilder – nu i ett insikts fönster

Scener, dum par och nyckel bild rutor kombineras nu i en inblick för enklare användning och navigering. När du väljer önskad scen kan du se vilka bilder och nyckel rutor den består av. 

### <a name="notification-about-a-long-video-name"></a>Meddelande om ett långt video namn

När ett video namn är längre än 80 tecken visar Video Indexer ett beskrivande fel vid uppladdning.

### <a name="streaming-endpoint-is-disabled-notification"></a>Slut punkt för direkt uppspelning är inaktiverat meddelande

När slut punkten för direkt uppspelning är inaktive rad visas Video Indexer ett beskrivande fel på sidan spelare.

### <a name="error-handling-improvement"></a>Förbättring av fel hantering

Status kod 409 kommer nu att returneras från [index video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video? https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) och [uppdatera video index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) -API: er om en video är aktivt indexerad för att förhindra att de aktuella omindexet ändras av misstag.

## <a name="november-2019"></a>November 2019
 
* Stöd för koreanska anpassade språk modeller

    Video Indexer stöder nu anpassade språk modeller i koreanska (`ko-KR`) i både API: n och portalen. 
* Nya språk som stöds för tal till text (STT)

    Video Indexer API: er har nu stöd för STT i arabiska Levantine (ar-SY), engelska Storbritannien dialekt (en-GB) och engelska australisk dialekt (en-AU).
    
    För video uppladdning ersätter vi zh-HANS till zh-CN, men båda stöds men zh-CN rekommenderas och mer exakt.
    
## <a name="october-2019"></a>Oktober 2019
 
* Sök efter animerade tecken i galleriet

    När du indexerar animerade tecken kan du nu söka efter dem i kontots video text. Mer information finns i [igenkänning av animerade tecken](animated-characters-recognition.md).

## <a name="september-2019"></a>September 2019
 
Flera framsteg lanseras i IBC 2019:
 
* Animerad typsnitts igenkänning (offentlig för hands version)

    Möjlighet att identifiera grup AD identifiera tecken i animerat innehåll via integrering med anpassad vision. Mer information finns i [animerat teckensnitts identifiering](animated-characters-recognition.md).
* Identifiering på flera språk (offentlig för hands version)

    Identifiera segment i flera språk i ljud spåret och skapa en flerspråkig avskrifts enhet baserat på dem. Första support: engelska, spanska, tyska och franska. Mer information finns i [identifiera och automatisk identifiering av innehåll på flera språk](multi-language-identification-transcription.md).
* Extraktion av namngiven enhet för personer och plats

    Extraherar varumärken, platser och personer från tal och visuell text via naturlig språk bearbetning (NLP).
* Klassificering av redigerings typ

    Märkning av dum par med redaktionella typer som att stänga, medels Tor, två bilder, inomhus, Utomhus osv. Mer information finns i [identifiering av redaktionella typer av tagningar](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Ämne inferencing-förbättring – nu täcker nivå 2
    
    Avsnittet inferencing-modellen stöder nu djupare granularitet i IPTC-taxonomin. Läs fullständig information på [Azure Media Services nya AI-baserade innovationer](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Augusti 2019
 
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

## <a name="june-2019"></a>Juni 2019

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

När du anropar [uppdaterings video avskrifts-API: t](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)läggs avskriften till automatiskt. Den utbildnings modell som är associerad med videon uppdateras också automatiskt. Information om hur du anpassar och tränar dina språk modeller finns i [Anpassa en språk modell med video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nya format för att ladda ned avskrifter – TXT och CSV

Förutom formatet för dold textning som redan stöds (SRT, VTT och TTML) stöder nu Video Indexer att ladda ned avskriften i TXT-och CSV-format.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
