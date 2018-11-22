---
title: 'Snabbstart: Skapa en första instans för anpassad sökning i Bing'
titlesuffix: Azure Cognitive Services
description: Om du vill använda anpassad sökning i Bing måste du skapa en anpassad sökinstans som definierar vyn eller sektorn på webben. Instansen innehåller inställningar som anger de offentliga domänerna och underwebbplatserna som du vill att Bing ska söka i, och eventuella justeringar för rangordning.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: aahi
ms.openlocfilehash: c9b37486d664920bbc4b85a0715ce7f5ea910365
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161557"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Snabbstart: Skapa din första instans för anpassad sökning i Bing
Om du vill använda anpassad sökning i Bing måste du skapa en anpassad sökinstans som definierar vyn eller sektorn på webben. Instansen innehåller inställningar som anger de offentliga domänerna och webbplatserna som du vill att Bing ska söka i, och eventuella justeringar för rangordning. Använd [portalen](https://customsearch.ai) för anpassad sökning i Bing för att skapa instansen. 

## <a name="create-a-custom-search-instance"></a>Skapa en anpassad sökinstans

Så här skapar du en instans för anpassad sökning i Bing:

1.  Hämta en nyckel för API för anpassad sökning. Gå till [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Klicka på knappen **Logga in** och logga in på portalen med ett Microsoft-konto (MSA). 
    - Om du inte har något MSA klickar du på **Skapa ett Microsoft-konto**. Portalen begär behörighet att komma åt dina data. Klicka på **Ja**.
    - Samtyck till villkoren för Cognitive Services. Markera **Jag accepterar** och klicka på **Acceptera**.  
3.  När du har loggat in klickar du på **Ny instans** och namnger instansen. Använd ett beskrivande namn som beskriver typen av innehåll som sökningen returnerar. Du kan ändra namnet när som helst. 
4.  På sidan **Sökupplevelse** klickar du på fliken **Aktiv** och anger webbadressen till en eller flera webbplatser som du vill inkludera i sökningen.
5.  Ange en fråga för att bekräfta att din instans returnerar resultat i förhandsgranskningsfönstret till höger. Om det inte finns några resultat kan du ange en ny webbplats. Bing returnerar endast resultat för offentliga webbplatser som den har indexerat.
6.  Klicka på **Publicera** för att publicera ändringar i konfigurationen för produktion. När du uppmanas till detta klickar du på **Publicera** för att bekräfta.
7.  Klicka på **Produktion** > **Slutpunkter** och kopiera **ID:t för anpassad konfiguration**. Du behöver detta ID för att anropa API:et för anpassad Bing-sökning.

## <a name="next-steps"></a>Nästa steg

Fortsätt att arbeta med den anpassade sökinstans som du nyss skapat genom att följa instruktionerna i de här instruktionerna:

- [Konfigurera din anpassade sökupplevelse](./define-your-custom-view.md)
- [Anropa din anpassade sökning](./search-your-custom-view.md)
- [Dela din anpassade sökning](./share-your-custom-search.md)
- [Konfigurera värdbaserad UI-upplevelse](./hosted-ui.md)
- [Använda dekorationsmarkörer för att markera text](./hit-highlighting.md)
- [Webbsidor för sida](./page-webpages.md)
