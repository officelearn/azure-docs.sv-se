---
title: Skapa LUIS-resurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80879228"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Skapa LUIS-resurser i Azure Portal

1. Använd [den här länken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) för att börja skapa Luis-resurser i Azure Portal.

1. Ange alla nödvändiga inställningar:

    |Namn|Syfte|
    |--|--|
    |Prenumerationens namn| den prenumeration som ska faktureras för resursen.|
    |Resursgrupp| Ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering.|
    |Namn| Ett anpassat namn som du väljer, används som anpassad under domän för dina redigerings-och förutsägelse slut punkts frågor.|
    |Redigerings plats|Den region som är associerad med din modell.|
    |Redigera pris nivå|Pris nivån avgör den högsta transaktionen per sekund och månad.|
    |Körnings plats|Den region som är kopplad till den publicerade slut punkts körningen för förutsägelse.|
    |Pris nivå för körning|Pris nivån avgör den högsta transaktionen per sekund och månad.|

    > [!div class="mx-imgBorder"]
    > [![Skapa en språk förståelse resurs](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Klicka på **Granska + skapa** och vänta tills resursen har skapats.
1. När båda resurserna har skapats väljer du den nya redigerings resursen i Azure Portal och sedan **snabb starter** för att hämta **slut punkts-URL** och **nyckel** för redigering av program mässigt.

> [!TIP]
> Om du vill använda resurserna går du till LUIS-portalen och [tilldelar resurserna](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).