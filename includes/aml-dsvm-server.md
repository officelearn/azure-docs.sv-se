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
ms.date: 03/05/2020
ms.openlocfilehash: 2504245a14eee5308e046148f8073cf34ffa8143
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79486089"
---
1. [Skapa en Azure Machine Learning-arbetsyta](../articles/machine-learning/how-to-manage-workspace.md).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurations fil för arbets ytor i den klonade katalogen med någon av följande metoder:

    * I [Azure Portal](https://ms.portal.azure.com)väljer du **Hämta config. JSON** från **översikts** avsnittet på arbets ytan. 

    ![Ladda ned config. JSON](./media/aml-dsvm-server/download-config.png)

    * Skapa en ny arbetsyta med hjälp av koden i anteckningsboken [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) i den klonade katalogen.

1. Starta notebook-servern från den klonade katalogen.

    ```bash
    jupyter notebook
    ```