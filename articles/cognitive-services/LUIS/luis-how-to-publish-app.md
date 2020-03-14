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
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 676c6d15c4f439543a3ed74627001725632fecfa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220890"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicera din aktiva, utbildade app till en mellanlagrings-eller produktions slut punkt

När du har skapat, tränat och testat din aktiva LUIS-app, gör du den tillgänglig för klient programmet genom att publicera den till slut punkten. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicera

1. Om du vill publicera till slut punkten väljer du **publicera** i den övre högra panelen. 

    ![Knappen publicera överst, höger navigerings fält](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Välj inställningar för den publicerade förutsägelse slut punkten och välj sedan **publicera**.

    ![Välj publicerings inställningar och sedan knappen publicera](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicerings platser

Välj rätt plats när popup-fönstret visas: 

* Mellanlagring
* Produktion 

Genom att använda både publicerings platser kan du välja att ha två olika versioner av din app tillgängliga på de publicerade slut punkterna eller samma version på två olika slut punkter. 

### <a name="publishing-regions"></a>Publicera regioner

Appen publiceras till alla regioner som är kopplade till resurserna för LUIS förutsägelse slut punkt som lagts till i LUIS-portalen från sidan **hantera** ->  **[Azure-resurser](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** . 

Till exempel, för en app som skapas på [www.Luis.AI](https://www.luis.ai), om du skapar en Luis-resurs i två regioner, **väster** och **östra**, och lägger till dem i appen som resurser, publiceras appen i båda regionerna. Mer information om LUIS-regioner finns i [regioner](luis-reference-regions.md).

> [!TIP]
> Det finns 3 redigerings regioner. Du måste redigera i den region som du ska publicera till. Om du behöver publicera i alla regioner måste du hantera redigerings processen och den resulterande tränade modellen i alla tre redigerings regionerna. 


## <a name="configuring-publish-settings"></a>Konfigurera inställningar för publicering

När du har valt plats, konfigurerar du publicerings inställningarna för:

* Attitydanalys
* Stavnings korrigering – v2 endast förutsägelse slut punkt
* Tal Prima 

När du har publicerat är de här inställningarna tillgängliga för granskning på sidan hantera **publicerings inställningar** i avsnittet **Hantera** . Du kan ändra inställningarna med varje publicering. Om du avbryter en publicering avbryts även eventuella ändringar som du har gjort under publiceringen. 

### <a name="when-your-app-is-published"></a>När din app publiceras

När din app har publicerats visas ett meddelande längst upp i webbläsaren. Meddelandet innehåller också en länk till slut punkterna. 

Välj länken om du behöver slutpunkts-URL. Du kan också gå till URL-adresserna för slut punkter genom att välja **Hantera** i den översta menyn och sedan välja **Azure-resurser** på den vänstra menyn. 

## <a name="sentiment-analysis"></a>Attitydanalys

<a name="enable-sentiment-analysis"></a>

Sentiment-analys gör det möjligt för LUIS att integrera med [textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/) för att tillhandahålla sentiment och nyckel fras analys. 

Du behöver inte ange en nyckel för textanalys och det är kostnadsfritt faktureringen för den här tjänsten på Azure-kontot. 

Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data. Sentiment-etiketten för `positive`, `neutral`och `negative` är per kultur som stöds. För närvarande stöder endast engelska sentiment-etiketter. 

Mer information om JSON-slutpunktens svar med sentiment-analys finns i [sentiment-analys](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Stavnings korrigering

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Korrigeringar av stavning görs före LUIS User uttryck förutsägelse. Du kan se ändringar i den ursprungliga uttryck, inklusive stavning, i svaret.

## <a name="speech-priming"></a>Tal Prima

Tal Prima är den process som används för att skicka LUIS-modellen till tal tjänster före konvertering av text till tal. Detta gör att röst tjänsten ger tal konverteringen mer exakt för din modell. Detta tillåter robot tal-och LUIS-begäranden och svar i ett anrop genom att göra ett tal samtal och få tillbaka ett LUIS-svar. Det ger mindre latens.

## <a name="next-steps"></a>Nästa steg

* Se [Hantera nycklar](./luis-how-to-azure-subscription.md) för att lägga till nycklar till Azure-prenumerationen i Luis och hur du ställer in stavningskontroll i Bing nyckel och inkluderar alla avsikter i resultat.
* Se [träna och testa din app](luis-interactive-test.md) för instruktioner om hur du testar din publicerade app i test konsolen.

