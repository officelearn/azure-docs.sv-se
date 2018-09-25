---
title: Skapa en första Bing Custom Search-instans – Microsoft Cognitive Services
description: Om du vill använda Bing Custom Search, måste du skapa en anpassad Sökinstans som definierar vyn eller sektorn på webben. Instansen innehåller inställningar som anger den offentliga domäner och underplatser webbsidor som du vill att Bing för att söka och justeringar rangordning.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 25d622772fe47ffad001834d476e612f8c606904
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981680"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Skapa din första Bing Custom Search-instans
Om du vill använda Bing Custom Search, måste du skapa en anpassad Sökinstans som definierar vyn eller sektorn på webben. Instansen innehåller inställningar som anger den offentliga domäner, webbplatser och webbsidor som du vill att Bing för att söka och justeringar rangordning. Använd Bing Custom Search för att skapa instansen [portal](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad Sökinstans

Skapa en anpassad sökning i Bing-instans:

1.  Hämta en nyckel för API för anpassad sökning. Se [testa kognitiva tjänster](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Klicka på den **logga in** knappen och logga in på portalen med ett Microsoft-konto (MSA). 
    - Om du inte har en MSA, klickar du på **skapa ett Microsoft-konto**. Portalen begär behörighet att komma åt dina data. Klicka på **Ja**.
    - Samtycker till villkoren för Cognitive Services. Kontrollera **jag godkänner** och klicka på **accepterar**.  
3.  När du har loggat in klickar du på **ny instans** och namnge instansen. Använd ett namn som betyder något och beskriver typ av innehåll som returnerar sökningen. Du kan ändra namnet när som helst. 
4.  På den **Active** fliken **Sökupplevelse**, ange Webbadressen till en eller flera webbplatser som du vill ska ingå i sökningen.
5.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Om det finns inga resultat kan du ange en ny webbplats. Bing returnerar resultat endast för offentliga webbplatser som den har indexerats.
6.  Klicka på **publicera** att publicera ändringar i konfigurationen för produktion. När du uppmanas till detta klickar du på **publicera** att bekräfta.
7.  Klicka på **produktion** > **slutpunkter** och kopiera den **anpassad konfigurations-ID**. Du behöver detta ID för att anropa API: et för anpassad sökning.

## <a name="next-steps"></a>Nästa steg

Fortsätt att arbeta med anpassad Sökinstans som du nyss skapat genom att följa instruktionerna i dessa instruktionsguider:

- [Konfigurera din upplevelse för anpassad sökning](./define-your-custom-view.md)
- [Anropa dina anpassad sökning](./search-your-custom-view.md)
- [Dela din anpassade sökning](./share-your-custom-search.md)
- [Konfigurera din värdbaserade användargränssnitt](./hosted-ui.md)
- [Använda decoration markörer för att markera text](./hit-highlighting.md)
- [Sidan webbsidor](./page-webpages.md)
