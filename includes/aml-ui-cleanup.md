---
title: ta med fil
description: ta med fil
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028597"
---
>[!IMPORTANT]
>Du kan använda de resurser som du skapade bland kraven för andra Azure Machine Learning-tjänsten självstudier och instruktionsartiklar.


### <a name="delete-everything"></a>Tar bort allt

Om du inte planerar att använda något som du skapade, tar du bort hela resursgruppen så att du inte betala något:

1. I Azure-portalen väljer du **resursgrupper** på vänster sida av fönstret.
 
   ![Ta bort resursgrupp i Azure-portalen](./media/aml-ui-cleanup/delete-resources.png)

1. Välj den resursgrupp som du skapade i listan.

1. På höger sida av fönstret, väljer du knappen med tre punkter (**...** ).

1. Välj **Ta bort resursgrupp**.

Ta bort resursgruppen tar även bort alla resurser som du skapade i det visuella gränssnittet.  

### <a name="delete-only-the-compute-target"></a>Ta bort bara beräkningsmål

Beräkningsmål som du skapade här *automatiskt skalar* till noll noder när den inte används. Detta är att minimera kostnader. Om du vill ta bort beräkningsmål kan du göra följande:

1. I den [Azure-portalen](https://portal.azure.com), öppna din arbetsyta.

    ![Ta bort beräkningsmål](./media/aml-ui-cleanup/delete-compute-target.png)

1. I den **Compute** avsnittet av arbetsytan och välj resursen.

1. Välj **Ta bort**.

### <a name="delete-individual-assets"></a>Ta bort enskilda tillgångar

I det visuella gränssnittet där du skapade ditt experiment, ta bort enskilda tillgångar genom att markera dem och sedan välja den **ta bort** knappen.

![Ta bort experiment](./media/aml-ui-cleanup/delete-experiment.png)
