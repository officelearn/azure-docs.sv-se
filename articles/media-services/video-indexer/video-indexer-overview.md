---
title: Vad är Video Indexer?
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över tjänsten Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: ae8634fbfdaa250cbabda6189c6c2eeef8e5e4f1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292497"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Azure Video Indexer är ett molnprogram som bygger på Azure-medieanalys, Azure Search, Cognitive Services (som Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). Det gör det möjligt att extrahera insikter från dina videor med Video Indexer-modeller som beskrivs nedan:
 
- **Automatisk språkidentifiering**: Identifierar automatiskt dominant talat språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och portugisiska (Brasilien). Om språket inte kan identifieras används engelska.
- **Ljudtranskription**: Konverterar tal till text på 10 språk och stöder tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska och portugisiska (Brasilien).
- **Textning**: Skapar textning i tre format: VTT, TTML och SRT.
- **Bearbetning av två kanaler**: Identifierar automatiskt, avgränsar avskrift och slår samman till en enda tidslinje.
- **Brusreducering**: Rensar telefoniljud eller inspelningar med störningar (baserat på Skype-filter).
- **Avskriftsanpassning (CRIS)**: Tränar och kör utökade anpassade tal till text-modeller för att skapa branschspecifika avskrifter.
- **Talaruppräkning**: Mappar och förstår vilken talare som sa vilka ord och när.
- **Talarstatistik**: Ger statistik om talförhållanden för talare.
- **Visuell textigenkänning (OCR)**: Extraherar text som visas i videon.
- **Extrahering av bildrutor**: Identifierar stabila nyckelbilder i en video.
- **Attitydanalys**: Identifierar positiva, negativa och neutrala attityder i tal och visuell text.
- **Moderering av visuellt innehåll**: Identifierar vuxna och/eller vågade visuella objekt.
- **Extrahering av nyckelord**: Extraherar nyckelord från tal och visuell text.
- **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
- **Extrahering av varumärken**: Extraherar varumärken från tal och visuell text.
- **Ansiktsigenkänning**: Identifierar och grupperar ansikten som visas i videon.
- **Extrahering av miniatyrer för ansikten (”bästa ansikte”)**: Identifierar automatiskt det bäst avbildade ansiktet i varje grupp med ansikten (baserat på kvalitet, storlek och position framifrån) och extraherar det som en bildtillgång.
- **Identifiering av kända personer**: Video Indexer identifierar automatiskt över en miljon kända personer – som världsledare, skådespelare, idrottsprofiler, forskare, affärs- och teknikledare från hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
- **Kontobaserad ansiktsidentifiering**: Video Indexer tränar en modell för ett visst konto. Sedan kan det identifiera ansikten i videor baserat på modellen som tränats specifikt för videor i det kontot.
- **Moderering av textinnehåll**: Identifierar stötande text i ljudavskriften.
- **Scenidentifiering**: Avgör när en scen ändras i videon.
- **Identifiering av svarta bildrutor**: Identifierar svarta bildrutor som visas i videon.
- **Ljudeffekter**: Identifierar ljudeffekter, till exempel handklappningar, tal och tystnad.
- **Ämnesinferens**: Skapar inferens av huvudämnen från avskrifter. [IPTC](https://iptc.org/standards/media-topics/)-taxonomi på första nivån ingår.
- **Känsloigenkänning**: Identifierar känslor baserat på tal och ljudtips. Känslan kan vara: glädje, sorg, ilska eller rädsla.
- **Artefakter**: Extraherar en omfattande uppsättning ”nästa detaljnivå”-artefakter för de olika modellerna.
- **Översättning**: Skapar översättningar av ljudavskriften till 54 olika språk.

När Video Indexer är klar med bearbetning och analys kan du granska, moderera, söka och publicera videoinsikterna.

Både innehållsansvariga och utvecklare kan ha nytta av Video Indexer-tjänsten. Innehållsansvariga kan använda Video Indexer-webbportalen för att använda tjänsten utan att behöva skriva en enda rad kod. Se [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md). Utvecklare kan dra nytta av API:er för att bearbeta innehållet i stor skala. Se [Använda Video Indexer REST API](video-indexer-use-apis.md). Tjänsten gör det även möjligt för kunderna att använda widgetar för att publicera videoströmmar och extrahera insikter i sina egna program. Se [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Du kan registrera dig för tjänsten med ditt befintliga AAD-, LinkedIn-, Facebook-, Google- eller MSA-konto. Mer information finns i [Komma igång](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenarier

Nedan visas några scenarier där Video Indexer kan vara användbart

- Sökning – Insikter som extraheras från videon kan användas för att förbättra sökupplevelsen i ett videobibliotek. Indexering av tal och ansikten kan till exempel göra det möjligt att söka efter det ställe i en video där en viss person säger en viss sak eller när två personer har en scen tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.

- Intäkter – Video Indexer kan öka värdet för videofilmer. Till exempel kan branscher som är beroende av annonsintäkter (som nyhetsmedier, sociala medier osv.) leverera mer relevant reklam genom att använda de extraherade insikterna som ytterligare signaler till annonsservern (att presentera reklam för sportskor är mer relevant under en fotbollsmatch än under en simtävling).

- Användarengagemang – Videoinsikter kan användas för att förbättra användarnas engagemang genom positionering av relevanta videor. Ett exempel är en utbildningsvideo där sfärer förklaras under de första 30 minuterna och pyramider under efterföljande 30 minuter. En student som läser om pyramiderna har mer nytta av videon om den positioneras med start från 30-minutersmarkeringen.

Mer information finns i den här [bloggen](https://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)
