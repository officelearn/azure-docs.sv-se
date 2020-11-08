---
title: Sök i en anpassad vy – Anpassad sökning i Bing
titleSuffix: Azure Cognitive Services
description: När du har konfigurerat din anpassade Sök upplevelse kan du testa den från Anpassad sökning i Bing-portalen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: a27f252f401d0fcf863391a0d45538b01b1141e9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367292"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Anropa din Anpassad sökning i Bing instans från portalen

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

När du har konfigurerat din anpassade Sök upplevelse kan du testa den från Anpassad sökning i Bing- [portalen](https://customsearch.ai). 

![en skärm bild av den anpassade Bing search-portalen](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Skapa en Sök fråga 

När du har loggat in på Anpassad sökning i Bing- [portalen](https://customsearch.ai)väljer du din Sök instans och klickar på fliken **produktion** . Under **slut punkter** väljer du en API-slutpunkt (till exempel webb-API). Din prenumeration avgör vilka slut punkter som visas.

Om du vill skapa en Sök fråga anger du parameter värden för slut punkten. Observera att parametrarna som visas i portalen kan ändras beroende på vilken slut punkt du väljer. Mer information finns i [referensen för API för anpassad sökning](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) . Om du vill ändra vilken prenumeration din Sök instans använder lägger du till rätt prenumerations nyckel och uppdaterar lämplig marknad och/eller språk parametrar.

Några viktiga parametrar är nedan:


|Parameter  |Beskrivning  |
|---------|---------|
|Söka i data     | Sök termen att söka efter. Endast tillgängligt för slut punkter för webb-, bild-, video-och automatiska förslag |
|Anpassat konfigurations-ID | Konfigurations-ID för den valda anpassade Sök instansen. Det här fältet är skrivskyddat. |
|Telefonförsäljning     | Marknaden som resultatet kommer från. Endast tillgängligt för slut punkter för webb-, bild-, video-och värdbaserade gränssnitt.        |
|Prenumerationsnyckel | Den prenumerations nyckel som ska testas med. Du kan välja en nyckel i list rutan eller ange en manuellt.          |

Om du klickar på **ytterligare parametrar upptäcks** följande parametrar:  

|Parameter  |Beskrivning  |
|---------|---------|
|Säker sökning     | Ett filter som används för att filtrera webb sidor för innehåll som är vuxna. Endast tillgängligt för slut punkter för webb-, bild-, video-och värdbaserade gränssnitt. Observera att Bing Custom Videosökning endast stöder två värden: `moderate` och `strict` .        |
|Språk för användargränssnittet    | Språket som används för användar gränssnitts strängar. Om du t. ex. aktiverar bilder och videor i värdbaserade användar gränssnitt använder flikarna **bild** och **video** det angivna språket.        |
|Antal     | Det antal Sök resultat som ska returneras i svaret. Endast tillgängligt för slut punkter för webb, bild och video.         |
|Offset    | Antalet Sök resultat som ska hoppas över innan resultat returneras. Endast tillgängligt för slut punkter för webb, bild och video.        |
    
När du har angett alla obligatoriska alternativ klickar du på **anropa** för att Visa JSON-svaret i den högra rutan. Om du väljer den värdbaserade användar gränssnitts slut punkten kan du testa Sök funktionen i det nedre fönstret.

## <a name="change-your-bing-custom-search-subscription"></a>Ändra din Anpassad sökning i Bing prenumeration

Du kan ändra prenumerationen som är kopplad till din Anpassad sökning i Bing instans utan att skapa en ny instans. Om du vill att API-anrop ska skickas och debiteras till en ny prenumeration skapar du en ny Anpassad sökning i Bing resurs i Azure Portal. Använd den nya prenumerations nyckeln i API-begärandena, tillsammans med instansens anpassade konfigurations-ID.

## <a name="next-steps"></a>Nästa steg

- [Anropa din anpassade vy med C #](./call-endpoint-csharp.md)
- [Anropa din anpassade vy med Java](./call-endpoint-java.md)
- [Anropa din anpassade vy med NodeJs](./call-endpoint-nodejs.md)
- [Anropa din anpassade vy med python](./call-endpoint-python.md)

- [Anropa din anpassade vy med C# SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)