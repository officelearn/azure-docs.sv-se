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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395669"
---
1. Följ instruktionerna på [skapa en arbetsyta för Azure Machine Learning-tjänsten](../articles/machine-learning/service/setup-create-workspace.md#portal) för att skapa en Miniconda miljö, skapa en arbetsyta och skriva en konfigurationsfil för arbetsytan (**aml_config/config.json**) .

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurationsfil för arbetsytan med hjälp av någon av följande metoder:
    * Kopiera den **aml_config/config.json** fil som du skapade med hjälp av de nödvändiga snabbstarten i den klonade katalogen.
    * Skapa en ny arbetsyta med hjälp av kod i [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Starta notebook-servern från den klonade katalogen.
    
    ```shell
    jupyter notebook
    ```