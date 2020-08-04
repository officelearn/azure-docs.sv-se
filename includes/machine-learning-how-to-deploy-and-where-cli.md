---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542825"
---
## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](../articles/machine-learning/how-to-manage-workspace.md).
- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).
- [Tillägget för Azure Command Line Interface (CLI) för tjänsten Machine Learning](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

Följ anvisningarna i Azure CLI-dokumentationen för att [ställa in din prenumerations kontext](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Gör sedan följande:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

för att se de arbets ytor som du har åtkomst till.

## <a name="register-your-model"></a><a id="registermodel"></a>Registrera din modell

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [hitta modell filer i ditt Entry-skript](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Machine Learning-modeller registreras i din Azure Machine Learning-arbetsyta. Modellen kan komma från Azure Machine Learning eller från någon annan stans. När du registrerar en modell kan du välja att ange metadata om modellen. De `tags` `properties` ord listor som du använder för en modell registrering kan sedan användas för att filtrera modeller.

Följande exempel visar hur du registrerar en modell.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-utbildning

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du sökvägen till en mapp som innehåller filerna.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Om du vill inkludera flera filer i modell registreringen anger `-p` du sökvägen till en mapp som innehåller filerna.

Mer information finns i `az ml model register` [referens dokumentationen](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Definiera ett post skript

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Definiera en konfiguration för en härledning

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

Följande kommando visar hur du distribuerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

I det här exemplet anger konfigurationen följande inställningar:

* Som modellen kräver python
* [Entry-skriptet](#define-an-entry-script), som används för att hantera webb förfrågningar som skickas till den distribuerade tjänsten
* Den Conda-fil som beskriver de python-paket som behövs för att kunna utvägar

Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definiera en distributions konfiguration

Vilka alternativ som är tillgängliga för en distributions konfiguration beror på vilket beräknings mål du väljer.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Mer information finns i dokumentationen för [AZ ml-modellen Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Distribuera din modell

Nu är du redo att distribuera din modell.

### <a name="using-a-registered-model"></a>Använda en registrerad modell

Om du har registrerat din modell i Azure Machine Learning arbets ytan ersätter du "modell: 1" med namnet på din modell och dess versions nummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Använda en lokal modell

Om du föredrar att inte registrera din modell kan du skicka parametern "sourceDirectory" i din inferenceconfig.jspå för att ange en lokal katalog som din modell ska användas från.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Ta bort resurser

Använd om du vill ta bort en distribuerad WebService `az ml service <name of webservice>` .

Om du vill ta bort en registrerad modell från din arbets yta använder du`az ml model delete <model id>`

Läs mer om att [ta bort en WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) och [ta bort en modell](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)