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
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: 2afc3a859ddb5378b6313c43d693842fdb5fce14
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296430"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer (VI) är Azure Media Services AI-lösningen och en del av Microsoft Cognitive Services varumärket. Video Indexer ger möjlighet att extrahera djupgående insikter (utan behov av data analys eller kodnings kunskaper) med hjälp av maskin inlärnings modeller baserade på flera kanaler (röst, Vocals, visualisering). Du kan anpassa och träna modeller ytterligare. Tjänsten möjliggör djupgående sökning, minskar drifts kostnaderna, ger nya Sälj möjligheter, nya användar upplevelser på stora Arkiv av videor (med låga ingångs hinder). 

Om du vill börja extrahera insikter med Video Indexer måste du skapa ett konto och ladda upp videor. När du laddar upp videor till Video Indexer, analyserar den både visuella objekt och ljud genom att köra olika AI-modeller. När Video Indexer analyserar videon extraheras de insikter som extraheras av modellerna.

Följande diagram är en illustration och inte en teknisk förklaring av hur Video Indexer fungerar i Server delen.

![Flödesdiagram](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Video Indexer insikter kan tillämpas på många olika scenarier:

* *Djup sökning* – Använd de insikter som extraheras från videon för att förbättra Sök upplevelsen i ett video bibliotek. Att indexera talade ord och ansikten kan till exempel göra det möjligt att söka efter moment i en video där en person har ekrar på vissa ord eller när två personer setts tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.
* *Skapa innehåll*: skapa trailrar, markera rullar, innehåll i sociala medier eller nyhets klipp baserat på insikter video Indexer utdrag från ditt innehåll. Nyckel bild rutor, scener och tidsstämplar för visning av personer och etiketter gör processen mycket smidigare och enklare och ger dig rätt till de intressanta delar av videon du behöver för det innehåll som du skapar.
* *Hjälpmedel*: om du vill göra ditt innehåll tillgängligt för personer med funktions nedsättning eller om du vill att innehållet ska distribueras till olika regioner med olika språk kan du använda avskriften och översättningen som tillhandahålls av video indexerare på flera språk.
* *Monetarisering*: video Indexer kan hjälpa till att öka värdet för videor. Som exempel kan branscher som förlitar sig på AD-intäkter (till exempel nyhets medier, sociala medier osv.) leverera relevanta annonser genom att använda de extraherade insikterna som ytterligare signaler till AD-servern.
* *Innehålls moderator*: Använd text-och visuella innehålls redigerings modeller för att hålla dina användare skyddade från olämpligt innehåll och kontrol lera att innehållet som du publicerar matchar din organisations värden. Du kan automatiskt blockera vissa videor eller Varna dina användare om innehållet. 
* *Rekommendationer*: video insikter kan användas för att förbättra användarnas engagemang genom att markera relevant video moment till användare. Genom att tagga varje video med ytterligare metadata kan du rekommendera användarna de mest relevanta videor och markera den del av videon som ska matcha deras behov. 

## <a name="features"></a>Funktioner

Nedan följer en lista med insikter som du kan hämta från dina videor med hjälp av Video Indexer video-och ljud modeller:

### <a name="video-insights"></a>Video insikter

* **Ansiktsigenkänning**: Identifierar och grupperar ansikten som visas i videon.
* **Kändis-identifiering**: video Indexer identifierar automatiskt över 1 000 000 kändisar – till exempel världs ledare, aktörer, actresses, tävlande, forskare, företag och teknik ledare över hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
* **Kontobaserad ansiktsidentifiering**: Video Indexer tränar en modell för ett visst konto. Den identifierar sedan ansikten i videon baserat på den tränade modellen. Mer information finns i [Anpassa en person modell från video Indexer webbplats](customize-person-model-with-website.md) och [Anpassa en person modell med video Indexer API](customize-person-model-with-api.md).
* Rendering **av miniatyrer för ansikten** ("bästa ansikte"): identifierar automatiskt den bästa infångade ytan i varje grupp av ansikten (baserat på kvalitet, storlek och front position) och extraherar den som en bild till gång.
* **Visuell text igenkänning** (OCR): extraherar text som visas visuellt i videon.
* **Moderering av visuellt innehåll**: Identifierar vuxna och/eller vågade visuella objekt.
* **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
* **Scen segmentering**: anger när en scens ändringar i videon baseras på visuella tips. En scen visar en enskild händelse och den består av en serie med efterföljande dum par, som är semantiskt relaterade.
* **Bild identifiering**: anger när en bild ändras i video utifrån visuella tips. En bild är en serie med ramar som tas från samma rörelse-och bildkamera. Mer information finns i scener, bilder och nyckel bild rutor.
* **Identifiering av svarta bildrutor**: Identifierar svarta bildrutor som visas i videon.
* **Extrahering av bildrutor**: Identifierar stabila nyckelbilder i en video.
* **Rullande krediter**: identifiera början och slutet av de rullande eftertexterna i slutet av TV-program och filmer.
* Igenkänning av **animerade tecken** (för hands version): identifiering, gruppering och igenkänning av tecken i animerat innehåll via integration med [Cognitive Services anpassad vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Mer information finns i [animerat teckensnitts identifiering](animated-characters-recognition.md).
* **Identifiering av redaktionell text typer**: Tagga bilder baserat på deras typ (t. ex. wide-bild, medie tagning, nära upp, extrema nära upp, två bilder, flera personer, utomhus och inomhus osv.). Mer information finns i [identifiering av redaktionella typer av tagningar](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Ljud insikter

* **Automatisk språkidentifiering**: Identifierar automatiskt dominant talat språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska. Om språket inte kan identifieras med förtroende förutsätter Video Indexer att det talade språket är engelska. Mer information finns i [språk identifierings modell](language-identification-model.md).
* **Tal identifiering och avskriftering i flera språk** (för hands version): identifierar automatiskt det talade språket i olika segment från ljud, vilket innebär att varje segment i medie filen ska skrivas av och kombinera avskriften till en enhetlig avskrift. Mer information finns i [identifiera och automatisk identifiering av innehåll på flera språk](multi-language-identification-transcription.md).
* **Ljud avskrift**: konverterar tal till text på 12 språk och tillåter tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, portugisiska (Brasilien), hindi och koreanska.
* **Textning**: Skapar textning i tre format: VTT, TTML och SRT.
* **Bearbetning av två kanaler**: Identifierar automatiskt, avgränsar avskrift och slår samman till en enda tidslinje.
* **Brus minskning**: tar bort ljud-eller brus inspelnings telefoner (baserat på Skype-filter).
* **Avskrifts anpassning** (CRI: er): anpassad tal från tågen till text modeller för att skapa branschspecifika avskrifter. Mer information finns i [Anpassa en språk modell från video Indexer webbplats](customize-language-model-with-website.md) och [Anpassa en språk modell med video Indexer-API: er](customize-language-model-with-api.md).
* **Uppräkning av högtalare**: Maps och förstår vilken talare som är eker för ord och när.
* **Högtalar statistik**: tillhandahåller statistik för talares tal förhållande.
* **Moderering av textinnehåll**: Identifierar stötande text i ljudavskriften.
* **Ljudeffekter**: Identifierar ljudeffekter, till exempel handklappningar, tal och tystnad.
* **Känslo-identifiering**: identifierar känslor baserat på tal (vad som sägs) och röst ton (hur det sker). Känslo kan vara glädje, ledsenhet, ilska eller frukt.
* **Översättning**: Skapar översättningar av ljudavskriften till 54 olika språk.

### <a name="audio-and-video-insights-multi-channels"></a>Ljud-och video insikter (flera kanaler)

När indexering av en kanal del resultat för dessa modeller är tillgänglig

* **Extrahering av nyckelord**: extraherar nyckelord från tal och visuell text.
* **Extrahering av namngivna enheter**: extraherar varumärken, platser och personer från tal och visuell text via naturlig språk bearbetning (NLP).
* **Ämnesinferens**: Skapar inferens av huvudämnen från avskrifter. IPTC-taxonomin på andra nivån ingår.
* **Artefakter**: Extraherar en omfattande uppsättning ”nästa detaljnivå”-artefakter för de olika modellerna.
* **Attitydanalys**: Identifierar positiva, negativa och neutrala attityder i tal och visuell text.

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
