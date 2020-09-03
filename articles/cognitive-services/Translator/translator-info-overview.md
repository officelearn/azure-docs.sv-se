---
title: Vad är Translator? -Translator
titlesuffix: Azure Cognitive Services
description: Integrera översättare i dina program, webbplatser, verktyg och andra lösningar för att tillhandahålla användar upplevelser med flera språk.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 206e1bc86404fa41b84647c50482dbe63301c0af
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425967"
---
# <a name="what-is-translator"></a>Vad är Translator?

Azure Cognitive Services Translator är en molnbaserad maskin översättnings tjänst och ingår i [azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) -familjen av kognitiva API: er för att skapa smarta appar. Translator är enkelt att integrera i dina program, webbplatser, verktyg och lösningar. Det gör att du kan lägga till användar upplevelser med flera språk på [över 70 språk](languages.md)och kan användas på alla maskin varu plattformar med valfritt operativ system för översättning av text till text-språk.

## <a name="about-microsoft-translator"></a>Om Microsoft Translator

Azure Cognitive Services Translator är en molnbaserad maskin översättnings tjänst. Translator har ett antal Microsoft-produkter och-tjänster och används av tusentals företag i hela världen i sina program och arbets flöden, vilket ger sitt innehåll möjlighet att komma åt en global publik.

Tal översättning, som drivs av Translator, är också tillgängligt via [Microsoft Speech service](https://docs.microsoft.com/azure/cognitive-services/speech-service/). Den kombinerar funktioner från Translator Speech API och Custom Speech Service till en enhetlig och helt anpassningsbar tjänst. 

## <a name="language-support"></a>Stöd för språk

Microsoft Translator ger flerspråksstöd för översättning, translitteration, språkidentifiering och ordlistor. Se [språkstöd](language-support.md) för en fullständig lista, eller kom åt listan programmatiskt med [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).  

## <a name="microsoft-translator-neural-machine-translation"></a>Neural maskinöversättning med Microsoft Translator

Neural maskinöversättning är den nya standarden för högkvalitativ AI-driven maskinöversättning. Den ersätter den äldre statistiska maskinöversättningen, som nådde en kvalitetsplatå i mitten av 2010-talet.

Neural maskinöversättning ger bättre översättningar än den statistiska varianten. Den ger bättre överensstämmelse betydelsemässigt och låter dessutom mer mänsklig. Den viktigaste orsaken till det är att hela meningskontexten används när ord översätts. I den statistiska översättningen användes bara direkta samband med några få ord före och efter varje ord.

Neurala modeller är kärnan i API:et och visas inte för slutanvändaren. Den enda märkbara skillnaden är förbättrad översättning, och särskilt för språk som arabiska, kinesiska och japanska.

Läs mer om [hur neural maskinöversättning fungerar](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="language-customization"></a>Språkanpassning

Ett tillägg till tjänsten Translator, anpassad översättare kan användas tillsammans med Translator för att anpassa neurala översättnings systemet och förbättra översättningen för din speciella terminologi och stil.

Med den anpassade översättningstjänsten kan du skapa översättningssystem som hanterar den terminologi som används inom företaget eller branschen. Det anpassade översättnings systemet integreras sedan enkelt i dina befintliga program, arbets flöden och webbplatser på flera olika typer av enheter, via den vanliga översättaren, med hjälp av parametern Category.

Läs mer om [språkanpassning](customization.md)

## <a name="next-steps"></a>Nästa steg

- [Registrera dig](translator-text-how-to-signup.md) för en åtkomst nyckel.
- [API-referensen](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference) innehåller den tekniska dokumentationen för API:erna.
- [Prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
