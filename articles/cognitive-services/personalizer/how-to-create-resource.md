---
title: Skapa en personanpassa resurs
description: Tjänst konfigurationen omfattar hur tjänsten behandlar förmåner, hur ofta tjänsten utforskar, hur ofta modellen omtränas och hur mycket data som lagras.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624235"
---
# <a name="create-a-personalizer-resource"></a>Skapa en personanpassa resurs

En personanpassa resurs är samma sak som en personanpassa inlärnings slinga. En enskild resurs eller inlärnings slinga skapas för varje ämnes domän eller innehålls område som du har. Använd inte flera innehålls områden i samma slinga eftersom detta förvirrar inlärnings slingan och ger dåliga förutsägelser.

Om du vill att Personanpassaren ska välja det bästa innehållet för mer än ett innehålls områden på en webb sida använder du en annan inlärnings slinga för var och en.


## <a name="create-a-resource-in-the-azure-portal"></a>Skapa en resurs i Azure Portal

Skapa en personanpassa resurs för varje feedback-slinga.

1. Logga in på [Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Föregående länk tar dig till sidan **skapa** för tjänsten personanpassa.
1. Ange tjänstens namn, Välj en prenumeration, plats, pris nivå och resurs grupp.

    > [!div class="mx-imgBorder"]
    > ![använda Azure Portal för att skapa en personanpassa resurs, även kallat en inlärnings slinga.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Välj **skapa** för att skapa resursen.

1. När resursen har distribuerats väljer du knappen **gå till resurs** för att gå till din personanpassa resurs. Gå till **konfigurations** sidan för den nya resursen för att [Konfigurera inlärnings slingan](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Skapa en resurs med Azure CLI

1. Logga in på Azure CLI med följande kommando:

    ```bash
    az login
    ```

1. Skapa en resurs grupp, en logisk gruppering för att hantera alla Azure-resurser som du tänker använda med personanpassa resursen.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Skapa en ny personanpassa resurs, _inlärnings slinga_med följande kommando för en befintlig resurs grupp.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Nästa steg

* [Konfigurera](how-to-settings.md) Inlärnings slinga för personanpassa