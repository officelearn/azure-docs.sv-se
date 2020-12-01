---
title: 'Snabbstart: Skapa en första instans för anpassad sökning i Bing'
titleSuffix: Azure Cognitive Services
description: Använd den här snabb starten för att skapa en anpassad Bing-instans som kan söka igenom domäner och webb sidor som du definierar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338341"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Snabbstart: Skapa din första instans för anpassad sökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Om du vill använda anpassad sökning i Bing måste du skapa en anpassad sökinstans som definierar vyn eller sektorn på webben. Den här instansen innehåller de offentliga domäner och webbplatser som du vill söka i samt eventuella önskade justeringar för rangordning. 

Använd [portalen för anpassad sökning i Bing](https://customsearch.ai) för att skapa instansen. 

![En bild av portalen för anpassad sökning i Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad sökinstans

Så här skapar du en instans för anpassad sökning i Bing:

1. Klicka på **Kom igång** på webbplatsen för [portalen för anpassad sökning i Bing](https://customsearch.ai) och logga in med ditt Microsoft-konto.

2. Klicka på **New Instance** (Ny instans) och ange ett beskrivande namn. Du kan när som helst ändra namnet på instansen.
 
3. På sidan **Sökupplevelse** klickar du på fliken **Aktiv** och anger webbadressen till en eller flera webbplatser som du vill inkludera i sökningen. 

    > [!NOTE]
    > Instanser av anpassad Bing-sökning returnerar endast resultat för domäner och webbplatser som är offentliga och har indexerats av Bing.

4. Du kan använda höger sida i portalen för anpassad sökning i Bing för att ange en fråga och granska de sökresultat som returneras av sökinstansen. Om inga resultat returneras kan du prova att ange en annan URL.  

5. Klicka på **Publicera** för att publicera ändringarna till produktionsmiljön, och uppdatera instansens slutpunkter.

6.  Klicka på fliken **produktion** under **slut punkter** och kopiera ditt **anpassade konfigurations-ID**. Du behöver detta ID för att anropa API:et för anpassad sökning genom att lägga till det till frågeparametern `customconfig=` i dina anrop.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: anropa din Anpassad sökning i Bing-slutpunkt](./call-endpoint-csharp.md)