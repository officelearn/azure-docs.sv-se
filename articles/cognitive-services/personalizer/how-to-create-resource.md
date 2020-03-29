---
title: Skapa Personalizer-resurs
description: Tjänstkonfigurationen innehåller hur tjänsten behandlar belöningar, hur ofta tjänsten utforskar, hur ofta modellen tränas om och hur mycket data som lagras.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336063"
---
# <a name="create-a-personalizer-resource"></a>Skapa en Personalizer-resurs

En Personalizer-resurs är samma sak som en Personalizer-inlärningsslinga. En enda resurs, eller utbildningsloop, skapas för varje ämnesdomän eller innehållsområde som du har. Använd inte flera innehållsområden i samma slinga eftersom detta kommer att förvirra inlärningsloopen och ge dåliga förutsägelser.

Om du vill att Personalizer ska välja det bästa innehållet för mer än ett innehållsområde på en webbsida använder du en annan utbildningsloop för varje.


## <a name="create-a-resource-in-the-azure-portal"></a>Skapa en resurs i Azure-portalen

Skapa en Personalizer-resurs för varje återkopplingsloop.

1. Logga in på [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Den föregående länken tar dig till sidan **Skapa** för personalizer-tjänsten.
1. Ange ditt tjänstnamn, välj en prenumeration, plats, prisnivå och resursgrupp.

    > [!div class="mx-imgBorder"]
    > ![Använd Azure-portalen för att skapa Personalizer-resurs, även kallad utbildningsloop.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Välj **Skapa** om du vill skapa resursen.

1. När resursen har distribuerats väljer du knappen **Gå till resurs** för att gå till personalizer-resursen.

1. Välj **snabbstartssidan** för resursen och kopiera sedan värdena för slutpunkten och nyckeln. Du behöver både resursslutpunkten och nyckeln för att använda rank- och belönings-API:erna.

1. Välj **sidan Konfiguration** för den nya resursen för att [konfigurera utbildningsloopen](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Skapa en resurs med Azure CLI

1. Logga in på Azure CLI med följande kommando:

    ```azurecli-interactive
    az login
    ```

1. Skapa en resursgrupp, en logisk gruppering för att hantera alla Azure-resurser som du tänker använda med Personalizer-resursen.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Skapa en ny Personalizer-resurs, _utbildningsloop_, med följande kommando för en befintlig resursgrupp.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Detta returnerar ett JSON-objekt, som innehåller **resursslutpunkten**.

1. Använd följande Azure CLI-kommando för att hämta **din resursnyckel**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Du behöver både resursslutpunkten och nyckeln för att använda rank- och belönings-API:erna.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera](how-to-settings.md) Personalizer lärande loop