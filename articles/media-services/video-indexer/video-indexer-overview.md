---
title: Vad är Video Indexer?
titleSuffix: Azure Media Services
description: Den här artikeln innehåller en översikt över Azure Media Services Video Indexer-tjänsten.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/02/2020
ms.author: juliako
ms.openlocfilehash: efd8386f464bfdf2ac27d3be07e6572dc27952e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163598"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Video Indexer (VI) är Azure Media Services AI-lösning och en del av Azure Cognitive Services varumärke. Video Indexer ger möjlighet att extrahera djupa insikter (utan behov av dataanalys eller kodning färdigheter) med hjälp av maskininlärning modeller baserade på flera kanaler (röst, sång, visuell). Du kan ytterligare anpassa och träna modellerna. Tjänsten möjliggör djupsökning, minskar driftskostnaderna, möjliggör nya möjligheter till intäktsgenerering och skapar nya användarupplevelser på stora arkiv med videor (med låga inträdeshinder).

Om du vill börja extrahera insikter med Video Indexer måste du skapa ett konto och ladda upp videor. När du laddar upp dina videor till Video Indexer analyseras både grafik och ljud genom att köra olika AI-modeller. När Video Indexer analyserar din video, de insikter som extraheras av AI-modellerna.

Följande diagram är en illustration och inte en teknisk förklaring av hur Video Indexer fungerar i backend.

![Flödesdiagram för Azure Media Services Video Indexer](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer, och du får inte använda Video Indexer eller någon Azure-tjänst på ett sätt som bryter mot andras rättigheter, eller som kan vara skadliga för andra.

Innan du laddar upp någon video/bild till Video Indexer måste du ha alla rätt rättigheter att använda videon/bilden, inklusive, där så krävs enligt lag, alla nödvändiga medgivanden från individer (om sådana finns) i videon/bilden, för användning, bearbetning och lagring av deras data i Video Indexer och Azure. Vissa jurisdiktioner kan införa särskilda rättsliga krav för insamling, onlinebehandling och lagring av vissa kategorier av uppgifter, såsom biometriska uppgifter. Innan du använder Video Indexer och Azure för bearbetning och lagring av data som omfattas av särskilda juridiska krav, måste du se till att alla sådana juridiska krav som kan gälla för dig.

Om du vill veta mer om efterlevnad, sekretess och säkerhet i Video Indexer kan du besöka Microsoft [Trust Center](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). Om du har Microsofts sekretessskyldigheter, datahantering och lagringspraxis, inklusive hur du tar bort dina data, läser du Microsofts [sekretesspolicy,](https://privacy.microsoft.com/PrivacyStatement) [villkoren för onlinetjänster](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") och [tillägget för databehandling](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Genom att använda Video Indexer samtycker du till att vara bunden av OST, DPA och sekretesspolicyn.

## <a name="what-can-i-do-with-video-indexer"></a>Vad kan jag göra med Video Indexer?

Video Indexerers insikter kan tillämpas på många scenarier, bland dem är:

* *Djup sökning:* Använd de insikter som extraheras från videon för att förbättra sökupplevelsen i ett videobibliotek. Indexering av talade ord och ansikten kan till exempel göra det möjligt för sökupplevelsen att hitta ögonblick i en video där en person talade vissa ord eller när två personer sågs tillsammans. Sökning baserat på sådana insikter från videor är tillämplig på nyhetsbyråer, utbildningsinstitut, programföretag, ägare av underhållningsinnehåll, LOB-appar för företag och i allmänhet till alla branscher som har ett videobibliotek som användarna behöver söka mot.
* *Skapande av innehåll*: Skapa trailers, markera rullar, innehåll i sociala medier eller nyhetsklipp baserat på de insikter videoindexer extraherar från ditt innehåll. Nyckelrutor, scenmarkörer och tidsstämplar för personer och etikettutseenden gör skapandeprocessen mycket smidigare och enklare, och gör att du kan komma till de delar av videon du behöver för det innehåll du skapar.
* *Tillgänglighet*: Oavsett om du vill göra ditt innehåll tillgängligt för personer med funktionshinder eller om du vill att ditt innehåll ska distribueras till olika regioner med olika språk kan du använda transkriptionen och översättningen som tillhandahålls av videoindexerare på flera språk.
* *Intäktsgenerering:* Video Indexer kan bidra till att öka värdet på videor. Till exempel kan branscher som är beroende av annonsintäkter (nyhetsmedier, sociala medier och så vidare) leverera relevanta annonser genom att använda de extraherade insikterna som ytterligare signaler till annonsservern.
* *Innehållsmodererering*: Använd text- och visuella modeller för innehållsmoderering för att skydda användarna från olämpligt innehåll och verifiera att innehållet du publicerar matchar organisationens värden. Du kan automatiskt blockera vissa videor eller varna användarna om innehållet.
* *Rekommendationer*: Videoinsikter kan användas för att förbättra användarnas engagemang genom att lyfta fram relevanta videoögonblick för användarna. Genom att tagga varje video med ytterligare metadata kan du rekommendera användarna de mest relevanta videorna och markera de delar av videon som matchar deras behov.

## <a name="features"></a>Funktioner

Följande lista visar de insikter du kan hämta från dina videor med videoindexeringsvideo- och ljudmodeller:

### <a name="video-insights"></a>Videoinsikter

* **Ansiktsigenkänning**: Identifierar och grupperar ansikten som visas i videon.
* **Celebrity identifiering:** Video Indexer identifierar automatiskt över 1 miljon kändisar, som världsledare, skådespelare, skådespelerskor, idrottare, forskare, företag och tech ledare över hela världen. Uppgifterna om dessa kändisar kan också hittas på olika webbplatser (IMDB, Wikipedia och så vidare).
* **Kontobaserad ansiktsidentifiering**: Video Indexer tränar en modell för ett visst konto. Den känner sedan igen ansikten i videon baserat på den tränade modellen. Mer information finns i [Anpassa en personmodell från videoindexerarens webbplats](customize-person-model-with-website.md) och anpassa en [personmodell med API:et för videoindexerare](customize-person-model-with-api.md).
* **Miniatyrextraktion för ansikten** ("bästa ansikte"): Identifierar automatiskt det bästa tagna ansiktet i varje grupp av ansikten (baserat på kvalitet, storlek och frontposition) och extraherar det som en bildtillgång.
* **OCR (Visual Text Recognition):** Extraherar text som visas visuellt i videon.
* **Moderering av visuellt innehåll**: Identifierar vuxna och/eller vågade visuella objekt.
* **Identifiering av etiketter**: Identifierar visuella objekt och åtgärder som visas.
* **Scensegmentering**: Avgör när en scen ändras i video baserat på visuella tips. En scen visar en enda händelse och den består av en serie på varandra följande bilder, som är semantiskt relaterade.
* **Bildigetering:** Avgör när ett skott ändras i video baserat på visuella ledtrådar. Ett skott är en serie bilder tagna från samma filmkamera. Mer information finns i [Scener, bilder och nyckelrutor](scenes-shots-keyframes.md).
* **Identifiering av svarta bildrutor**: Identifierar svarta bildrutor som visas i videon.
* **Extrahering av bildrutor**: Identifierar stabila nyckelbilder i en video.
* **Rullande krediter**: Identifierar början och slutet av rullande krediter i slutet av TV-program och filmer.
* **Identifiering av animerade figurer** (förhandsgranskning): Identifiering, gruppering och igenkänning av tecken i animerat innehåll via integrering med anpassad vision för [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Mer information finns i [Identifiering av animerade tecken](animated-characters-recognition.md).
* **Redaktionell skott typ upptäckt:** Märkning skott baserat på deras typ (som bred skott, medium skott, närbild, extrem närbild, två skott, flera personer, utomhus och inomhus, och så vidare). Mer information finns i [Identifiering av redaktionell skotttyp](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Ljudinsikter

* **Automatisk språkidentifiering**: Identifierar automatiskt dominant talat språk. Språk som stöds inkluderar engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska. Om språket inte kan identifieras med tillförsikt förutsätter Video Indexer att det talade språket är engelska. Mer information finns i [Språkidentifieringsmodell](language-identification-model.md).
* **Talidentifiering och transkription** på flera språk (förhandsgranskning): Identifierar automatiskt det talade språket i olika segment från ljud. Det skickar varje segment av mediefilen som ska transkriberas och sedan kombinerar transkriptionen tillbaka till en enhetlig transkription. Mer information finns i [Identifiera och transkribera innehåll med flera språk](multi-language-identification-transcription.md)automatiskt .
* **Ljudtranskription**: Konverterar tal till text på 12 språk och tillåter tillägg. Språk som stöds inkluderar engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, brasiliansk portugisiska, hindi och koreanska.
* **Textning**: Skapar textning i tre format: VTT, TTML och SRT.
* **Två kanalbearbetning:** Automatisk identifierar separat avskrift och sammanfogas till en enda tidslinje.
* **Brusreducering**: Rensar upp telefoniljud eller bullriga inspelningar (baserat på Skype-filter).
* **Avskrift anpassning** (CRIS): Tåg anpassade tal till textmodeller för att skapa branschspecifika utskrifter. Mer information finns i [Anpassa en språkmodell från videoindexerarens webbplats](customize-language-model-with-website.md) och anpassa en [språkmodell med API:erna för videoindexerare](customize-language-model-with-api.md).
* **Speaker uppräkning**: Kartor och förstår vilken talare talade vilka ord och när.
* **Högtalarstatistik**: Ger statistik för talarnas talförhållanden.
* **Moderering av textinnehåll**: Identifierar stötande text i ljudavskriften.
* **Ljudeffekter**: Identifierar ljudeffekter som hand klappar, tal och tystnad.
* **Emotion detection**: Identifierar känslor baserat på tal (vad som sägs) och röst tonalitet (hur det sägs). Känslan kan vara glädje, sorg, ilska eller rädsla.
* **Översättning**: Skapar översättningar av ljudavskriften till 54 olika språk.

### <a name="audio-and-video-insights-multi-channels"></a>Ljud- och videoinsikter (flera kanaler)

Vid indexering med en kanal kommer partiellt resultat för dessa modeller att vara tillgängligt.

* **Extrahera sökord**: Extraherar nyckelord från tal och visuell text.
* **Namngivna enheter extrahering:** Extraherar varumärken, platser och personer från tal och visuell text via bearbetning av naturligt språk (NLP).
* **Ämnesinferens**: Skapar inferens av huvudämnen från avskrifter. IPTC-taxonomi på andra nivån ingår.
* **Artefakter**: Extraherar en omfattande uppsättning ”nästa detaljnivå”-artefakter för de olika modellerna.
* **Attitydanalys**: Identifierar positiva, negativa och neutrala attityder i tal och visuell text.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hur kommer jag igång med Video Indexer?

Du kan komma åt videoindexeringsfunktionerna på tre sätt:

* Video Indexer portal: En lättanvänd lösning som låter dig utvärdera produkten, hantera kontot och anpassa modeller.

    Mer information om portalen finns [på Komma igång med webbplatsen Video Indexer](video-indexer-get-started.md).  

* API-integrering: Alla Video Indexers funktioner är tillgängliga via ett REST API, som gör att du kan integrera lösningen i dina appar och din infrastruktur.

    Information om hur du kommer igång som utvecklare finns i [Använda REST API för videoindexerare](video-indexer-use-apis.md).

* Inbäddningsbar widget: Gör att du kan bädda in videoindexeringsstatistiken, spelar- och redigeringsupplevelserna i appen.

    Mer information finns [i Bädda in visuella widgetar i programmet](video-indexer-embed-widgets.md).

Om du använder webbplatsen läggs insikterna till som metadata och visas i portalen. Om du använder API:er är insikterna tillgängliga som en JSON-fil.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Kom igång med webbplatsen Video Indexer](video-indexer-get-started.md).
- [Bearbeta innehåll med REST API för videoindexerare](video-indexer-use-apis.md).
- [Bädda in visuella widgetar i programmet](video-indexer-embed-widgets.md).
