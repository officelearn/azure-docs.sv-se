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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391800"
---
1. Följ instruktionerna på [skapa en arbetsyta för Azure Machine Learning-tjänsten](../articles/machine-learning/service/setup-create-workspace.md#portal) att göra följande:
    * Skapa en Miniconda-miljö
    * Installera Azure Machine Learning SDK för Python
    * Skapa en arbetsyta
    * Skriv en konfigurationsfil för arbetsytan (**aml_config/config.json**).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```