---
title: Använd hemligheter i utbildningskörningar
titleSuffix: Azure Machine Learning
description: Skicka hemligheter till utbildning körs på säkert sätt med Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942281"
---
# <a name="use-secrets-in-training-runs"></a>Använd hemligheter i utbildningskörningar
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder hemligheter i utbildning körs säkert. Autentiseringsinformation som ditt användarnamn och lösenord är hemligheter. Om du till exempel ansluter till en extern databas för att fråga träningsdata måste du skicka ditt användarnamn och lösenord till fjärrkörningskontexten. Kodning sådana värden i utbildning skript i klartext är osäker eftersom det skulle avslöja hemligheten. 

I stället har din Azure Machine Learning-arbetsyta en associerad resurs som kallas [ett Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Använd det här nyckelvalvet för att skicka hemligheter till fjärrkörningar på ett säkert sätt genom en uppsättning API:er i Azure Machine Learning Python SDK.

Det grundläggande flödet för att använda hemligheter är:
 1. På den lokala datorn loggar du in på Azure och ansluter till din arbetsyta.
 2. På den lokala datorn anger du en hemlighet i Workspace Key Vault.
 3. Skicka en fjärrkörning.
 4. Inom fjärrkörningen hämtar du hemligheten från Key Vault och använder den.

## <a name="set-secrets"></a>Ange hemligheter

I Azure Machine Learning innehåller klassen [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) metoder för att ange hemligheter. I den lokala Python-sessionen får du först en referens till [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) din arbetsytenyckelvalv och använder sedan metoden för att ange en hemlighet efter namn och värde. Metoden __set_secret__ uppdaterar det hemliga värdet om namnet redan finns.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Placera inte det hemliga värdet i python-koden eftersom det är osäkert att lagra det i filen som klartext. Hämta i stället det hemliga värdet från en miljövariabel, till exempel Azure DevOps build secret eller från interaktiv användarinmatning.

Du kan lista hemliga namn med hjälp av [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) metoden och det finns också en batchversion,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) som gör att du kan ange flera hemligheter åt gången.

## <a name="get-secrets"></a>Få hemligheter

I din lokala kod kan[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) du använda metoden för att hämta det hemliga värdet efter namn.

För inskickade körningar [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) använder [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) du metoden med klassen. Eftersom en skickad körning är medveten om arbetsytan, genvägar den här metoden arbetsytans instansiering och returnerar det hemliga värdet direkt.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Var noga med att inte exponera det hemliga värdet genom att skriva eller skriva ut det.

Det finns också en batch-version, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) för att komma åt flera hemligheter på en gång.

## <a name="next-steps"></a>Nästa steg

 * [Visa exempel anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Lär dig mer om företagssäkerhet med Azure Machine Learning](concept-enterprise-security.md)
