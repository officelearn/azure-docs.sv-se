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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021755"
---
1. Följ instruktionerna på [skapa en arbetsyta för Azure Machine Learning-tjänsten](../articles/machine-learning/service/setup-create-workspace.md#portal) till:
    * Skapa en Miniconda-miljö
    * Installera Azure Machine Learning SDK för Python
    * Skapa en arbetsyta
    * Skriv en konfigurationsfil för arbetsytan (**aml_config/config.json**).
    
1. Klona [github-lagringsplatsen](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Lägg till en konfigurationsfil för arbetsytan med hjälp av någon av följande metoder:
    * Kopiera den **aml_config/config.json** fil som du skapade i steg 1 i den klonade katalogen.

    * I den [Azure-portalen](https://ms.portal.azure.com)väljer **hämta config.json** från den **översikt** avsnitt i din arbetsyta. 

    ![Ladda ned config.json](./media/aml-dsvm-server/download-config.png)

    * Skapa en ny arbetsyta med hjälp av kod i [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Starta notebook-servern från den klonade katalogen.
    
    ```shell
    jupyter notebook
    ```