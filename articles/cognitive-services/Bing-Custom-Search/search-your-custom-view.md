---
title: 'Anpassad sökning i Bing: Söka efter en anpassad vy | Microsoft Docs'
description: Beskriver hur du söker efter en anpassad vy över webben
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7a2db7881dfe7efedb1a83637a6281f786652958
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964913"
---
# <a name="call-your-custom-search"></a>Anropa dina anpassad sökning

Innan du gör ditt första anrop till API: et för anpassad sökning att hämta sökresultat för din instans, måste du hämta en prenumerationsnyckel för Cognitive Services. Om du vill hämta en nyckel för API för anpassad sökning, se [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Prova

När du har konfigurerat anpassade användningen kan testa du konfigurationen från anpassad sökning i portalen. 

1. Logga in på [anpassad sökning i](https://customsearch.ai).
2. Klicka på en anpassad sökning i instans från listan av instanser.
3. Klicka på den **produktion** fliken. 
4. Under den **slutpunkter** väljer du en slutpunkt (till exempel webb-API). Prenumerationen avgör vilka slutpunkter ska visas (se [priser](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) för Prenumerationsalternativ). 
5. Ange parametervärden. 

    Följande är möjliga parametrar som du kan ange (den faktiska listan beror på den valda slutpunkten). Mer information om dessa parametrar finns i [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) referens.

    - **Fråga**: den sökterm att söka efter. Endast tillgängligt för webb, bild, Video och automatiska förslag slutpunkter.
    - **ID för anpassade**: konfigurations-ID för den valda Custom Search-instansen. Det här fältet är skrivskyddat.
    - **Marknaden**: marknaden var resultatet kommer från. Endast tillgängligt för webb, bild, Video och värdbaserade UI-slutpunkter.
    - **Prenumerationsnyckel**: prenumerationsnyckeln att testa med. Du kan välja en nyckel i listrutan eller ange en manuellt.  
      
    Klicka på **ytterligare parametrar** visar följande parametrar:  
      
    - **Säker sökning**: ett filter som används för att filtrera webbsidor för vuxet innehåll. Endast tillgängligt för webb, bild, Video och värdbaserade UI-slutpunkter.
    - **Språk för användargränssnittet**: det språk som används för användaren gränssnittet strängar. Exempel: Om du aktiverar bilder och videor i Användargränssnittet för värd för den **bild** och **Video** flikarna använder det angivna språket.
    - **Antal**: antalet sökresultat att returnera i svaret. Endast tillgängligt för webb, bild och videoinnehåll slutpunkter.
    - **Förskjutning**: antalet sökresultat ska hoppas över innan det returneras resultatet. Endast tillgängligt för webb, bild och videoinnehåll slutpunkter.

6. När du har angett alla obligatoriska alternativ, klickar du på **anropa** Se JSON-svar i den högra rutan. 

Om du väljer den värdbaserade UI-slutpunkten kan testa du sökupplevelsen längst ned i fönstret.

## <a name="next-steps"></a>Nästa steg

- [Anropa den anpassade vyn med C#](./call-endpoint-csharp.md)
- [Anropa den anpassade vyn med Java](./call-endpoint-java.md)
- [Anropa den anpassade vyn med NodeJs](./call-endpoint-nodejs.md)
- [Anropa den anpassade vyn med Python](./call-endpoint-python.md)

- [Anropa den anpassade vyn med C#-SDK](./sdk-csharp-quick-start.md)