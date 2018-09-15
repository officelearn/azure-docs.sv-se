---
title: Vanliga frågor och svar om API för textanalys
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om API för textanalys.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: aa1c5b048c3ef339d01a3a63fd1d565b888ffbbb
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603404"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Vanliga frågor (och svar FAQ) om tjänsten Text Analytics Cognitive

 Hitta svar på vanliga frågor om koncept, kod och scenarier som är relaterat till Textanalys för Microsoft Cognitive Services på Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Textanalys kan identifiera sarkasm?

Analysen är för positiv negativ attityd i stället för stämning identifiering.

Det finns alltid en viss grad av inexaktheter i attitydanalys, men modellen är mest användbara när det finns inga dolda betydelse eller underordnad text till innehållet. Irony, sarkasm, humor och på samma sätt nyanserade innehåll beroende kontexten kulturella och normer att förmedla avsikt. Den här typen av innehåll är ett av de mest utmanande att analysera. Största skillnaden mellan ett visst poängintervall som produceras av analysatorn och en subjektiv bedömning av en människa är vanligtvis för innehåll med nyanserade betydelse.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kan jag använda min egen träningsdata eller modeller?

Nej, modellerna är tränats. De enda åtgärderna som är tillgängliga på överförda data bedömning, extrahering av diskussionsämne och språkidentifiering. Vi hantera inte anpassade modeller. Om du vill skapa och vara värd för anpassade machine learning-modeller, Överväg att den [machine learning-funktioner i Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kan jag begära ytterligare språk?

Attitydanalys och extrahering av diskussionsämne är tillgängliga för en [Välj antal språk](text-analytics-supported-languages.md). Bearbetning av naturligt språk är komplex och kräver omfattande testning innan nya funktioner kan släppas. Därför undvika förväg presenterar support så att ingen får ett beroende på funktioner som behöver mer tid att mogna. 

För att hjälpa oss att prioritera vilka språk som ska fungera på nästa, rösta för vissa språk på [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Varför extrahering av diskussionsämne tillbaka vissa ord, men inte för andra?

Extrahering av diskussionsämne eliminerar ord som inte behövs och fristående adjektiv. Adjektiv-substantiv kombinationer, till exempel ”fantastiskt vyer” eller ”dimmig väder” returneras tillsammans.

Utdata består normalt av substantiv och objekt i meningen. Utdata visas i prioritetsordning, med första frasen är de viktigaste. Vikten mäts efter hur många gånger ett visst koncept nämns eller relationen av elementets för andra element i texten.

## <a name="why-does-output-vary-given-identical-inputs"></a>Varför utdata varierar, beroende på identiska indata?

Förbättringar av modeller och algoritmer tillkännages om ändringen är större eller tyst slipstream i tjänsten om uppdateringen är mindre. Med tiden kan kan det hända att samma text input resultat i en annan sentimentresultatet eller diskussionsämne utdata. Det här är ett normalt och avsiktlig följd av att använda hanterade machine learning-resurser i molnet.

## <a name="next-steps"></a>Nästa steg

Är en fråga om en funktion eller funktionalitet som saknas? Överväg att begära eller röstat vår [UserVoice-webbplats](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Se också

 [StackOverflow: API för textanalys](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
