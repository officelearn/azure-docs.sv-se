---
title: 'Snabbstart: Skapa en första instans för anpassad sökning i Bing | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Använd den här artikeln för att skapa en anpassad Bing-instans som kan söka i domäner och webbplatser som du definierar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 6949824f598194456837544526223b823dcfc3e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273342"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Snabbstart: Skapa din första instans för anpassad sökning i Bing

Om du vill använda anpassad sökning i Bing måste du skapa en anpassad sökinstans som definierar vyn eller sektorn på webben. Den här instansen innehåller de offentliga domäner och webbplatser som du vill söka i samt eventuella önskade justeringar för rangordning. 

Använd [portalen för anpassad sökning i Bing](https://customsearch.ai) för att skapa instansen. 

![En bild av portalen för anpassad sökning i Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

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

6.  Klicka på den **produktion** fliken **slutpunkter**, och kopiera din **anpassad konfigurations-ID**. Du behöver detta ID för att anropa API:et för anpassad sökning genom att lägga till det till frågeparametern `customconfig=` i dina anrop.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabbstart: Anropa slutpunkt för anpassad Bing-sökning](./call-endpoint-csharp.md)
