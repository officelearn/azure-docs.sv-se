---
title: Vad är Video Indexer?
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över tjänsten Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: b0d0df5d113b1d75602022085b8bb17133f07333
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422977"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Azure Video Indexer är ett molnprogram som bygger på Azure-medieanalys, Azure Search, Cognitive Services (som Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). Det gör att du kan extrahera insikter från dina videor med hjälp av Video Indexer video-och ljud modeller som beskrivs nedan:
  
## <a name="video-insights"></a>Video insikter

- **Ansikts igenkänning**: Identifierar och grupper ansikten som visas i videon.
- **Kändis-ID**: Video Indexer identifierar automatiskt över 1 000 000 kändisar – till exempel världs ledare, aktörer, actresses, tävlande, forskare, företag och teknik ledare över hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
- **Konto-baserad ansikts identifiering**: Video Indexer tågen en modell för ett bestämt konto. Den identifierar sedan ansikten i videon baserat på den tränade modellen. Mer information finns i [Anpassa en person modell från video Indexer webbplats](customize-person-model-with-website.md) och [Anpassa en person modell med video Indexer API](customize-person-model-with-api.md).
- **Bild extrahering för ansikten** ("bästa FACET"): Identifierar automatiskt den bästa infångade ytan i varje grupp av ansikten (baserat på kvalitet, storlek och front position) och extraherar den som en bild till gång.
- **Visuell text igenkänning** (OCR): Extraherar text som visas visuellt i videon.
- **Moderator för visuellt innehåll**: Identifierar vuxen och/eller vågat visuella objekt.
- **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
- **Scen segmentering**: anger när en scens ändringar i videon baseras på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande dum par, som är semantiskt relaterade. 
- **Bild identifiering**: anger när en bild ändras i video utifrån visuella tips. En bild är en serie med ramar som tas från samma rörelse-och bildkamera. Mer information finns i [scener, bilder och nyckel bild rutor](scenes-shots-keyframes.md).
- **Identifiering av svart ram**: Identifierar svarta bild rutor som visas i videon.
- **Extrahering av nyckel rutor**: Identifierar stabila nyckel rutor i en video.
- **Rullande krediter**: identifiera början och slutet av de rullande eftertexterna i slutet av TV-program och filmer.

## <a name="audio-insights"></a>Ljud insikter

- **Automatisk språk identifiering**: Identifierar automatiskt det dominerande talade språket. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska. Om språket inte kan identifieras med förtroende förutsätter Video Indexer att det talade språket är engelska. Mer information finns i [språk identifierings modell](language-identification-model.md).
- **Ljud avskrift**: Konverterar tal till text på 12 språk och tillåter tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, portugisiska (Brasilien), hindi och koreanska.
- **Dold textning**: Skapar dold textning i tre format: VTT, TTML, SRT.
- **Två kanal bearbetning**: Automatisk identifierar, separera avskrift och sammanslagningar till en enda tids linje.
- **Brus minskning**: Tar bort ljud-eller brus inspelnings telefoner (baserat på Skype-filter).
- **Avskrifts anpassning** (CRI: er): Anpassad tal till text modeller för tågen för att skapa branschspecifika avskrifter. Mer information finns i [Anpassa en språk modell från video Indexer webbplats](customize-language-model-with-website.md) och [Anpassa en språk modell med video Indexer-API: er](customize-language-model-with-api.md).
- **Uppräkning av högtalare**: Mappar och förstår vilken talare som har ekrar som ord och när.
- **Högtalar statistik**: Innehåller statistik för tal i tal för talare.
- **Text innehålls moderator**: Identifierar explicit text i ljud avskriften.
- **Ljud effekter**: Identifierar ljud effekter som hand claps, tal och tystnad.
- **Känslo-identifiering**: Identifierar känslor baserat på tal (vad som sägs) och röst ton (hur det sägs).  Känslan kan vara: glädje, sorg, ilska eller rädsla.
- **Översättning**: Skapar översättningar av ljud avskriften till 54 olika språk.

## <a name="audio-and-video-insights-multi-channels"></a>Ljud-och video insikter (flera kanaler)

När indexering av en kanal del resultat för dessa modeller är tillgänglig

- **Extrahering av nyckelord**: Extraherar nyckelord från tal och visuell text.
- **Extrahering av varumärken**: Extraherar varumärken från tal och visuell text.
- **Avsnitts härledning**: Gör det enklare att utföra huvud ämnena i avskrifter. IPTC-taxonomi på den första nivån ingår.
- **Artefakter**: Extraherar omfattande uppsättning "nästa nivå av information"-artefakter för varje modell.
- **Sentiment-analys**: Identifierar positiva, negativa och neutrala sentiment från tal och visuell text.
 
När Video Indexer är klar med bearbetning och analys kan du granska, moderera, söka och publicera videoinsikterna.

Både innehållsansvariga och utvecklare kan ha nytta av Video Indexer-tjänsten. Innehållsansvariga kan använda Video Indexer-webbportalen för att använda tjänsten utan att behöva skriva en enda rad kod. Se [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md). Utvecklare kan dra nytta av API:er för att bearbeta innehållet i stor skala. Se [Använda Video Indexer REST API](video-indexer-use-apis.md). Tjänsten gör det även möjligt för kunderna att använda widgetar för att publicera videoströmmar och extrahera insikter i sina egna program. Se [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Du kan registrera dig för tjänsten med ditt befintliga AAD-, LinkedIn-, Facebook-, Google- eller MSA-konto. Mer information finns i [Komma igång](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenarier

Nedan visas några scenarier där Video Indexer kan vara användbart

- Sökning – Insikter som extraheras från videon kan användas för att förbättra sökupplevelsen i ett videobibliotek. Indexering av tal och ansikten kan till exempel göra det möjligt att söka efter det ställe i en video där en viss person säger en viss sak eller när två personer har en scen tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.
- Skapa innehåll – insikter som har extraherats från videor och som gör det effektivt att skapa innehåll som till exempel släp vagnar, innehåll i sociala medier, nyhets klipp osv. från befintligt innehåll i organisationens Arkiv 
- Intäkter – Video Indexer kan öka värdet för videofilmer. Till exempel kan branscher som är beroende av annonsintäkter (som nyhetsmedier, sociala medier osv.) leverera mer relevant reklam genom att använda de extraherade insikterna som ytterligare signaler till annonsservern (att presentera reklam för sportskor är mer relevant under en fotbollsmatch än under en simtävling).
- Användarengagemang – Videoinsikter kan användas för att förbättra användarnas engagemang genom positionering av relevanta videor. Ett exempel är en utbildningsvideo där sfärer förklaras under de första 30 minuterna och pyramider under efterföljande 30 minuter. En student som läser om pyramiderna har mer nytta av videon om den positioneras med start från 30-minutersmarkeringen.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)
