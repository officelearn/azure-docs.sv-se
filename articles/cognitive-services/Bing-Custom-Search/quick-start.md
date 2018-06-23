---
title: Skapa en första Bing anpassad sökning-instans - kognitiva Microsoft-tjänster
description: Om du vill använda Bing anpassad sökning, måste du skapa en anpassad sökning-instans som definierar vyn eller segment av webbplatsen. Instansen innehåller inställningar som anger den offentliga domäner och underplatser webbsidor som du vill använda Bing för att söka och eventuella justeringar av rangordning.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352884"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Skapa din första Bing anpassad sökning-instans
Om du vill använda Bing anpassad sökning, måste du skapa en anpassad sökning-instans som definierar vyn eller segment av webbplatsen. Instansen innehåller inställningar som anger offentliga domäner, webbplatser och webbsidor som du vill använda Bing för att söka och eventuella justeringar av rangordning. Använd Bing anpassad sökning för att skapa instansen [portal](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad sökning-instans

Skapa en anpassad sökning Bing-instans:

1.  Hämta en nyckel för anpassad sökning API. Se [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Logga in på portalen med ett Microsoft-konto (MSA). Klicka på den **inloggning** knappen. Om det är första gången du använder portalen Följ stegen nedan, annars vidare till steg 3.
    - Om du inte har en MSA, klickar du på **skapa ett Microsoft-konto**. Portalen begär behörighet att komma åt dina data. Klicka på **Ja**.
    - Acceptera villkoren kognitiva tjänster. Kontrollera **acceptera** och på **accepterar**.  
3.  När du har loggat in klickar du på **ny instans** och kalla instansen. Använd ett namn som är meningsfullt och beskriver typ av innehåll som returnerar sökningen. Du kan ändra namnet när som helst. 
4.  På den **Active** fliken i **sökinställningar**, ange Webbadressen till en eller fler platser som du vill inkludera i din sökning.
5.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Om det finns inga resultat kan du ange en ny plats. Bing returnerar resultat endast för offentliga platser som den har indexerats.
6.  Klicka på **publicera** publicera konfigurationsändringar till produktion. När du uppmanas, klickar du på **publicera** att bekräfta.
7.  Klicka på **produktion** > **slutpunkter** och kopiera den **ID för konfiguration av anpassade**. Du behöver detta ID för att anropa API: et för anpassad sökning.

## <a name="next-steps"></a>Nästa steg

Fortsätta att arbeta med anpassad sökning-instans som du har skapat genom att följa anvisningarna i dessa guider:

- [Konfigurera din anpassade sökinställningar](./define-your-custom-view.md)
- [Anropa sökningen anpassad](./search-your-custom-view.md)
- [Dela sökningen anpassad](./share-your-custom-search.md)
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Markera text med hjälp av decoration markörer](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
