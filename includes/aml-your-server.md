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
ms.openlocfilehash: e11b1c8c19db73ace069d4bfda3516cdbbceaa02
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476087"
---
1. Följ anvisningarna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att installera Azure Machine Learning SDK för python

1. Skapa en [Azure Machine Learning-arbetsyta](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Skriv en [konfigurations fil](../articles/machine-learning/service/how-to-configure-environment.md#workspace) fil (**aml_config/config. JSON**).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```
