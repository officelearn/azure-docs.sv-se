---
title: Använda hemligheter i utbildnings körningar
titleSuffix: Azure Machine Learning
description: Skicka hemligheter till inlärnings körningar på ett säkert sätt med hjälp av arbets ytan Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2074cec65ea4c1df803999c6a995f73ea4227ee
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796675"
---
# <a name="use-secrets-in-training-runs"></a>Använda hemligheter i utbildnings körningar
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du använder hemligheter i utbildning på ett säkert sätt. Om du till exempel vill ansluta till en extern databas för att fråga efter tränings data måste du skicka användar namn och lösen ord till fjärrkörnings kontexten. Att koda sådana värden till utbildnings skript i klartext är inte säkra eftersom det exponerar hemligheten. 

I stället har Azure Machine Learning-arbetsyta [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) som tillhör ande resurs. Den här Key Vault kan användas för att skicka hemligheter till fjärrkörningar på ett säkert sätt via en uppsättning API: er i Azure Machine Learning python SDK.

Det grundläggande flödet för att använda hemligheter är:
 1. Logga in på Azure på den lokala datorn och Anslut till din arbets yta.
 2. På den lokala datorn anger du en hemlighet i arbets ytans Key Vault.
 3. Skicka en fjärrkörning.
 4. I fjärrkörning hämtar du hemligheten från nyckel värde och använder den.

## <a name="set-secrets"></a>Ange hemligheter

I Azure Machine Learning python SDK innehåller klassen [Vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) metoder för att ställa in hemligheter. I din lokala python-session hämtar du först en referens till arbets ytans Key Vault och använder sedan [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) -metoden för att ange en hemlighet efter namn och värde.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Placera inte det hemliga värdet i python-koden eftersom det är osäkert att lagra det i filen som klartext. Hämta i stället det hemliga värdet från miljövariabeln, till exempel Azure DevOps build Secret eller från interaktiva användarindata.

Du kan lista hemliga namn med metoden [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) . Metoden __set_secret__ uppdaterar det hemliga värdet om namnet redan finns.

## <a name="get-secrets"></a>Hämta hemligheter

I din lokala kod kan du använda nyckel [valv. get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) -metoden för att hämta det hemliga värdet efter namn.

I körs som skickas med [experiment. submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-), Använd [Run. get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) -metoden. Eftersom en skickad körning är medveten om dess arbets yta, tar den här metoden genvägar till instansiering av arbets ytan och returnerar det hemliga värdet direkt.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Var noga med att inte exponera det hemliga värdet genom att skriva eller skriva ut det.

Metoderna Set och get har också batch-versioner [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) och [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) för åtkomst till flera hemligheter samtidigt.

## <a name="next-steps"></a>Nästa steg

 * [Visa exempel antecknings bok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Lär dig mer om företags säkerhet med Azure Machine Learning](concept-enterprise-security.md)
