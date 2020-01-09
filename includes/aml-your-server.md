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
ms.date: 09/26/2019
ms.openlocfilehash: 1260ee32c267a2652ad02e74c3978654bf2f9669
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529802"
---
1. Följ anvisningarna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att installera Azure Machine Learning SDK för python

1. Skapa en [Azure Machine Learning-arbetsyta](../articles/machine-learning/how-to-manage-workspace.md).

1. Skriv en [konfigurations fil](../articles/machine-learning/how-to-configure-environment.md#workspace) fil (**aml_config/config.JSON**).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```
