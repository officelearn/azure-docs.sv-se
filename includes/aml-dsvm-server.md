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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486089"
---
1. [Skapa en Azure Machine Learning-arbetsyta](../articles/machine-learning/how-to-manage-workspace.md).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurationsfil för arbetsytan i den klonade katalogen med någon av följande metoder:

    * I [Azure-portalen](https://ms.portal.azure.com)väljer du **Hämta config.json** från avsnittet **Översikt** på arbetsytan. 

    ![Ladda ner config.json](./media/aml-dsvm-server/download-config.png)

    * Skapa en ny arbetsyta med hjälp av koden i anteckningsboken [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) i den klonade katalogen.

1. Starta notebook-servern från den klonade katalogen.

    ```bash
    jupyter notebook
    ```