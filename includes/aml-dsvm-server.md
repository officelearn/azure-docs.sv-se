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
ms.openlocfilehash: 6bddfa048e4cb9cb7728c236417d4672df6f25b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476091"
---
1. [Skapa en Azure Machine Learning-arbetsyta](../articles/machine-learning/service/how-to-manage-workspace.md).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurations fil för arbets ytor i den klonade katalogen med någon av följande metoder:

    * I [Azure Portal](https://ms.portal.azure.com)väljer du **Hämta config. JSON** från **översikts** avsnittet på arbets ytan. 

    ![Ladda ned config. JSON](./media/aml-dsvm-server/download-config.png)

    * Skapa en ny arbetsyta med hjälp av koden i anteckningsboken [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) i den klonade katalogen.

1. Starta notebook-servern från den klonade katalogen.

    ```shell
    jupyter notebook
    ```