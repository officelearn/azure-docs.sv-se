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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391807"
---
1. [Skapa en arbetsyta för Azure Machine Learning-tjänsten](../articles/machine-learning/service/setup-create-workspace.md).

1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```CLI
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