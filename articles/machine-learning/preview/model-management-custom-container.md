---
title: Anpassa den behållare bild som används för att distribuera Azure ML-modell | Microsoft Docs
description: Den här artikeln beskriver hur du anpassar en behållare avbildning för Azure Machine Learning-modeller
services: machine-learning
author: tedway
ms.author: tedway, raymondl
manager: mwinkle
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ms.openlocfilehash: 858c8933565aeeb22dc1b685082dab2c6481737b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Anpassa den behållare bild som används för Azure ML-modeller

Den här artikeln beskriver hur du anpassar en behållare avbildning för Azure Machine Learning-modeller.  Azure ML-arbetsstationen använder behållare för distribution av machine learning-modeller. Modeller distribueras tillsammans med deras beroenden och Azure ML bygger en bild från modellen, beroenden och associerade filer.

## <a name="how-to-customize-the-docker-image"></a>Hur du anpassar Docker-bild
Anpassa Docker-avbildningen som Azure ML distribuerar med:

1. En depenencies.yml-fil: hantera beroenden som kan installeras från [PyPi]( https://pypi.python.org/pypi), du kan använda filen conda_dependencies.yml från arbetsstationen projektet eller skapa egna. Det här är en rekommenderad metod för att installera Python-beroenden som kan installeras med pip.

   Exempel CLI-kommando:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> -c amlconfig\conda_dependencies.yml
   ```

   Conda_dependencies-exempelfil: 
   ```yaml
   name: project_environment
   dependencies:
      - python=3.5.2
      - scikit-learn
      - ipykernel=4.6.1
      
      - pip:
        - azure-ml-api-sdk==0.1.0a11
        - matplotlib
   ```
        
2. En Docker steg fil: med det här alternativet kan du anpassa den distribuerade avbildningen genom att installera beroenden som inte kan installeras från PyPi. 

   Filen bör innehålla Docker installationssteg som en DockerFile. Följande kommandon är tillåtna i filen: 

    RUN, ENV, ARG, LABEL, EXPOSE

   Exempel CLI-kommando:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Bild, manifestet och tjänsten kommandon accepterar flaggan docker-fil.

   Docker steg exempelfil:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Basavbildningen för Azure ML-behållare är Ubuntu och kan inte ändras. Om du anger en annan basavbildning, kommer att ignoreras.

## <a name="next-steps"></a>Nästa steg
Nu när du har anpassat behållaren avbildningen måste distribuera du den till ett kluster för storskalig användning.  Mer information om hur du skapar ett kluster för web service-distributionen finns [modellen Management Configuration](deployment-setup-configuration.md). 
