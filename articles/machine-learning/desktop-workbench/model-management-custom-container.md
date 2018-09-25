---
title: Anpassa den Behållaravbildning som används för att distribuera Azure ML-modeller | Microsoft Docs
description: Den här artikeln beskriver hur du anpassar en behållaravbildning för Azure Machine Learning-modeller
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: mldocs, raymondl
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 3/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: d4cfb7067510ec06df3319035dee5e2195cb2f9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46997547"
---
# <a name="customize-the-container-image-used-for-azure-ml-models"></a>Anpassa den behållaravbildning som används för Azure ML-modeller

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Den här artikeln beskriver hur du anpassar en behållaravbildning för Azure Machine Learning-modeller.  Azure ML Workbench använder behållare för att distribuera machine learning-modeller. Modeller som distribueras tillsammans med deras beroenden och Azure ML bygger en bild från modellen, beroenden och tillhörande filer.

## <a name="how-to-customize-the-docker-image"></a>Hur du anpassar Docker-avbildningen
Anpassa Azure ML distribuerar med hjälp av Docker-avbildningen:

1. En `dependencies.yml` fil: att hantera beroenden som kan installeras från [PyPi]( https://pypi.python.org/pypi), du kan använda den `conda_dependencies.yml` filen från Workbench-projekt, eller skapa egna. Detta är det rekommenderas att-metoden för att installera Python-beroenden som är pip-installation.

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
        
2. Docker steg fil: med det här alternativet kan du anpassa den distribuerade avbildningen genom att installera beroenden som inte kan installeras från PyPi. 

   Filen ska innehålla Docker installationsstegen som en DockerFile. Följande kommandon är tillåtna i filen: 

    KÖRA, EXPONERA ENV, ARG, ETIKETT,

   Exempel CLI-kommando:
   ```azurecli
   az ml image create -n <my Image Name> --manifest-id <my Manifest ID> --docker-file <myDockerStepsFileName> 
   ```

   Bild, Manifest och tjänsten kommandona accepterar flaggan docker-filen.

   Docker steg exempelfil:
   ```docker
   # Install tools required to build the project
   RUN apt-get update && apt-get install -y git libxml2-dev
   # Install library dependencies
   RUN dep ensure -vendor-only
   ```

> [!NOTE]
> Basavbildningen för Azure ML-behållare är Ubuntu och kan inte ändras. Om du anger en annan basavbildning kommer att ignoreras.

## <a name="next-steps"></a>Nästa steg
Nu när du har anpassat en behållaravbildning kan distribuera du det till ett kluster för storskalig användning.  Mer information om hur du konfigurerar ett kluster för distribution av webbtjänster finns i [Administrationskonfiguration för modellen](deployment-setup-configuration.md). 
