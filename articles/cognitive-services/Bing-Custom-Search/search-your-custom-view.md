---
title: Sök i en anpassad vy - Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
description: När du har konfigurerat din anpassade sökupplevelse kan du testa den från portalen för anpassad sökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983120"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Anropa din anpassade Bing-sökningsinstans från portalen

När du har konfigurerat din anpassade sökupplevelse kan du testa den från [portalen](https://customsearch.ai)För anpassad sökning i Bing . 

![en skärmbild av den anpassade sökportalen Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Skapa en sökfråga 

När du har loggat in på [portalen](https://customsearch.ai)Anpassad sökning i Bing markerar du sökinstansen och klickar på fliken **Produktion.** Under **Slutpunkter**väljer du en API-slutpunkt (till exempel webb-API). Din prenumeration avgör vilka slutpunkter som visas.

Om du vill skapa en sökfråga anger du parametervärdena för slutpunkten. Observera att de parametrar som visas i portalen kan ändras beroende på vilken slutpunkt du väljer. Mer information finns i [API-referensen](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) för anpassad sökning. Om du vill ändra prenumerationen som sökinstansen använder lägger du till rätt prenumerationsnyckel och uppdaterar lämpliga marknads- och/eller språkparametrar.

Några viktiga parametrar är nedan:


|Parameter  |Beskrivning  |
|---------|---------|
|Söka i data     | Söktermen att söka efter. Slutpunkter för webb, bild, video och automatiska förslag |
|Anpassat konfigurations-ID | Konfigurations-ID för den valda custom search-instansen. Det här fältet är skrivskyddat. |
|Marknaden     | Den marknad som resultaten kommer att komma från. Endast tillgängligt för slutpunkter för webb-, bild-, video- och värdbaserade användargränssnitt.        |
|Prenumerationsnyckel | Prenumerationsnyckeln att testa med. Du kan välja en nyckel i listrutan eller ange en manuellt.          |

Om du klickar på **Ytterligare parametrar** visas följande parametrar:  

|Parameter  |Beskrivning  |
|---------|---------|
|Säker sökning     | Ett filter som används för att filtrera webbsidor för barnförbjudet innehåll. Endast tillgängligt för slutpunkter för webb-, bild-, video- och värdbaserade användargränssnitt. Observera att Bing Custom Video Search `moderate` `strict`endast stöder två värden: och .        |
|Språk för användargränssnittet    | Språket som används för användargränssnittssträngar. Om du till exempel aktiverar bilder och videor i värdgränssnittet använder flikarna **Bild** och **Video** det angivna språket.        |
|Antal     | Antalet sökresultat som ska returneras i svaret. Endast tillgängligt för webb-, bild- och videoslutpunkter.         |
|Offset    | Antalet sökresultat som ska hoppa över innan resultaten returneras. Endast tillgängligt för webb-, bild- och videoslutpunkter.        |
    
När du har angett alla obligatoriska alternativ klickar du på **Ring** för att visa JSON-svaret i den högra rutan. Om du väljer slutpunkten för användargränssnittet värd kan du testa sökupplevelsen i det nedre fönstret.

## <a name="change-your-bing-custom-search-subscription"></a>Ändra din prenumeration på anpassad sökning i Bing

Du kan ändra prenumerationen som är associerad med din anpassade Bing-sökningsinstans utan att skapa en ny instans. Om du vill att API-anrop ska skickas och debiteras en ny prenumeration skapar du en ny Bing-anpassad sökresurs i Azure-portalen. Använd den nya prenumerationsnyckeln i dina API-begäranden, tillsammans med instansens anpassade konfigurations-ID.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade vy med C #](./call-endpoint-csharp.md)
- [Ring din anpassade vy med Java](./call-endpoint-java.md)
- [Ring din anpassade vy med NodeJs](./call-endpoint-nodejs.md)
- [Anropa din anpassade vy med Python](./call-endpoint-python.md)

- [Anropa din anpassade vy med C# SDK](./sdk-csharp-quick-start.md)
