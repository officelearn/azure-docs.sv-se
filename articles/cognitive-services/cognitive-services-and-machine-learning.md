---
title: Kognitiva tjänster och maskininlärning
titleSuffix: Azure Cognitive Services
description: Lär dig hur Azure Cognitive Services passar ihop med andra Azure-erbjudanden för maskininlärning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531487"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services och Machine Learning

Cognitive Services tillhandahåller maskininlärningsfunktioner för att lösa allmänna problem som att analysera text för känslomässiga känslor eller analysera bilder för att känna igen objekt eller ansikten. Du behöver inte särskild maskininlärning eller datavetenskap kunskap för att använda dessa tjänster. 

[Cognitive Services](welcome.md) är en grupp tjänster som alla stöder olika, generaliserade förutsägelsefunktioner. Tjänsterna är indelade i olika kategorier för att hjälpa dig att hitta rätt tjänst. 

|Servicekategori|Syfte|
|--|--|
|[Beslut](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Skapa appar som ger rekommendationer för välinformerade och effektiva beslut.|
|[Språk](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Tillåt att dina appar behandlar naturligt språk med fördefinierade skript, utvärderar känslor och lär sig att känna igen vad användare vill.|
|[Sök](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Lägg till API:er för Bing-sökning i dina appar och utnyttja möjligheten att söka i miljontals webbsidor, bilder, videor och nyheter med ett enda API-anrop.|
|[Tal](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Konvertera tal till text och text till tal som låter naturligt. Översätt från ett språk till ett annat och aktivera talarverifiering och -igenkänning.|
|[Visuellt innehåll](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Avkänning, identifiering, textning, indexering och moderering för bilder, videor och innehåll med digitala pennanteckningar.|
||||

Använd Cognitive Services när du:

* Kan använda en generaliserad lösning.
* Åtkomstlösning från ett REST API eller SDK för programmering. 

Använd en annan maskininlärningslösning när du:

* Måste välja algoritmen och måste träna på mycket specifika data.

## <a name="what-is-machine-learning"></a>Vad är maskininlärning?

Maskininlärning är ett koncept där du samlar data och en algoritm för att lösa ett specifikt behov. När data och algoritm har tränats är utdata en modell som du kan använda igen med olika data. Den tränade modellen ger insikter baserat på nya data. 

Processen att bygga ett maskininlärningssystem kräver viss kunskap om maskininlärning eller datavetenskap.

Maskininlärning tillhandahålls med hjälp av [Azure Machine Learning (AML) produkter och tjänster](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context).

## <a name="what-is-a-cognitive-service"></a>Vad är en kognitiv tjänst?

En Cognitive Service tillhandahåller delar av eller alla komponenter i en maskininlärningslösning: data, algoritm och tränad modell. Dessa tjänster är avsedda att kräva allmän kunskap om dina data utan att behöva erfarenhet av maskininlärning eller datavetenskap. Dessa tjänster tillhandahåller både REST API och språkbaserade SDK:er. Därför måste du ha kunskaper i programmeringsspråk för att kunna använda tjänsterna.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Hur är Cognitive Services och Azure Machine Learning (AML) liknande?

Båda har slutmålet att tillämpa artificiell intelligens (AI) för att förbättra affärsverksamheten, men hur var och en tillhandahåller detta i respektive erbjudanden är olika. 

I allmänhet är publiken olika:

* Cognitive Services är till för utvecklare utan maskininlärning.
* Azure Machine Learning är skräddarsytt för datavetare. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Hur skiljer sig en kognitiv tjänst från maskininlärning?

En cognitive service tillhandahåller en utbildad modell åt dig. Detta sammanför data och en algoritm, tillgängliga från ett REST API eller SDK. Du kan implementera den här tjänsten inom några minuter, beroende på ditt scenario.  En cognitivetjänst ger svar på allmänna problem, till exempel nyckelfraser i text- eller objektidentifiering i bilder. 

Maskininlärning är en process som i allmänhet kräver en längre tid för att implementera. Den här tiden går åt till datainsamling, rengöring, omvandling, algoritmval, modellutbildning och distribution för att komma till samma nivå av funktioner som tillhandahålls av en kognitiv tjänst. Med maskininlärning är det möjligt att ge svar på högspecialiserade och/eller specifika problem. Maskininlärningsproblem kräver förtrogenhet med det specifika ämnet och data om det aktuella problemet, samt expertis inom datavetenskap.

## <a name="what-kind-of-data-do-you-have"></a>Vilken typ av data har du?

Cognitive Services, som en grupp av tjänster, kan kräva ingen, vissa eller alla anpassade data för den tränade modellen. 

### <a name="no-additional-training-data-required"></a>Inga ytterligare utbildningsdata krävs

Tjänster som tillhandahåller en fullt utbildad modell kan behandlas som en _svart låda_. Du behöver inte veta hur de fungerar eller vilka data som användes för att träna dem. Du tar dina data till en fullt utbildad modell för att få en förutsägelse. 

### <a name="some-or-all-training-data-required"></a>Vissa eller alla utbildningsdata krävs

Vissa tjänster kan du ta med egna data, sedan träna en modell. På så sätt kan du utöka modellen med hjälp av Tjänstens data och algoritm med dina egna data. Utdata matchar dina behov. När du tar med egna data kan du behöva tagga data på ett sätt som är specifikt för tjänsten. Till exempel, om du tränar en modell för att identifiera blommor, kan du ge en katalog över blomma bilder tillsammans med platsen för blomman i varje bild för att träna modellen. 

En tjänst kan _tillåta_ dig att tillhandahålla data för att förbättra sina egna data. En tjänst kan _kräva_ att du tillhandahåller data. 

### <a name="real-time-or-near-real-time-data-required"></a>Realtidsdata eller nära realtidsdata som krävs

En tjänst kan behöva data i realtid eller i nära realtid för att skapa en effektiv modell. Dessa tjänster bearbetar betydande mängder modelldata. 

## <a name="service-requirements-for-the-data-model"></a>Servicekrav för datamodellen

Följande data kategoriserar varje tjänst med vilken typ av data den tillåter eller kräver.

|Kognitiv tjänst|Inga utbildningsdata krävs|Du tillhandahåller vissa eller alla träningsdata|Datainsamling i realtid eller nära realtid|
|--|--|--|--|
|[Avvikelseidentifiering](./Anomaly-Detector/overview.md)|x|x|x|
|Bing Search |x|||
|[Datorseende](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./Custom-Vision-Service/home.md)||x||
|[Ansikte](./Face/Overview.md)|x|x||
|[Formigenkänning](./form-recognizer/overview.md)||x||
|[Avancerad läsare](./immersive-reader/overview.md)|x|||
|[Handskriftsigenkänning](./Ink-recognizer/overview.md)|x|x||
|[Språk understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personanpassning](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Högtalare Recognizer](./speaker-recognition/home.md)||x||
|[Tal text till tal (TTS)](speech-service/text-to-speech.md)|x|x||
|[Tal tal till text (STT)](speech-service/speech-to-text.md)|x|x||
|[Talöversättning](speech-service/speech-translation.md)|x|||
|[Textanalys](./text-analytics/overview.md)|x|||
|[Översättare Text](./translator/translator-info-overview.md)|x|||
|[Översättare Text - anpassad översättare](./translator/custom-translator/overview.md)||x||

*Personalizer behöver bara utbildningsdata som samlas in av tjänsten (eftersom den fungerar i realtid) för att utvärdera din policy och dina data. Personalizer behöver inte stora historiska datauppsättningar för up-front eller batch utbildning. 

## <a name="where-can-you-use-cognitive-services"></a>Var kan du använda Cognitive Services?
 
Tjänsterna används i alla program som kan göra REST API eller SDK-anrop. Exempel på program är webbplatser, robotar, virtuell eller blandad verklighet, stationära och mobila applikationer. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Hur är Azure Cognitive Search relaterat till Cognitive Services?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) är en separat molnsöktjänst som eventuellt använder Cognitive Services för att lägga till avbildnings- och bearbetning av naturligt språk för att indexera arbetsbelastningar. Cognitive Services exponeras i Azure Cognitive Search genom [inbyggda kunskaper](../search/cognitive-search-predefined-skills.md) som radbryter enskilda API:er. Du kan använda en kostnadsfri resurs för genomgångar, men planera att skapa och koppla en [fakturerbar resurs](../search/cognitive-search-attach-cognitive-services.md) för större volymer.

## <a name="how-can-you-use-cognitive-services"></a>Hur kan du använda Cognitive Services?

Varje tjänst innehåller information om dina data. Du kan kombinera tjänster tillsammans för att kedja lösningar som att konvertera tal (ljud) till text, översätta texten till många språk och sedan använda de översatta språken för att få svar från en kunskapsbas. Cognitive Services kan användas för att skapa intelligenta lösningar på egen hand, men de kan också kombineras med traditionella maskininlärningsprojekt för att komplettera modeller eller påskynda utvecklingsprocessen. 

Cognitive Services som tillhandahåller exporterade modeller för andra verktygsverk:

|Kognitiv tjänst|Modellinformation|
|--|--|
|[Custom Vision](./custom-vision-service/home.md)|[Exportera](./Custom-Vision-Service/export-model-python.md) för Tensorflow för Android, CoreML för iOS11, ONNX för Windows ML|

## <a name="learn-more"></a>Läs mer

* [Arkitekturguide – Vilka är maskininlärningsprodukterna på Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Maskininlärning - Introduktion till djupinlärning kontra maskininlärning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Nästa steg

* Skapa ditt Cognitive Service-konto i [Azure-portalen](cognitive-services-apis-create-account.md) eller med [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Läs om hur du [autentiserar](authentication.md) till en kognitiv tjänst.
* Använd [diagnostikloggning](diagnostic-logging.md) för problemidentifiering och felsökning. 
* Distribuera en cognitivetjänst i en [Docker-behållare](cognitive-services-container-support.md).
* Håll dig uppdaterad med [serviceuppdateringar](https://azure.microsoft.com/updates/?product=cognitive-services).
