---
title: Skapa LUIS-resurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: 88289686e5b091ef3ec309ee9b54ee0f895c8c22
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754361"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Skapa LUIS-resurser i Azure-portalen

1. Använd [den här länken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) för att börja skapa LUIS-resurser i Azure-portalen.

1. Ange alla nödvändiga inställningar:

    |Namn|Syfte|
    |--|--|
    |Prenumerationens namn| prenumerationen som faktureras för resursen.|
    |Resursgrupp| Ett anpassat resursgruppsnamn som du väljer eller skapar. Med resursgrupper kan du gruppera Azure-resurser för åtkomst och hantering.|
    |Namn| Ett eget namn som du väljer, som används som din anpassade underdomän för dina redigerings- och förutsägelseslutpunktsfrågor.|
    |Skapa plats|Den region som är associerad med din modell.|
    |Prisnivå för redigering|Prisnivån bestämmer den maximala transaktionen per sekund och månad.|
    |Körtidsplats|Den region som är associerad med din publicerade slutpunktskörning för förutsägelse.|
    |Prisnivå för körning|Prisnivån bestämmer den maximala transaktionen per sekund och månad.|

    > [!div class="mx-imgBorder"]
    > [![Skapa språköverenskommelsen resurs](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klicka på **Granska + skapa** och vänta tills resursen har skapats.
1. När båda resurserna har skapats, fortfarande i Azure-portalen, väljer du den nya redigeringsresursen och sedan **Snabbstartar** för att hämta **författarslutpunkts-URL:en** och **nyckeln** för redigering programmässigt.

> [!TIP]
> Om du vill använda resurserna [tilldelar du resurserna](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)i LUIS-portalen .