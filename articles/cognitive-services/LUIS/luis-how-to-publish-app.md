---
title: Publicera app - LUIS
titleSuffix: Azure Cognitive Services
description: När du är klar med att skapa och testa din aktiva LUIS-app gör du den tillgänglig för klientprogrammet genom att publicera den till slutpunkten.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053437"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicera din aktiva, tränade app till en mellanlagrings- eller produktionsslutpunkt

När du är klar med att skapa, träna och testa din aktiva LUIS-app gör du den tillgänglig för klientprogrammet genom att publicera den till slutpunkten. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicera

1. Om du vill publicera till slutpunkten väljer du **Publicera** i den övre högra panelen. 

    ![Knappen Publicera i övre, högra navigeringsfältet](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Välj dina inställningar för den publicerade slutpunkten för förutsägelse och välj sedan **Publicera**.

    ![Välj publiceringsinställningar och välj sedan knappen Publicera](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publicering av ankomst- och avgångstider

Välj rätt plats när popup-fönstret visas: 

* Mellanlagring
* Produktion 

Genom att använda båda publiceringsplatserna kan du ha två olika versioner av appen tillgängliga vid de publicerade slutpunkterna eller samma version på två olika slutpunkter. 

### <a name="publishing-regions"></a>Publiceringsregioner

Appen publiceras i alla regioner som är associerade med slutpunktsresurserna LUIS-förutsägelse som lagts till i LUIS-portalen från sidan **Hantera** -> **[Azure-resurser.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

För en app som skapats på [www.luis.ai](https://www.luis.ai), om du skapar en LUIS-resurs i två regioner, **westus** och **eastus**och lägger till dessa i appen som resurser, publiceras appen i båda regionerna. Mer information om LUIS-regioner finns i [Regioner](luis-reference-regions.md).

> [!TIP]
> Det finns 3 författarregioner. Du måste skapa i den region som du tänker publicera till. Om du behöver publicera i alla regioner måste du hantera redigeringsprocessen och den resulterande tränade modellen i alla tre redigeringsregioner. 


## <a name="configuring-publish-settings"></a>Konfigurera publiceringsinställningar

När du har valt plats konfigurerar du publiceringsinställningarna för:

* Sentimentanalys
* Stavningskorrigering - endast v2-förutsägelseslutpunkt
* Tal priming 

När du har publicerat kan dessa inställningar granskas från sidan **Hantera** **inställningar.** Du kan ändra inställningarna för varje publicering. Om du avbryter en publicering avbryts även alla ändringar som du gjorde under publiceringen. 

### <a name="when-your-app-is-published"></a>När appen publiceras

När appen har publicerats visas ett meddelande om lyckade uppgifter högst upp i webbläsaren. Meddelandet innehåller också en länk till slutpunkterna. 

Om du behöver slutpunkts-URL:en väljer du länken. Du kan också komma åt slutpunktsadresserna genom att välja **Hantera** i den övre menyn och sedan välja **Azure-resurser** på den vänstra menyn. 

## <a name="sentiment-analysis"></a>Sentimentanalys

<a name="enable-sentiment-analysis"></a>

Sentimentanalys gör det möjligt för LUIS att integrera med [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) för att ge sentiment och nyckelfrasanalys. 

Du behöver inte ange en Text Analytics-nyckel och det finns ingen faktureringsavgift för den här tjänsten till ditt Azure-konto. 

Sentimentdata är en poäng mellan 1 och 0 som anger den positiva (närmare 1) eller negativa (närmare 0) sentimentet för data. Sentimentetiketten `neutral`för `negative` `positive`, och är per kultur som stöds. För närvarande stöder endast engelska sentimentetiketter. 

Mer information om JSON-slutpunktssvaret med sentimentanalys finns i [Sentimentanalys](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Stavningskorrigering

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Stavningskorrigeringar görs innan FÖRUTSÄGELSEN OM LUIS-användares uttryck. Du kan se eventuella ändringar i det ursprungliga uttrycket, inklusive stavning, i svaret.

## <a name="speech-priming"></a>Tal priming

Talpriming är processen att använda skicka LUIS-modellen till Tal tjänster före konvertering av text till tal. På så sätt kan taltjänsten ge talkonvertering mer exakt för din modell. Detta gör bot Tal och LUIS förfrågningar och svar i ett samtal genom att göra ett tal samtal och få tillbaka en LUIS svar. Det ger mindre latens totalt sett.

## <a name="next-steps"></a>Nästa steg

* Se [Hantera nycklar](./luis-how-to-azure-subscription.md) för att lägga till nycklar till Azure-prenumerationsnyckeln i LUIS och hur du ställer in Bing-stavningskontroll-tangenten och inkluderar alla avsikter i resultaten.
* Se [Träna och testa appen](luis-interactive-test.md) för instruktioner om hur du testar din publicerade app i testkonsolen.

