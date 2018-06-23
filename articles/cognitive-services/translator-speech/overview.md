---
title: Tal översättning API-dokumentationen | Microsoft Docs
titleSuffix: Cognitive Services
description: Använd Microsoft översättare tal översättning API för att lägga till tal tal och tal text översättning till dina program.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: article
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 15f27e6b5b2fd7384958a660156855fc65f4e558
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352431"
---
# <a name="microsoft-translator-speech-api"></a>Microsoft Translator-API för talöversättning
Microsoft översättare tal-API kan användas för att lägga till slutpunkt till slutpunkt, realtid, tal översättningar till program, verktyg eller en lösning som flerspråkig tal översättning oavsett måloperativsystem eller programmeringsspråk. API: et kan användas för både tal tal och tal översättning av text.

Microsoft översättare Text API är en Azure-tjänst, en del av den [Microsoft kognitiva Services API-samlingen](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) av machine learning och AI algoritmer i molnet, lätt att använda i din utvecklingsprojekt.

Med Microsoft översättare tal-API klientprogram strömma tal ljud till tjänsten och få tillbaka en dataström med text - och ljud-baserade resultat, bland annat den tolkade texten i käll-språk och dess översättning på språket som mål. Textresultat produceras genom att använda automatisk Speech Recognition (ASR) tillhandahålls av djupa neurala nätverk till inkommande ljudström. Rådata ASR utdata ytterligare förbättras av en ny teknik som kallas Sanntext för att återspegla närmare användaravsikt. Till exempel Sanntext tar bort disfluencies (hmms och coughs), upprepade ord och återställer rätt skiljetecken och versaler. Möjligheten att maskera med eller undanta profanities ingår också. Redovisning och översättning motorer är särskilt utbildade för att hantera vardagliga samtalsuttryck tal. 

Tjänsten tal översättning använder identifiering av tystnad för att avgöra i slutet av en utterance. Efter en paus i röst aktivitet direktuppspelas tjänsten tillbaka ett slutresultat för slutförda utterance. Tjänsten kan också skicka tillbaka ofullständiga resultat som ger mellanliggande erkännanden och översättningar för en utterance pågår. 

För taligenkänning tal översättning möjligheten tjänsten att syntetisera tal (text till tal) från talade texten i språk som mål. Text till tal-ljud har skapats i det format som anges av klienten. WAV och MP3 format är tillgängliga.

Tal översättning API använder WebSocket-protokollet för att tillhandahålla en full duplex kommunikationskanalen mellan klienten och servern. 

## <a name="about-microsoft-translator"></a>Om Microsoft Translator
Microsoft Translator är en molnbaserad maskinöversättning-tjänst. Är kärnan i den här tjänsten [översättare Text API] (https://www.microsoft.com/en-us/translator/translatorapi.aspx) och översättare Speech API power olika Microsoft-produkter och tjänster som används av tusentals företag över hela världen i sina program och arbetsflöden, så att deras innehåll till nå en global publik.

Lär dig mer om den [tjänsten Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Microsoft Translator Neural maskinöversättning (NMT)
Microsoft översättare tal-API används både äldre statistiska maskinöversättning (SMT) och nyare neural maskinöversättning (NMT) för att tillhandahålla översättningar.

Statistisk maskinöversättning har nått en nivå vad gäller förbättring av prestanda. Översättningen inte längre förbättra alls betydande för allmän system med SMT. En ny teknik för AI-baserade översättning har blivit momentum baserat på Neurala nätverk (NN).

NMT ger bättre översättningar inte bara från en rå översättningen bedömningen synvinkel utan även eftersom låter mer flytande, mänsklig än SMT de. Viktiga orsaken till det här flytbarhet är att NMT använder fullständig kontexten för en mening för att översätta ord. SMT tar bara några ord omedelbar kontexten före och efter varje ord.

NMT modeller är kärnan i API: et och är inte synliga för användare. Endast märkbar skillnaderna är:
* Förbättrad översättningen, särskilt för språk, till exempel kinesiska, japanska och arabiska
* Inkompatibilitet med befintliga hubb anpassningsfunktionerna (för användning med Microsoft översättare Text API)

Alla språk som stöds tal översättning drivs av NMT. Därför använder alla tal tal översättning NMT. 

Tal text översättning kan använda en kombination av NMT och SMT beroende på språkparet. Om mål-språk stöds av NMT är fullständig översättningen NMT påslagen. Om mål-språk inte stöds av NMT, är översättningen en kombination av NMT och SMT använda engelska som ”pivot” mellan de två språk. 

Visa stöds språk på [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx). 

Lär dig mer om [hur NMT fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera sig](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Börja koda](quickstarts/csharp.md)

## <a name="see-also"></a>Se också
- [Kognitiva dokumentationssidan för tjänster](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Kognitiva produktsidan för tjänster](https://azure.microsoft.com/services/cognitive-services/)
- [Lösningen och information om priser](https://www.microsoft.com/en-us/translator/home.aspx) 
