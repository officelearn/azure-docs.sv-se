---
title: Vad är Video Indexer?
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över tjänsten Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: c1c81ba0f168759ab9cc05f3441e07ff03275e84
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417388"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Azure Video Indexer är ett molnprogram som bygger på Azure-medieanalys, Azure Search, Cognitive Services (som Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). På så sätt kan du extrahera insikter från dina videor med Video Indexer-video och ljud-modeller som beskrivs nedan:
  
## <a name="video-insights"></a>Videoinsikter

- **Ansiktsspårning**: Identifierar och grupperar ansikten visas i videon.
- **Kändisar identifiering**: Video Indexer identifierar automatiskt över 1 miljon kändisar – till exempel world ledare, aktörer och actresses, tävlande, forskare, företag och tekniska ledare i hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
- **Baserade ansiktsidentifiering**: Video Indexer träna en modell för ett visst konto. Sedan kan det identifiera ansikten i videor baserat på modellen som tränats specifikt för videor i det kontot.
- **Miniatyr extrahering för ansikten** (”bästa ansikte”): Automatiskt identifierar bäst avbildade ansikte i varje grupp med ansikten (baserat på kvalitet, storlek och främre placering) och extrahera den som en bild tillgång.
- **Visual textigenkänning** (OCR): Extraherar text som visas visuellt i videon.
- **Visual innehållsmoderering**: Identifierar vuxet/vågat eller visuella objekt.
- **ID för etiketten**: Identifierar visuella objekt och åtgärder som visas.
- **Som identifiering av**: Anger när en scen ändras i videon.
- **Svart ramtyp**: Identifierar svart ramar som visas i videon.
- **Extrahering av bildrutan**: Identifierar stabil nyckelrutor i en video.

## <a name="audio-insights"></a>Ljud insikter

- **Automatisk språkidentifiering**: Identifierar automatiskt dominerande talat språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och portugisiska (Brasilien). Om språket inte kan identifieras används engelska.
- **Ljudutskrift**: Konverterar tal till text i 12 språk och tillåter tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, portugisiska (Brasilien), Hindi och koreanska.
- **Textning**: Skapar textning i tre format: VTT, TTML, SRT.
- **Två channel bearbetning**: Automatiskt identifierar, avgränsa avskrift och slår ihop till en enda tidslinje.
- **Buller minskning**: Rensar upp telefoni ljud- eller störningar inspelningar (baserat på Skype filter).
- **Avskriften anpassning** (CRIS): Träna och kör utökade anpassad tal till text modeller för att skapa branschspecifika avskrifter.
- **Talare uppräkning**: Mappar och förstår vilka talare ekrar vilka ord och när.
- **Talare statistik**: Innehåller statistik för talare tal förhållanden.
- **Textbaserade innehållsmoderering**: Identifierar explicit text i ljudavskrifter.
- **Ljud effekterna**: Identifierar ljud effekter som hand applåder, tal och åsidosatt inaktivitet.
- **Känsloigenkänning**: Identifierar känslor baserat på tal- och ljud tips. Känslan kan vara: glädje, sorg, ilska eller rädsla.
- **Översättning**: Skapar översättningar av ljudavskrifter till 54 olika språk.

## <a name="audio-and-video-insights-multi-channels"></a>Ljud- och insights (med flera kanaler)

När indexering av en kanal delresultat för dessa modeller kommer att vara tillgänglig

- **Extrahering av nyckelord**: Extraherar nyckelord från tal- och visual text.
- **Anpassar extrahering**: Extraherar varumärken från tal- och visual text.
- **Avsnittet inferens**: Gör inferens av viktigaste avsnitten från avskrifter. 1: a-nivå IPTC taxonomi ingår.
- **Artefakter**: Extraherar omfattande uppsättning ”nästa nivå med information om” artefakter för de olika modellerna.
- **Attitydanalys**: Identifierar positivt, negativt och neutral sentiment från tal- och visual text.
 
  
 
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
