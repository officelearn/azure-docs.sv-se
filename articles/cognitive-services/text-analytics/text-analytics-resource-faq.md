---
title: Vanliga frågor om textanalys API - kognitiva Azure-tjänster | Microsoft Docs
description: Få svar på vanliga frågor om Microsoft kognitiva Services Text Analytics API på Azure.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: bf82899b4317f0f5ce0f6ca5096dccef7cddd931
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355173"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Vanliga frågor (FAQ) om textanalys API

 Få svar på vanliga frågor om begrepp och koden scenarion Text Analytics-API: et för Microsoft kognitiva Services på Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Textanalys kan identifiera sarcasm?

Analys är för positiv negativ sentiment i stället för sinnesstämning identifiering.

Det finns alltid viss mån inexaktheter i sentiment analys, men modellen är mest användbart när det finns ingen dolda betydelse eller underordnad text till innehållet. Irony, sarcasm, humor och innehåll på samma sätt nuanced förlitar sig på kulturell kontext och normer att förmedla avsikt. Den här typen av innehåll är bland de svåraste att analysera. Största skillnaden mellan en viss poäng som produceras av analyzer och en subjektiv bedömning av en mänskliga är vanligtvis för innehåll med nuanced betydelse.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kan jag använda min egen träningsdata eller modeller?

Nej, modeller är pretrained. Endast åtgärder på överförda data bedömningen viktiga frasen extrahering och identifiera språk. Vi värd inte anpassade modeller. Om du vill skapa och vara värd för anpassade machine learning-modeller bör du den [maskin learning funktioner i Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kan jag begära ytterligare språk?

Sentiment analys och viktiga frasen extrahering är tillgängliga för en [Välj antal språk](text-analytics-supported-languages.md). Bearbetning av naturligt språk är komplex och kräver betydande test innan nya funktioner kan släppas. Därför undvika före meddelar support så att ingen tar ett beroende på funktioner som behöver mer tid att mogna. 

För att hjälpa oss att prioritera vilka språk som ska fungera på nästa, rösta för specifika språk på [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Varför viktiga frasen extrahering returnera några ord men inte andra?

Viktiga frasen extrahering eliminerar irrelevanta ord och fristående adjektiv. Adjektiv och substantiv kombinationer, till exempel ”spektakulära vyer” eller ”dimmig väder” returneras tillsammans.

I allmänhet består utdata av substantiv och objekt i meningarna. Utdata visas i prioritetsordning, med den första frasen är de viktigaste. Vikten mäts av antalet gånger som en viss konceptet nämns eller relationen elementet till andra element i texten.

## <a name="why-does-output-vary-given-identical-inputs"></a>Varför utdata varierar, beroende på identiska indata?

Förbättringar av modeller och algoritmer meddelas om ändringen är större eller tyst slipstream i tjänsten när uppdateringen är mindre. Över tiden, kan det hända att samma text inkommande resultat i en annan sentiment poäng eller viktiga frasen utdata. Detta är ett normalt och avsiktlig följd av att använda utbildningsresurser för hanterad dator i molnet.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstning för den vår [UserVoice-webbplatsen](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Se också

 [StackOverflow: Textanalys API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitiva tjänster](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
