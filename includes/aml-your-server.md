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
ms.openlocfilehash: 6c93d1243db1b3c4277a54cf71e10f6bbc648d26
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846019"
---
- Azure Machine Learning SDK för python har installerats. Följ anvisningarna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att göra följande:


1. Följ anvisningarna på [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) för att göra följande:
    * Skapa en Miniconda-miljö [skapa och hantera Azure Machine Learning service-arbetsytor]
    * Installera Azure Machine Learning SDK för python

1. Skapa en [Azure Machine Learning service-arbetsyta](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Skriv en [konfigurations fil](../articles/machine-learning/service/how-to-configure-environment.md#workspace) fil (**aml_config/config. JSON**).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```