---
title: Återställ felet till en lyckad distribution
description: Ange att en misslyckad distribution ska återställas till en lyckad distribution.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460151"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Återställning vid fel till lyckad distribution

När en distribution misslyckas kan du automatiskt distribuera om en tidigare, lyckad distribution från distributionshistoriken. Den här funktionen är användbar om du har ett fungerande tillstånd för infrastrukturdistributionen och vill återgå till det här tillståndet. Det finns ett antal förbehåll och begränsningar:

- Omfördelningen körs exakt som den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Den tidigare distributionen körs med [det fullständiga läget](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den tidigare distributionen tas bort och alla resurskonfigurationer anges till deras tidigare tillstånd. Se till att du har full förståelse för [distributionslägena](./deployment-modes.md).
- Omfördelningen påverkar bara resurserna, eventuella dataändringar påverkas inte.
- Du kan bara använda den här funktionen med resursgruppsdistributioner, inte distributioner på prenumerations- eller hanteringsgruppsnivå. Mer information om distribution på prenumerationsnivå finns i [Skapa resursgrupper och resurser på prenumerationsnivå](./deploy-to-subscription.md).
- Du kan bara använda det här alternativet med distributioner på rotnivå. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill använda det här alternativet måste distributionerna ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn kan den aktuella misslyckade distributionen skriva över den tidigare lyckade distributionen i historiken.

## <a name="powershell"></a>PowerShell

Om du vill distribuera om den `-RollbackToLastDeployment` senaste lyckade distributionen lägger du till parametern som en flagga.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Om du vill distribuera om en `-RollBackDeploymentName` viss distribution använder du parametern och anger namnet på distributionen. Den angivna distributionen måste ha lyckats.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Om du vill distribuera om den `--rollback-on-error` senaste lyckade distributionen lägger du till parametern som en flagga.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Om du vill distribuera om en `--rollback-on-error` viss distribution använder du parametern och anger namnet på distributionen. Den angivna distributionen måste ha lyckats.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>REST API

Om du vill distribuera om den senaste lyckade distributionen om den aktuella distributionen misslyckas använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Om du vill distribuera om en viss distribution om den aktuella distributionen misslyckas använder du:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Den angivna distributionen måste ha lyckats.

## <a name="next-steps"></a>Nästa steg

- Information om hur du distribuerar tjänsten till mer än en region på ett säkert sätt finns i [Azure Deployment Manager](deployment-manager-overview.md).
- Information om hur resurser som finns i resursgruppen men som inte har definierats i mallen ska [hanteras](deployment-modes.md).
- Information om hur du definierar parametrar i mallen finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [Distribuera privat mall med SAS-token](secure-template-with-sas-token.md).
