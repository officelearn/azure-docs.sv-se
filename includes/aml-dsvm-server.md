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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302201"
---
1. Slutför [snabbstarten för Azure Machine Learning med Python](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) för att skapa en arbetsyta.  Hoppa över avsnittet **Använda anteckningsboken** om du vill.
1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Lägg till en konfigurationsfil för arbetsytan med hjälp av någon av följande metoder:
    * Kopiera filen **aml_config\config.json** som du skapade med hjälp av den nödvändiga snabbstarten till den klonade katalogen.
    * Skapa en ny arbetsyta med hjälp av koden i anteckningsboken [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) i den klonade katalogen.
1. Starta notebook-servern från den klonade katalogen.
    
    ```shell
    jupyter notebook
    ```