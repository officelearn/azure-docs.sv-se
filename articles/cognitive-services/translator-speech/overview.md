---
title: Vad är Translator Speech-tjänsten?
titleSuffix: Azure Cognitive Services
description: Använd tjänsten Translator Speech API för att lägga till tal till tal- och tal till text-översättning till dina program.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 77e60fe39f7cbb985ee0e7ed2785805e80c389ae
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341148"
---
# <a name="what-is-translator-speech-api"></a>Vad är Translator Speech API?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech API kan användas för att lägga till talöversättning, heltäckande och i realtid, program, verktyg och lösningar som kräver talöversättning på flera språk oberoende av måloperativsystem och utvecklingsspråk. API:et kan användas för både tal till tal- och tal till text-översättning.

Translator Text API är en del av Microsofts [Azure Cognitive Services-API-samling](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive). Det här är en samling algoritmer för maskininlärning och AI i molnet som är enkla att använda i utvecklingsprojekt.

Med Translator Speech API kan klientprogram strömma talat ljud till tjänsten och få tillbaka en dataström med text- och ljudbaserade resultat, som inkluderar den tolkade texten på källspråket och en översättning som visas på målspråket. Textresultatet produceras genom automatisk taligenkänning (ASR) som drivs av djupa neurala nätverk för den inkommande ljudströmmen. ASR-råutdata förbättras ytterligare av en ny teknik som kallas TrueText som återspeglar användaravsikten närmare. Till exempel tar TrueText bort avbrott (hummanden och hostningar), upprepade ord och återställer till rätt användning av interpunktion och versaler. Möjligheten att maskera eller undanta svordomar ingår också. Igenkänningsmotorn och översättningsmotorn är särskilt tränade för att hantera samtal. 

Translator Speech-tjänsten använder identifiering av tystnad för att avgöra slutet av ett påstående. Efter en paus i röstaktiviteten återger tjänsten ett slutresultat för slutförda påståenden. Tjänsten kan också skicka tillbaka ofullständiga resultat, som ger mellanliggande igenkänningar och översättningar av ett pågående påstående. 

För tal till tal-översättning ger tjänsten möjlighet att syntetisera tal (text till tal) från talad text på målspråket. Text till tal-ljud skapas i det format som anges av klienten. WAV- och MP3-format är tillgängliga.

Translator Speech API använder WebSocket-protokoll för att tillhandahålla en full duplex-kommunikationskanal mellan klienten och servern. 

## <a name="about-microsoft-translator"></a>Om Microsoft Translator
Microsoft Translator är en molnbaserad maskinöversättningstjänst. Kärnan i tjänsten är [Translator Text API] och (https://www.microsoft.com/en-us/translator/translatorapi.aspx)Translator Speech API. De finns i olika Microsoft-produkter och tjänster och används av tusentals företag över hela världen i program och arbetsflöden i syfte att nå en världsomspännande publik.

Läs mer om [Microsoft Translator-tjänsten](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Neural maskinöversättning med Microsoft Translator (NMT)
Translator Speech API använder både äldre statistisk maskinöversättning (SMT) och nyare neural maskinöversättning (NMT) för att tillhandahålla översättningar.

Statistisk maskinöversättning har nått en platå när det gäller förbättring av prestanda. Det går inte längre att förbättra översättningskvaliteten på något bra sätt för allmänna system med SMT. En ny teknik för AI-baserad översättning vinner mark baserat på neurala nätverk (NN).

Neural maskinöversättning ger bättre översättningar än den statistiska varianten. Den ger bättre överensstämmelse betydelsemässigt och låter dessutom mer mänsklig. Den viktigaste orsaken till det är att hela meningskontexten används när ord översätts. I den statistiska översättningen användes bara direkta samband med några få ord före och efter varje ord.

Neurala modeller är kärnan i API:et och visas inte för slutanvändaren. De enda märkbara skillnaderna är:
* Förbättrad översättning, och särskilt för språk som arabiska, kinesiska och japanska
* Inkompatibilitet med befintliga hubbanpassningsfunktioner (för användning med Microsoft Translator Text API)

Alla språk för talöversättning som stöds drivs av NMT. Därför använder all tal till tal-översättning NMT. 

Tal till text-översättning kan använda en kombination av NMT och SMT beroende på språkparet. Om målspråket stöds av NMT är hela översättningen NMT-baserad. Om målspråket inte stöds av NMT är översättningen en kombination av NMT och SMT och använder engelska som en ”medelpunkt” mellan de två språken. 

Visa stöds språk på [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Läs mer om [hur neural maskinöversättning fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera dig](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Börja koda](quickstarts/csharp.md)

## <a name="see-also"></a>Se även
- [Dokumentationssida om Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Produktsida om Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Information om lösningar och prissättning](https://www.microsoft.com/en-us/translator/home.aspx) 
