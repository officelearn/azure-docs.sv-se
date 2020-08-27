---
title: Använda privata Python-paket
titleSuffix: Azure Machine Learning
description: Få åtkomst till privata python-paket på ett säkert sätt från Azure Machine Learning miljöer.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 314f6a45bf688125e79f0b8ce0099a8326b339dc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958158"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Använd privata python-paket med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln lär du dig att använda privata python-paket på ett säkert sätt inom Azure Machine Learning. Användnings fall för privata python-paket är:

 * Du har utvecklat ett privat paket som du inte vill dela offentligt.
 * Du vill använda en granskad lagrings plats för paket som lagras i en företags brand vägg.

Den rekommenderade metoden beror på om du har några paket för en enskild Azure Machine Learning arbets yta eller en hel lagrings plats för paket för alla arbets ytor inom en organisation.

Privata paket används via [miljö](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) klass. I en miljö deklarerar du vilka python-paket som ska användas, inklusive privata. Mer information om miljön i Azure Machine Learning i allmänhet finns i [så här använder du miljöer](how-to-use-environments.md). 

## <a name="prerequisites"></a>Förutsättningar

 * [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Använd ett litet antal paket för utveckling och testning

För ett litet antal privata paket för en enskild arbets yta använder du den statiska [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) metoden. Med den här metoden kan du snabbt lägga till ett privat paket i arbets ytan, och det passar utmärkt för utvecklings-och testnings ändamål.

Peka fil Sök vägs argumentet till en lokal Wheel-fil och kör ```add_private_pip_wheel``` kommandot. Kommandot returnerar en URL som används för att spåra platsen för paketet i din arbets yta. Avbilda lagrings-URL: en och skicka den till `add_pip_package()` metoden.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Internt ersätter Azure Machine Learning-tjänsten URL: en med säker SAS-URL, så att din Wheel-fil hålls privat och säker.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Använd en lagrings plats för paket från Azure DevOps-feed

Om du aktivt utvecklar python-paket för ditt Machine Learning-program kan du vara värd för dem i en Azure DevOps-lagringsplats som artefakter och publicera dem som en feed. Med den här metoden kan du integrera DevOps-arbetsflödet för att skapa paket med din Azure Machine Learning-arbetsyta. Läs [komma igång med python-paket i Azure-artefakter](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops) för att lära dig hur du konfigurerar python-flöden med Azure DevOps

Den här metoden använder personlig åtkomsttoken för att autentisera mot lagrings platsen. Samma metod gäller för andra databaser med token-baserad autentisering, till exempel privata GitHub-databaser. 

 1. [Skapa en personlig åtkomsttoken (Pat)](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) för din Azure DevOps-instans. Ange omfånget för token som ska __paketera > läsa__. 

 2. Lägg till URL-och PAT för Azure-DevOps som arbets ytans egenskaper med hjälp av metoden [arbetsyte. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) .

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Skapa en Azure Machine Learning-miljö och Lägg till python-paket från feeden.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

Miljön är nu redo att användas i utbildnings körningar eller distributioner av webb tjänst slut punkter. När du skapar miljön använder Azure Machine Learning tjänsten PAT för att autentisera mot feeden med matchande bas-URL.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Använd en lagrings plats för paket från privat lagring

Du kan använda paket från ett Azure Storage-konto i din organisations brand vägg. Lagrings kontot kan innehålla en granskad uppsättning paket eller en intern spegling av offentligt tillgängliga paket.

Så här konfigurerar du sådan privat lagring:

1. [Placera arbets ytan i ett virtuellt nätverk (VNet)](how-to-enable-virtual-network.md).
1. Skapa ett lagrings konto och [Tillåt inte offentlig åtkomst](https://docs.microsoft.com/azure/storage/common/storage-network-security).
1. Placera de python-paket som du vill använda i en behållare i lagrings kontot 
1. [Tillåt åtkomst till lagrings kontot från VNet-arbetsytan](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
1. [Placera Azure Container Registry (ACR) för arbets ytan bakom VNet](how-to-enable-virtual-network.md#azure-container-registry).

    > [!IMPORTANT]
    > Du måste slutföra det här steget för att kunna träna eller distribuera modeller med hjälp av det privata paketets lagrings plats.

När du har slutfört de här konfigurationerna kan du referera till paketen i Azure Machine Learning-miljödefinitionen med sin fullständiga URL i Azure Blob Storage.

## <a name="next-steps"></a>Nästa steg

 * Läs mer om [företags säkerhet i Azure Machine Learning](concept-enterprise-security.md)
