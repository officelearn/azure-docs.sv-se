---
title: 'Snabb start: skapa en Azure Cognitive Services-resurs med ARM-mallar | Microsoft Docs'
description: Kom igång med att använda en Azure Resource Manager-mall för att distribuera en Cognitive Services-resurs.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/18/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: 9b2091c7acd42d4c967ec5baef82d970344f9231
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081587"
---
# <a name="create-a-cognitive-services-resource-using-an-azure-resource-manager-template"></a>Skapa en Cognitive Services resurs med hjälp av en Azure Resource Manager-mall

Använd den här artikeln för att skapa och distribuera en Cognitive Services resurs med en Azure Resource Manager ARM-mall (ARM). Med den här resursen med flera tjänster kan du:
* Få åtkomst till flera Azure-Cognitive Services med en enda nyckel och slut punkt.
* Konsolidera faktureringen från de tjänster som du använder.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]


## <a name="prerequisites"></a>Krav 

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts arm-mallar](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

En Azure-resurs har definierats i mallen:
* [Microsoft. CognitiveServices/Accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts): skapar en Cognitive Services-resurs.

### <a name="deploy-the-template"></a>Distribuera mallen

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Klicka på knappen **distribuera till Azure** .

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Ange följande värden.
    
    |Värde  |Beskrivning  |
    |---------|---------|
    | **Prenumeration** | Välj en Azure-prenumeration. |
    | **Resursgrupp** | Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**. |
    | **Region** | Välj en region.  Till exempel **USA, östra** |
    | **Kognitivt tjänst namn** | Ersätt med ett unikt namn för din resurs. Du kommer att behöva namnet i nästa avsnitt när du validerar distributionen. |
    | **Position** | Ersätt med den region som används ovan. |
    | **SKU** | [Pris nivån](https://azure.microsoft.com/pricing/details/cognitive-services/) för din resurs. |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Skärmen skapa resurs.":::

3. Välj **Granska + skapa** och klicka sedan på **Skapa**. När resursen har slutfört distributionen markeras knappen **gå till resurs** .

    

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group`Create kräver Azure CLI version 2,6 eller senare. För att Visa versions typen `az --version` . Mer information finns i [dokumentationen](https://docs.microsoft.com/cli/azure/deployment/group).

Kör följande skript med hjälp av Azure Command Line Interface (CLI) [på den lokala datorn](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)eller i en webbläsare med knappen **prova** . Ange ett namn och en plats (till exempel `centralus` ) för en ny resurs grupp och arm-mallen kommer att användas för att distribuera en Cognitive Services resurs i den. Kom ihåg det namn som du använder. Du kommer att använda den senare för att verifiera distributionen.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---


## <a name="validate-the-deployment"></a>Verifiera distributionen

# <a name="portal"></a>[Portal](#tab/portal)

När distributionen är klar kan du klicka på knappen **gå till resurs** för att se din nya resurs. Du kan också hitta resurs gruppen genom att:

1. Välj **resurs grupper** på den vänstra navigerings menyn.
2. Välja resurs gruppens namn.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Använd Azure CLI och kör följande skript och ange namnet på den resurs grupp som du skapade tidigare. 

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen tas även andra resurser som ingår i gruppen bort.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta upp resurs gruppen som innehåller resursen som ska tas bort
3. Högerklicka på listan över resurs grupper. Välj **Ta bort resursgrupp** och bekräfta.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Använd Azure CLI och kör följande skript och ange namnet på den resurs grupp som du skapade tidigare. 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Nästa steg

* [Autentisera begär anden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)
