---
title: Självstudie – Skapa en Azure ML-arbetsyta – Resource Manager-mall
description: I den här självstudien använder du en Azure Resource Manager mall för att snabbt distribuera en Azure-arbetsyta för Machine Learning
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 760406b738d2aa95ef086941850814f4bf39fbb4
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639876"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Självstudie: Distribuera en Azure Machine Learning-arbetsyta med en ARM-mall

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här självstudien visas hur du skapar en Azure Machine Learning-arbetsyta med hjälp av en Azure Resource Manager-mall (ARM-mall). Azure Machine Learning-arbetsytor ordnar alla Machine Learning-tillgångar från bas linje data uppsättningar till distribuerade modeller. Arbets ytor är en enda plats för att samar beta med kollegor om att skapa, köra och granska experiment, hantera din utbildning och inferencing beräknings resurser samt övervaka och distribuera modeller.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/services/machine-learning/) innan du börjar.

* Om du vill använda CLI-kommandona i det här dokumentet från din **lokala miljö**behöver du [Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Följande resurser definieras i mallen:

* [Microsoft. MachineLearningServices/arbets ytor](/azure/templates/microsoft.machinelearningservices/workspaces): skapa en Azure ml-arbetsyta. I den här mallen är platsen och namnet parametrar som användaren kan skicka i eller interaktivt ange.

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill använda mallen från Azure CLI loggar du in och väljer din prenumeration (se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)). Kör sedan:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location &&
echo "Press [ENTER] to continue ..." &&
read
```

När du kör kommandot ovan anger du:

1. Ett projekt namn som ska utgöra grunden för namnen på den skapade resurs gruppen och Azure ML-arbetsytan.
1. Den Azure-plats där du vill göra distributionen.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Så här visar du din Azure ML-arbetsyta:

1. Gå till https://portal.azure.com.
1. Logga in.
1. Välj den arbets yta som du nyss skapade.

Du ser Azure Machine Learning start sidan:

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Skärm bild av Azure ML-arbetsytan":::

Om du vill se alla resurser som är kopplade till distributionen klickar du på länken i det övre vänstra hörnet med arbets ytans namn (i skärm bilden `my_templated_ws` ). Länken tar dig till resurs gruppen i Azure Portal. Resurs gruppens namn är `{projectName}rg` och arbets ytan heter `{projectName}ws` .

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill använda den här arbets ytan tar du bort den. Eftersom arbets ytan är kopplad till andra resurser, till exempel ett lagrings konto, vill du förmodligen ta bort hela resurs gruppen som du skapade. Du kan ta bort resurs gruppen med hjälp av portalen genom att klicka på knappen **ta bort** och bekräfta. Du kan också ta bort resurs gruppen från CLI med:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Azure Machine Learning-arbetsyta från en ARM-mall. Om du vill utforska Azure Machine Learning fortsätter du med självstudien.

> [!div class="nextstepaction"]
> [Självstudie: kom igång med att skapa ditt första ML-experiment med python SDK](tutorial-1st-experiment-sdk-setup.md)
