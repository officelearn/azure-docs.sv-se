---
title: Vanliga frågor och svar om API:et för textanalys
titleSuffix: Azure Cognitive Services
description: Hitta svar på vanliga frågor om begrepp, kod och scenarier relaterade till Text Analytics API för Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: aca3b0bea65923cee1bae25020058666c973922c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837129"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Vanliga frågor och svar om cognitivetjänsten textanalys

 Hitta svar på vanliga frågor om begrepp, kod och scenarier relaterade till Text Analytics API för Microsoft Cognitive Services på Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Kan TextAnalys identifiera sarkasm?

Analys är för positiva negativa känslor snarare än humör upptäckt.

Det finns alltid en viss grad av oprecision i sentimentanalys, men modellen är mest användbar när det inte finns någon dold mening eller undertext till innehållet. Ironi, sarkasm, humor och liknande nyanserat innehåll bygger på kulturella sammanhang och normer för att förmedla uppsåt. Denna typ av innehåll är bland de mest utmanande att analysera. Typiskt, den största skillnaden mellan en viss poäng som produceras av analysatorn och en subjektiv bedömning av en människa är för innehåll med nyanserad mening.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kan jag lägga till mina egna träningsdata eller modeller?

Nej, modellerna är förtränade. De enda åtgärder som är tillgängliga för uppladdade data är bedömning, extrahering av nyckelfraser och språkidentifiering. Vi är inte värd för anpassade modeller. Om du vill skapa och vara värd för anpassade maskininlärningsmodeller bör du tänka på [maskininlärningsfunktionerna i Microsoft R Server](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Kan jag begära fler språk?

Sentimentanalys och nyckelfrasutdragning är tillgängliga för ett [urval av språk](text-analytics-supported-languages.md). Bearbetning av naturligt språk är komplex och kräver omfattande tester innan nya funktioner kan släppas. Därför undviker vi att i förväg tillkännage stöd så att ingen tar ett beroende av funktioner som behöver mer tid att mogna. 

För att hjälpa oss att prioritera vilka språk som ska fungera härnäst, rösta på specifika språk på [User Voice](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Varför nyckelfras utvinning tillbaka några ord men inte andra?

Tilläggsfrasextraktion eliminerar icke-väsentliga ord och fristående adjektiv. Adjektiv-substantiv kombinationer, såsom "spektakulära vyer" eller "dimmigt väder" returneras tillsammans.

I allmänhet består utdata av substantiv och objekt i meningen. Produktionen är listad i prioritetsordning, med den första frasen är den viktigaste. Betydelse mäts med det antal gånger ett visst begrepp nämns, eller förhållandet mellan detta element och andra element i texten.

## <a name="why-does-output-vary-given-identical-inputs"></a>Varför varierar utdata, med tanke på identiska ingångar?

Förbättringar av modeller och algoritmer meddelas om ändringen är större, eller tyst slipstreamed i tjänsten om uppdateringen är mindre. Med tiden kanske du upptäcker att samma textinmatning resulterar i en annan sentimentpoäng eller nyckelfrasutdata. Detta är en normal och avsiktlig konsekvens av att använda hanterade maskininlärningsresurser i molnet.

## <a name="next-steps"></a>Nästa steg

Är din fråga om en saknad funktion? Överväg att begära eller rösta för det på vår [UserVoice webbplats](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Se även

 [StackOverflow: API för textanalys](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Kognitiva tjänster](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
