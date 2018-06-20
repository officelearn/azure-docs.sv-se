---
title: Förstå begrepp för konvertering i THOMAS - Azure | Microsoft Docs
description: Lär dig hur du kan ändra utterances innan förutsägelser språk förstå (THOMAS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 56de113b41be419a5e39d705a22466139c1c29ce
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266115"
---
# <a name="data-conversion-concepts-in-luis"></a>Begrepp för konvertering i THOMAS
THOMAS är ett sätt att konvertera utterances från talade utterances till text utterances innan förutsägelse. 

## <a name="speech-to-intent-conversion-concepts"></a>Tal till avsiktshantering konvertering begrepp
Konvertera till text i THOMAS tal kan du skicka talade utterances till en slutpunkt och ta emot svaret THOMAS förutsägelse. Processen är en integrering av den [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) med THOMAS. 

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **Bing Speech API** nyckeln för den här integreringen. Nyckeln THOMAS fungerar för den här integreringen.

### <a name="new-endpoint"></a>Ny slutpunkt 
Den här integreringen skapar en ny slutpunkt och [priser](luis-boundaries.md#key-limits) modell. Slutpunkten är kan ta emot både talas och text utterances så att du kan använda den som en enda slutpunkt. 

### <a name="quota-usage"></a>Kvotanvändningen
Se [nyckeln gränser](luis-boundaries.md#key-limits) information. 

### <a name="data-retention"></a>Datakvarhållning
Data som skickas till slutpunkten, oavsett om den är tal eller text, används bara för att förbättra din modell för tal. Den används inte utöver din modell för att förbättra tal eller THOMAS i en allmän kapacitet. När appen THOMAS tas bort kvarhållna data också att tas bort.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Rätt stavfel med den här självstudiekursen](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions