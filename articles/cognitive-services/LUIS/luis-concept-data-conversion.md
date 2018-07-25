---
title: Förstå begrepp för konvertering i LUIS - Azure | Microsoft Docs
description: Lär dig hur du kan ändra yttranden innan förutsägelser i Språkförståelse (LUIS)
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 05a02191af171936dd9f5c4bf469b9c84aa144a3
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223709"
---
# <a name="data-conversion-concepts-in-luis"></a>Begrepp för konvertering i LUIS
LUIS är ett sätt att konvertera yttranden från talat yttranden till text yttranden innan förutsägelse. 

## <a name="speech-to-intent-conversion-concepts"></a>Tal till avsikt konvertering begrepp
Konvertera tal till text i LUIS kan du skicka talat yttranden till en slutpunkt och ta emot svar LUIS förutsägelse. Processen är en integrering av den [tal](https://docs.microsoft.com/azure/cognitive-services/Speech) tjänst med LUIS. 

### <a name="key-requirements"></a>Viktiga krav
Du behöver inte skapa en **taligenkänning för Bing** nyckel för den här integreringen. En **Språkförståelse** nyckeln som skapats i Azure portal som fungerar för den här integreringen. Använd inte nyckeln LUIS starter, fungerar inte för den här integreringen.

### <a name="new-endpoint"></a>Ny slutpunkt 
Den här integreringen skapar en ny slutpunkt och [priser](luis-boundaries.md#key-limits) modellen. Slutpunkten, via den [tal SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk), kan ta emot både sägs och text yttranden så att du kan använda den som en enda slutpunkt. 

### <a name="quota-usage"></a>Kvotanvändning
Se [viktiga begränsningar](luis-boundaries.md#key-limits) information. 

### <a name="data-retention"></a>Datakvarhållning
Data som skickas till slutpunkten via tal SDK, oavsett om det är tal eller text, används bara för att förbättra din talmodell. Den används inte utöver din modell för att förbättra tal eller LUIS i en allmän kapacitet. När appen LUIS tas bort raderas även kvarhållna data.

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd tal till text](luis-tutorial-speech-to-intent.md)

