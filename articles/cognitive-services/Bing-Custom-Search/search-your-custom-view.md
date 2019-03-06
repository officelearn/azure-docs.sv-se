---
title: Sök efter en anpassad vy – Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Beskriver hur du söker efter en anpassad vy över webben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: f00a8a02ac7f9468516506b6e6a376209d24c270
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57448895"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Anropa anpassad sökning i Bing-instans från portalen

När du har konfigurerat anpassade användningen kan du testa den från inom Bing Custom Search [portal](https://customsearch.ai). 

![en skärmbild av Bing anpassad sökning i portalen](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Skapa en sökfråga 

När du har loggat in Bing Custom Search [portal](https://customsearch.ai), Välj din Sökinstans och klicka på den **produktion** fliken. Under **slutpunkter**, Välj en API-slutpunkt (till exempel webb-API). Prenumerationen avgör vilka slutpunkter som ska visas.

Ange parametervärden för din slutpunkt för att skapa en sökfråga. Observera att de parametrar som visas i portalen kan ändras beroende på den slutpunkt som du väljer. Se den[Custom Search API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) för mer information. 

Vissa viktiga parametrar finns nedan:


|Parameter  |Beskrivning  |
|---------|---------|
|Söka i data     | Den sökterm att söka efter. Endast tillgängligt för webb, bild, Video och automatiska förslag slutpunkter |
|Anpassade konfigurations-ID | Konfigurations-ID för den valda Custom Search-instansen. Det här fältet är skrivskyddat. |
|Marknad     | På marknaden som resulterar kommer kommer från. Endast tillgängligt för webb, bild, Video och värdbaserade UI-slutpunkter.        |
|Prenumerationsnyckel | Prenumerationsnyckel att testa med. Du kan välja en nyckel i listrutan eller ange en manuellt.          |

Klicka på **ytterligare parametrar** visar följande parametrar:  

|Parameter  |Beskrivning  |
|---------|---------|
|Säker sökning     | Ett filter som används för att filtrera webbsidor för vuxet innehåll. Endast tillgängligt för webb, bild, Video och värdbaserade UI-slutpunkter.        |
|Språk för användargränssnittet    | Språket som används för användaren gränssnittet strängar. Exempel: Om du aktiverar bilder och videor i Användargränssnittet för värd för den **bild** och **Video** flikarna använder det angivna språket.        |
|Antal     | Antal resultat från att returnera i svaret. Endast tillgängligt för webb, bild och videoinnehåll slutpunkter.         |
|Offset    | Antalet sökresultat ska hoppas över innan det returneras resultatet. Endast tillgängligt för webb, bild och videoinnehåll slutpunkter.        |
    
När du har angett alla obligatoriska alternativ, klickar du på **anropa** Se JSON-svar i den högra rutan. Om du väljer den värdbaserade UI-slutpunkten kan testa du sökupplevelsen längst ned i fönstret.

## <a name="next-steps"></a>Nästa steg

- [Anropa den anpassade vyn med C#](./call-endpoint-csharp.md)
- [Anropa den anpassade vyn med Java](./call-endpoint-java.md)
- [Anropa den anpassade vyn med NodeJs](./call-endpoint-nodejs.md)
- [Anropa den anpassade vyn med Python](./call-endpoint-python.md)

- [Anropa den anpassade vyn med C#-SDK](./sdk-csharp-quick-start.md)
