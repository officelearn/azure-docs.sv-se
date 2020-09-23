---
title: 'Självstudie: kom igång med Machine Learning – python'
titleSuffix: Azure Machine Learning
description: I den här självstudien kommer du att komma igång med Azure Machine Learning python SDK som körs i din personliga utvecklings miljö.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946640"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Självstudie: kom igång med Azure Machine Learning i utvecklings miljön (del 1 av 4)

I den här **själv studie serien i fyra delar**får du lära dig grunderna i Azure Machine Learning och slutföra Jobbbaserade python ml-uppgifter i Azure-molnet, inklusive:

1. Konfigurera en arbets yta och din lokala Machine Learning Developer-miljö.
2. Kör kod i molnet med hjälp av Azure Machine Learnings python SDK.
3. Hantera den python-miljö som du använder för modell träning.
4. Ladda upp data till Azure och använda dessa data i utbildningen.

I **del 1 av den här själv studie serien**kommer du att:

> [!div class="checklist"]
> * Installera Azure Machine Learning SDK
> * Konfigurera katalog strukturen för kod
> * Skapa en Azure Machine Learning-arbetsyta
> * Konfigurera din lokala utvecklings miljö
> * Konfigurera ett beräknings kluster

>[!NOTE]
> I den här själv studie serien fokuserar vi Azure Machine Learning koncept som passar för python __-jobbbaserade__ Machine Learning-uppgifter som är beräknings intensiva och/eller kräver reproducerbarhet. Om dina Machine Learning-uppgifter inte passar den här profilen kan du använda [Jupyter-eller RStudio-funktionen på en Azure Machine Learning beräknings instans](tutorial-1st-experiment-sdk-setup.md) för att publicera till Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova [Azure Machine Learning](https://aka.ms/AMLFree) idag.
- Bekanta dig med python och [Machine Learning koncept](concept-azure-machine-learning-architecture.md). Till exempel miljöer, utbildning, poäng och så vidare.
- En lokal utvecklings miljö – en bärbar dator med python installerat och din favorit-IDE (till exempel: VSCode, pycharm med, Jupyter och så vidare).

## <a name="install-the-azure-machine-learning-sdk"></a>Installera Azure Machine Learning SDK

I den här självstudien använder vi Azure Machine Learning python SDK.

Du kan använda de verktyg som är mest välkända för dig, till exempel: Conda, pip och så vidare, så att du kan konfigurera en miljö som ska användas i den här självstudien. Installera i miljön Azure Machine Learning python SDK via PIP:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Skapa katalog struktur för kod
Vi rekommenderar att du har följande enkla katalog konfiguration för den här självstudien:

```markdown
tutorial
└──.azureml
```

- **självstudie** (katalogen på den översta nivån i projektet)
- **. azureml** (dold under katalog i självstudie): `.azureml` katalogen används för att lagra Azure Machine Learning konfigurationsfiler.

## <a name="create-an-azure-machine-learning-workspace"></a>Skapa en Azure Machine Learning-arbetsyta

En arbets yta är en resurs på den översta nivån för Azure Machine Learning och är en central plats för att:

- Hantera resurser som Compute
- Lagra till gångar som antecknings böcker, miljöer, data uppsättningar, pipeliner, modeller, slut punkter och så vidare
- Samar beta med andra grupp medlemmar

I den översta överordnade katalogen – `tutorial` Lägg till en ny python-fil `01-create-workspace.py` som heter med koden nedan. Anpassa parametrarna (namn, prenumerations-ID, resurs grupp och [plats](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) med dina inställningar.

Du kan köra koden i en interaktiv session eller som en python-fil.

>[!NOTE]
> När du använder en lokal utvecklings miljö (t. ex. en bärbar dator) blir du ombedd att autentisera till din arbets yta med en *enhets kod* första gången du kör koden nedan. Följ anvisningarna på skärmen.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Kör den här koden från `tutorial` katalogen:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

När ovanstående kodfragment har körts kommer mappstrukturen att se ut så här:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Filen `.azureml/config.json` innehåller de metadata som krävs för att ansluta till din Azure Machine Learning-arbetsyta – nämligen ditt prenumerations-ID, resurs grupp och arbets ytans namn. 

> [!NOTE]
> Innehållet i `config.json` är inte hemligheter – det är helt bra att dela dessa uppgifter.
> Autentisering krävs fortfarande för att interagera med din Azure Machine Learning-arbetsyta.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Skapa ett Azure Machine Learning beräknings kluster

Skapa ett Python-skript i `tutorial` katalogen på den översta nivån som heter `02-create-compute.py` och fyll i med följande kod för att skapa ett Azure Machine Learning beräknings kluster som automatiskt skalar mellan noll och fyra noder:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Kör python-filen:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> När klustret har skapats har 0 noder etablerat. Klustret debiteras därför **inte** förrän du skickar ett jobb. Klustret kommer att skalas ned när det har varit inaktivt i 2400 sekunder (40 minuter).

Mappstrukturen kommer nu att se ut så här:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Nästa steg

I den här själv studie kursen har du:

- Skapat en Azure Machine Learning arbets yta
- Konfigurera din lokala utvecklings miljö
- Skapade ett Azure Machine Learning beräknings kluster.

I nästa självstudie går du igenom hur du skickar ett skript till Azure Machine Learning beräknings kluster.

> [!div class="nextstepaction"]
> [Självstudie: kör python-skriptet "Hello World" på Azure](tutorial-1st-experiment-hello-world.md)
