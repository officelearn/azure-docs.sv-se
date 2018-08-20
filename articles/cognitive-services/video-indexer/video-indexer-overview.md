---
title: Översikt över Azure Video Indexer | Microsoft Docs
description: Det här avsnittet ger en översikt över tjänsten Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397899"
---
# <a name="what-is-video-indexer-preview"></a>Vad är Video Indexer? (förhandsversion)

Video Indexer är ett molnprogram som skapats med Azure-medieanalys, kognitiva tjänster (som Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service) och Azure Search. Det gör det möjligt att extrahera följande insikter från dina videor med AI-teknik:

- **Automatisk språkidentifiering**: Video Indexer kan automatiskt identifiera språket i videon. Automatisk språkidentifiering har för närvarande stöd för engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska och ryska. Om språket inte kan identifieras används engelska.
- **Ljudtranskription**: Video Indexer har funktioner för tal till text, vilket gör att kunderna kan få transkriptioner av tal. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), portugisiska (Brasilien), japanska och ryska (och fler kommer framöver). 
- **Ansiktsspårnings och -identifiering**: teknik för ansiktsigenkänning möjliggör identifiering av ansikten i en video. De identifierade ansiktena matchas mot en kändisdatabas för att utvärdera vilka kändisar som är med i videon. Kunderna kan även etikettera ansikten som inte matchar en kändis. Video Indexer skapar en ansiktsmodell som baseras på de här etiketterna och kan identifiera de ansiktena i videofilmer som skickas in längre fram.
- **Talarindexering**: Video Indexer kan mappa och förstå vilken talare som sa vilka ord och när.
- **Visuell textigenkänning**: med den här tekniken extraherar Video Indexer text som visas i videofilmer.  
- **Röstaktivitetsidentifiering**: identifieringen gör det möjligt för Video Indexer att avgränsa bakgrundsbrus och röstaktivitet. 
- **Scenidentifiering**: Video Indexer kan utföra visuell analys på videon för att fastställa när en scen ändras i en video.
- **Extrahering av bildrutor**: Video Indexer identifierar automatiskt nyckelbilder i en video. 
- **Känsloanalys**: Video Indexer utför känsloanalys på den text som extraherats med tal-till-text och optisk teckenigenkänning, och tillhandahåller den informationen som positiv, negativ eller neutral känsla tillsammans med tidskoder.
- **Översättning**: Video Indexer kan översätta ljudtranskriptionen från ett språk till ett annat. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), portugisiska (Brasilien), japanska och ryska. När översättningen är klar kan användaren få undertextning på andra språk i videospelaren.
- **Visuell innehållsmoderering**: den här tekniken möjliggör identifiering av barnförbjudet och/eller stötande/olämpligt material i videon och kan användas för innehållsfiltrering. 
- **Extrahering av nyckelord**: Video Indexer extraherar nyckelord baserat på taltranskriptionen och text som identifierats av den visuella textidentifieraren.
- **Etiketter**: Video Indexer etiketterar visuella objekt som katt, hund, bord, eller bil, och till handlingar som stå, springa, och flyga.
- **Varumärken**: Video Indexer extraherar varumärken baserat på taltranskriptionen och textigenkänningen som gjorts av den visuella textidentifieraren.

När Video Indexer är klar med bearbetning och analys kan du granska, moderera, söka och publicera videoinsikterna.

Både innehållsansvariga och utvecklare kan ha nytta av Video Indexer-tjänsten. Innehållsansvariga kan använda Video Indexer-webbportalen för att använda tjänsten utan att behöva skriva en enda rad kod. Se [Komma igång med Video Indexer-portalen](video-indexer-get-started.md). Utvecklare kan dra nytta av API:er för att bearbeta innehållet i stor skala. Se [Använda Video Indexer REST API](video-indexer-use-apis.md). Tjänsten gör det även möjligt för kunderna att använda widgetar för att publicera videoströmmar och extrahera insikter i sina egna program. Se [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Du kan registrera dig för tjänsten med ditt befintliga AAD-, LinkedIn-, Facebook-, Google- eller MSA-konto. Mer information finns i [Komma igång](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenarier

Nedan visas några scenarier där Video Indexer kan vara användbart

- Sökning – Insikter som extraheras från videon kan användas för att förbättra sökupplevelsen i ett videobibliotek. Indexering av tal och ansikten kan till exempel göra det möjligt att söka efter det ställe i en video där en viss person säger en viss sak eller när två personer har en scen tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.

- Intäkter – Video Indexer kan öka värdet för videofilmer. Till exempel kan branscher som är beroende av annonsintäkter (som nyhetsmedier, sociala medier osv.) leverera mer relevant reklam genom att använda de extraherade insikterna som ytterligare signaler till annonsservern (att presentera reklam för sportskor är mer relevant under en fotbollsmatch än under en simtävling).

- Användarengagemang – Videoinsikter kan användas för att förbättra användarnas engagemang genom positionering av relevanta videor. Ett exempel är en utbildningsvideo där sfärer förklaras under de första 30 minuterna och pyramider under efterföljande 30 minuter. En student som läser om pyramiderna har mer nytta av videon om den positioneras med start från 30-minutersmarkeringen.

Mer information finns i den här [bloggen](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Komma igång med Video Indexer-portalen](video-indexer-get-started.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)
