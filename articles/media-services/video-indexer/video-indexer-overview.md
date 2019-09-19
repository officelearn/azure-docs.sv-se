---
title: Vad är Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Det här avsnittet ger en översikt över tjänsten Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/17/2019
ms.author: juliako
ms.openlocfilehash: a30fa858ea7befa2363e0ac2742e16d46037d0e0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123515"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Azure Media Services Video Indexer är en AI-lösning som bygger på Azure-Cognitive Services. Video Indexer ger möjlighet att extrahera djupgående insikter (utan behov av data analys eller kodnings kunskaper) med hjälp av maskin inlärnings modeller baserade på flera kanaler (röst, Vocals, visualisering). Du kan anpassa och träna modeller ytterligare. Tjänsten möjliggör djupgående sökning, minskar drifts kostnaderna, ger nya Sälj möjligheter, nya användar upplevelser på stora Arkiv av videor (med låga ingångs hinder). 

Om du vill börja extrahera insikter med Video Indexer måste du skapa ett konto och ladda upp videor. När du laddar upp videor till Video Indexer, analyserar den både visuella objekt och ljud genom att köra olika AI-modeller. När Video Indexer analyserar videon extraheras de insikter som extraheras av modellerna.

![Flödesdiagram](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Video Indexer insikter kan tillämpas på många olika scenarier:

* *Djup sökning* – Använd de insikter som extraheras från videon för att förbättra Sök upplevelsen i ett video bibliotek. Att indexera talade ord och ansikten kan till exempel göra det möjligt att söka efter moment i en video där en person har ekrar på vissa ord eller när två personer setts tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.
* *Skapa innehåll*: skapa trailrar, markera rullar, innehåll i sociala medier eller nyhets klipp baserat på insikter video Indexer utdrag från ditt innehåll. Nyckel bild rutor, scener och tidsstämplar för visning av personer och etiketter gör processen mycket smidigare och enklare och ger dig rätt till de intressanta delar av videon du behöver för det innehåll som du skapar.
* *Hjälpmedel*: om du vill göra ditt innehåll tillgängligt för personer med funktions nedsättning eller om du vill att innehållet ska distribueras till olika regioner med olika språk kan du använda avskriften och översättningen som tillhandahålls av video indexerare på flera språk.
* *Monetarisering*: Video Indexer kan hjälpa till att öka värdet för videor. Som exempel kan branscher som förlitar sig på AD-intäkter (till exempel nyhets medier, sociala medier osv.) leverera relevanta annonser genom att använda de extraherade insikterna som ytterligare signaler till AD-servern.
* *Innehålls moderator*: Använd text-och visuella innehålls redigerings modeller för att hålla dina användare skyddade från olämpligt innehåll och kontrol lera att innehållet som du publicerar matchar din organisations värden. Du kan automatiskt blockera vissa videor eller Varna dina användare om innehållet. 
* *Rekommendationer*: Du kan använda video insikter för att förbättra användarnas engagemang genom att markera relevant video moment till användare. Genom att tagga varje video med ytterligare metadata kan du rekommendera användarna de mest relevanta videor och markera den del av videon som ska matcha deras behov. 

## <a name="features"></a>Funktioner

Nedan följer en lista med insikter som du kan hämta från dina videor med hjälp av Video Indexer video-och ljud modeller:

### <a name="video-insights"></a>Video insikter

* **Ansikts igenkänning**: Identifierar och grupper ansikten som visas i videon.
* **Kändis-ID**: Video Indexer identifierar automatiskt över 1 000 000 kändisar – till exempel världs ledare, aktörer, actresses, tävlande, forskare, företag och teknik ledare över hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
* **Konto-baserad ansikts identifiering**: Video Indexer tågen en modell för ett bestämt konto. Den identifierar sedan ansikten i videon baserat på den tränade modellen. Mer information finns i [Anpassa en person modell från video Indexer webbplats](customize-person-model-with-website.md) och [Anpassa en person modell med video Indexer API](customize-person-model-with-api.md).
* **Bild extrahering för ansikten** ("bästa FACET"): Identifierar automatiskt den bästa infångade ytan i varje grupp av ansikten (baserat på kvalitet, storlek och front position) och extraherar den som en bild till gång.
* **Visuell text igenkänning** (OCR): Extraherar text som visas visuellt i videon.
* **Moderator för visuellt innehåll**: Identifierar vuxen och/eller vågat visuella objekt.
* **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
* **Scen segmentering**: anger när en scens ändringar i videon baseras på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande dum par, som är semantiskt relaterade.
* **Bild identifiering**: anger när en bild ändras i video utifrån visuella tips. En bild är en serie med ramar som tas från samma rörelse-och bildkamera. Mer information finns i scener, bilder och nyckel bild rutor.
* **Identifiering av svart ram**: Identifierar svarta bild rutor som visas i videon.
* **Extrahering av nyckel rutor**: Identifierar stabila nyckel rutor i en video.
* **Rullande krediter**: identifiera början och slutet av de rullande eftertexterna i slutet av TV-program och filmer.

### <a name="audio-insights"></a>Ljud insikter

* **Automatisk språk identifiering**: Identifierar automatiskt det dominerande talade språket. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska. Om språket inte kan identifieras med förtroende förutsätter Video Indexer att det talade språket är engelska. Mer information finns i [språk identifierings modell](language-identification-model.md).
* **Ljud avskrift**: Konverterar tal till text på 12 språk och tillåter tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, portugisiska (Brasilien), hindi och koreanska.
* **Dold textning**: Skapar dold textning i tre format: VTT, TTML, SRT.
* **Två kanal bearbetning**: Automatisk identifierar, separera avskrift och sammanslagningar till en enda tids linje.
* **Brus minskning**: Tar bort ljud-eller brus inspelnings telefoner (baserat på Skype-filter).
* **Avskrifts anpassning** (CRI: er): Anpassad tal till text modeller för tågen för att skapa branschspecifika avskrifter. Mer information finns i [Anpassa en språk modell från video Indexer webbplats](customize-language-model-with-website.md) och [Anpassa en språk modell med video Indexer-API: er](customize-language-model-with-api.md).
* **Uppräkning av högtalare**: Mappar och förstår vilken talare som har ekrar som ord och när.
* **Högtalar statistik**: Innehåller statistik för talares tal förhållande.
* **Text innehålls moderator**: Identifierar explicit text i ljud avskriften.
* **Ljud effekter**: Identifierar ljud effekter som hand claps, tal och tystnad.
* **Känslo-identifiering**: Identifierar känslor baserat på tal (vad som sägs) och röst ton (hur det sägs). Känslo kan vara glädje, ledsenhet, ilska eller frukt.
* **Översättning**: Skapar översättningar av ljud avskriften till 54 olika språk.

### <a name="audio-and-video-insights-multi-channels"></a>Ljud-och video insikter (flera kanaler)

När indexering av en kanal del resultat för dessa modeller är tillgänglig

* **Extrahering av nyckelord**: Extraherar nyckelord från tal och visuell text.
* **Extrahering av varumärken**: Extraherar varumärken från tal och visuell text.
* **Avsnitts härledning**: Gör det enklare att utföra huvud ämnena i avskrifter. IPTC-taxonomi på den första nivån ingår.
* **Artefakter**: Extraherar omfattande uppsättning "nästa nivå av information"-artefakter för varje modell.
* **Sentiment-analys**: Identifierar positiva, negativa och neutrala sentiment från tal och visuell text.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hur kan jag komma igång med Video Indexer?

Du kan komma åt Video Indexer-funktioner på tre olika sätt: 

* Video Indexer portal – en lättanvänd lösning som du kan använda för att utvärdera produkten, hantera kontot och anpassa modeller. 

    Mer information om portalen finns i [Kom igång med video Indexer webbplats](video-indexer-get-started.md).  
* API-integrering – alla Video Indexers funktioner är tillgängliga via en REST API så att du kan integrera lösningen i dina program och din infrastruktur. 

    Information om hur du kommer igång som utvecklare finns i [använda Video Indexer REST API](video-indexer-use-apis.md). 
* Emendable-widget – gör att du kan bädda in video Indexer Insights, spelare och redigerings upplevelser i ditt program. 

    Mer information finns i [bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md). 

Om du använder webbplatsen läggs insikterna till som metadata och visas i portalen. Om du använder API: er är insikterna tillgängliga som en JSON-fil. 

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)
