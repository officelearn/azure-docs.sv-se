---
title: Sök i en anpassad vy – Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: Beskriver hur du söker i en anpassad vy av webben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 814f57d4011823da80e53cce41ffcb523fc0bf1b
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404999"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Anropa din Anpassad sökning i Bing instans från portalen

När du har konfigurerat din anpassade Sök upplevelse kan du testa den från Anpassad sökning i Bing- [portalen](https://customsearch.ai). 

![en skärm bild av den anpassade Bing search-portalen](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Skapa en Sök fråga 

När du har loggat in på Anpassad sökning i Bing- [portalen](https://customsearch.ai)väljer du din Sök instans och klickar på fliken **produktion** . Under **slut punkter**väljer du en API-slutpunkt (till exempel webb-API). Din prenumeration avgör vilka slut punkter som visas.

Om du vill skapa en Sök fråga anger du parameter värden för slut punkten. Observera att parametrarna som visas i portalen kan ändras beroende på vilken slut punkt du väljer. Mer information finns i referensen för [API för anpassad sökning](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) . Om du vill ändra vilken prenumeration din Sök instans använder lägger du till rätt prenumerations nyckel och uppdaterar lämplig marknad och/eller språk parametrar.

Några viktiga parametrar är nedan:


|Parameter  |Beskrivning  |
|---------|---------|
|Söka i data     | Sök termen att söka efter. Endast tillgängligt för slut punkter för webb-, bild-, video-och automatiska förslag |
|Anpassat konfigurations-ID | Konfigurations-ID för den valda anpassade Sök instansen. Det här fältet är skrivskyddat. |
|Market     | Marknaden som resultatet kommer från. Endast tillgängligt för slut punkter för webb-, bild-, video-och värdbaserade gränssnitt.        |
|Prenumerationsnyckel | Den prenumerations nyckel som ska testas med. Du kan välja en nyckel i list rutan eller ange en manuellt.          |

Om du klickar på **ytterligare parametrar upptäcks** följande parametrar:  

|Parameter  |Beskrivning  |
|---------|---------|
|Safe Search     | Ett filter som används för att filtrera webb sidor för innehåll som är vuxna. Endast tillgängligt för slut punkter för webb-, bild-, video-och värdbaserade gränssnitt.        |
|Användar gränssnittets språk    | Språket som används för användar gränssnitts strängar. Om du t. ex. aktiverar bilder och videor i värdbaserade användar gränssnitt använder flikarna **bild** och **video** det angivna språket.        |
|Count     | Det antal Sök resultat som ska returneras i svaret. Endast tillgängligt för slut punkter för webb, bild och video.         |
|Offset    | Antalet Sök resultat som ska hoppas över innan resultat returneras. Endast tillgängligt för slut punkter för webb, bild och video.        |
    
När du har angett alla obligatoriska alternativ klickar du på **anropa** för att Visa JSON-svaret i den högra rutan. Om du väljer den värdbaserade användar gränssnitts slut punkten kan du testa Sök funktionen i det nedre fönstret.

## <a name="change-your-bing-custom-search-subscription"></a>Ändra din Anpassad sökning i Bing prenumeration

Du kan ändra prenumerationen som är kopplad till din Anpassad sökning i Bing instans utan att skapa en ny instans. Om du vill att API-anrop ska skickas och debiteras till en ny prenumeration skapar du en ny Anpassad sökning i Bing resurs i Azure Portal. Använd den nya prenumerations nyckeln i API-begärandena, tillsammans med instansens anpassade konfigurations-ID.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade vy medC#](./call-endpoint-csharp.md)
- [Anropa din anpassade vy med Java](./call-endpoint-java.md)
- [Anropa din anpassade vy med NodeJs](./call-endpoint-nodejs.md)
- [Anropa din anpassade vy med python](./call-endpoint-python.md)

- [Anropa din anpassade vy med C# SDK: n](./sdk-csharp-quick-start.md)
