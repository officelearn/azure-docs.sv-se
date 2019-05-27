---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: 02ef0d6c7c8ddc7088938d9c8ea379e3b97f3045
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158727"
---
1. [Skapa en arbetsyta för Azure Machine Learning-tjänsten](../articles/machine-learning/service/setup-create-workspace.md).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurationsfil för arbetsytan till den klonade katalogen med någon av följande metoder:

    * I den [Azure-portalen](https://ms.portal.azure.com)väljer **hämta config.json** från den **översikt** avsnitt i din arbetsyta. 

    ![Ladda ned config.json](./media/aml-dsvm-server/download-config.png)

    * Skapa en ny arbetsyta med hjälp av koden i anteckningsboken [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) i den klonade katalogen.


1. Starta notebook-servern från den klonade katalogen.
    
    ```shell
    jupyter notebook
    ```