---
title: Autentisering av hemligheter i utbildning
titleSuffix: Azure Machine Learning
description: Skicka hemligheter till inlärnings körningar på ett säkert sätt med hjälp av arbets ytan Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 11bcfa21b1a12df945a0dec6dc291c912a0e99c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047892"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Använd hemligheter för autentisering av autentiseringsuppgifter i Azure Machine Learning utbildnings körningar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder hemligheter i utbildning på ett säkert sätt. Autentiseringsinformation som ditt användar namn och lösen ord är hemligheter. Om du till exempel ansluter till en extern databas för att fråga efter tränings data måste du skicka ditt användar namn och lösen ord till fjärrkörnings kontexten. Att koda sådana värden till utbildnings skript i klartext är inte säkra eftersom det exponerar hemligheten. 

I stället har din Azure Machine Learning-arbetsyta en tillhör ande resurs som kallas för en [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Använd den här Key Vault för att skicka hemligheter till fjärrkörningar på ett säkert sätt via en uppsättning API: er i Azure Machine Learning python SDK.

Det grundläggande flödet för att använda hemligheter är:
 1. Logga in på Azure på den lokala datorn och Anslut till din arbets yta.
 2. På den lokala datorn anger du en hemlighet i arbets ytans Key Vault.
 3. Skicka en fjärrkörning.
 4. I fjärrkörningen hämtar du hemligheten från Key Vault och använder den.

## <a name="set-secrets"></a>Ange hemligheter

I Azure Machine Learning innehåller klassen för nyckel [valv](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) metoder för att ställa in hemligheter. I din lokala python-session får du först en referens till din arbets yta Key Vault och använder sedan [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metoden för att ange en hemlighet efter namn och värde. Metoden __set_secret__ uppdaterar det hemliga värdet om namnet redan finns.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Placera inte det hemliga värdet i python-koden eftersom det är osäkert att lagra det i filen som klartext. Hämta i stället det hemliga värdet från en miljö variabel, till exempel Azure DevOps build Secret eller från interaktiva användarindata.

Du kan lista hemliga namn med hjälp av [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) -metoden och det finns också en batch-version[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) som gör att du kan ange flera hemligheter i taget.

## <a name="get-secrets"></a>Hämta hemligheter

I din lokala kod kan du använda- [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) metoden för att hämta det hemliga värdet efter namn.

För körningar som skickats [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) använder du- [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metoden med- [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) klassen. Eftersom en skickad körning är medveten om dess arbets yta, tar den här metoden genvägar till instansiering av arbets ytan och returnerar det hemliga värdet direkt.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Var noga med att inte exponera det hemliga värdet genom att skriva eller skriva ut det.

Det finns också en batch-version [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) för åtkomst till flera hemligheter samtidigt.

## <a name="next-steps"></a>Nästa steg

 * [Visa exempel antecknings bok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Lär dig mer om företags säkerhet med Azure Machine Learning](concept-enterprise-security.md)
