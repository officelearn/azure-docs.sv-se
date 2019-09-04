---
title: Publicera app – LUIS
titleSuffix: Azure Cognitive Services
description: När du är klar att skapa och testa active LUIS-appen blir tillgänglig för klientprogrammet genom att publicera den till slutpunkten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257790"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicera din aktiva, utbildade app till en mellanlagrings-eller produktions slut punkt

När du är klar att skapa och testa active LUIS-appen blir tillgänglig för klientprogrammet genom att publicera den till slutpunkten. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publicering

1. Om du vill publicera till slutpunkten, Välj **publicera** i övre högra panelen. 

    ![Knappen publicera överst, höger navigerings fält](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Välj inställningar för den publicerade förutsägelse slut punkten och välj sedan **publicera**.

    ![Välj publicerings inställningar och sedan knappen publicera](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicerings platser

Välj rätt plats när popup-fönstret visas: 

* Mellanlagringsområdet.
* Produktions. 

Genom att använda både publicerings platser kan du välja att ha två olika versioner av din app tillgängliga på de publicerade slut punkterna eller samma version på två olika slut punkter. 

### <a name="publishing-regions"></a>Publicera regioner

Appen publiceras till alla regioner som är kopplade till resurserna för LUIS förutsägelse slut punkt som lagts till i LUIS-portalen. 

Till exempel, för en app som skapas på [www.Luis.AI](https://www.luis.ai), om du skapar en Luis-resurs i två regioner, **väster** och **östra**, och lägger till dem i appen som resurser, publiceras appen i båda regionerna. Mer information om LUIS regioner finns i [regioner](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Konfigurera inställningar för publicering

När du har valt plats, konfigurerar du publicerings inställningarna för:

* Sentimentanalys
* Stavnings korrigering
* Tal Prima 

När du har publicerat är de här inställningarna tillgängliga för granskning på sidan hantera **publicerings inställningar** i avsnittet **Hantera** . Du kan ändra inställningarna med varje publicering. Om du avbryter en publicering avbryts även eventuella ändringar som du har gjort under publiceringen. 

### <a name="when-your-app-is-published"></a>När din app publiceras

När din app har publicerats visas ett meddelande om grönt överst i webbläsaren. Grön meddelandefältet innehåller också en länk till slutpunkterna. 

![Publicera popup-fönster med länk till slut punkt](./media/luis-how-to-publish-app/publish-success.png)

Välj länken om du behöver slutpunkts-URL. Du kan också gå till URL-adresserna för slut punkter genom att välja **Hantera** i den översta menyn och sedan välja **Azure-resurser** på den vänstra menyn. 

## <a name="sentiment-analysis"></a>Sentimentanalys

<a name="enable-sentiment-analysis"></a>

Attitydanalys kan LUIS för att integrera med [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) att tillhandahålla känsla och nyckeln frasen analyser. 

Du behöver inte ange en nyckel för textanalys och det är kostnadsfritt faktureringen för den här tjänsten på Azure-kontot. 

Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data. Sentiment-etiketten för `positive`, `neutral`, och `negative` är per kultur som stöds. För närvarande stöder endast engelska sentiment-etiketter. 

Läs mer om JSON-svar för slutpunkt med attitydanalys [attitydanalys](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Stavnings korrigering

Korrigeringar av stavning görs före LUIS User uttryck förutsägelse. Du kan se ändringar i den ursprungliga uttryck, inklusive stavning, i svaret.

## <a name="speech-priming"></a>Tal Prima

Tal Prima är den process som används för att skicka LUIS-modellen till tal tjänster före konvertering av text till tal. Detta gör att röst tjänsten ger tal konverteringen mer exakt för din modell. Detta tillåter robot tal-och LUIS-begäranden och svar i ett anrop genom att göra ett tal samtal och få tillbaka ett LUIS-svar. Det ger mindre latens.

## <a name="next-steps"></a>Nästa steg

* Se [hantera nycklar](./luis-how-to-azure-subscription.md) nyckel till LUIS och hur du ställer in Bing-stavningskontroll nyckel att lägga till nycklar till Azure-prenumeration och inkluderas alla avsikter i resultaten.
* Se [träna och testa din app](luis-interactive-test.md) anvisningar om hur du testar din publicerade app i test-konsolen.

