---
title: Azure Resource Manager-distributionslägen | Microsoft Docs
description: Beskriver hur du anger om du vill använda en fullständig eller inkrementell distributionsläget med Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: f3ca140fd8606f60a07b71db32cf2d3987ed7860
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233607"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager-distributionslägen

När du distribuerar dina resurser kan ange du att distributionen är en inkrementell uppdatering eller en fullständig uppdatering.  Den viktigaste skillnaden mellan dessa två lägena är hur Resource Manager hanterar befintliga resurser i resursgruppen som inte är i mallen. Standardläget är inkrementell.

Endast på rotnivå mallar stöder fullständig Distributionsläge. För [länkad eller kapslade mallar](resource-group-linked-templates.md), måste du använda inkrementella läge. 

## <a name="incremental-and-complete-deployments"></a>Inkrementella och fullständiga distributioner

Resource Manager försöker skapa alla resurser som angetts i mallen för båda lägena. Om resursen finns redan i resursgruppen och dess inställningar har inte ändrats, tas ingen åtgärd för den resursen. Om du ändrar egenskapsvärden för en resurs uppdateras resursen med de nya värdena. Om du försöker uppdatera plats eller typ av en befintlig resurs misslyckas distributionen med ett fel. I stället distribuera en ny resurs med platsen eller ange att du behöver.

I Resource Manager-fullständig läge **tar bort** resurser som finns i resursgruppen men inte anges i mallen. Resurser som har angetts i mallen, men inte distribueras eftersom en [villkor](resource-manager-templates-resources.md#condition) utvärderas till false, tas inte bort.

I Resource Manager-inkrementella läge **lämnar oförändrade** resurser som finns i resursgruppen men inte anges i mallen. När du distribuerar om en resurs i inkrementella läge, anger du alla egenskapsvärden för resurs, inte bara de som du uppdaterar. Om du inte anger vissa egenskaper, tolkar uppdateringen som skriver över dessa värden i Resource Manager.

## <a name="example-result"></a>Exempelresultat

För att visa skillnaden mellan inkrementell och fullständig lägen kan du studera följande scenario.

**Resursgrupp** innehåller:

* Resurs A
* Resurs B
* Resurs C

**Mallen** innehåller:

* Resurs A
* Resurs B
* Resurs D

När de distribueras i **inkrementella** läge, resursgruppen har:

* Resurs A
* Resurs B
* Resurs C
* Resurs D

När de distribueras i **fullständig** läge, resurs C har tagits bort. Resursgruppen har:

* Resurs A
* Resurs B
* Resurs D

## <a name="set-deployment-mode"></a>Inställning för distribution

Ange Distributionsläge när du distribuerar med PowerShell och den `Mode` parametern.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Ange Distributionsläge när du distribuerar med Azure CLI och den `mode` parametern.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

I följande exempel visas en länkad mall stegvis distribution-läge:

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Läs om hur du skapar Resource Manager-mallar i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs om hur du distribuerar resurser i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).
* Åtgärder för en resursprovider finns [Azure REST API](/rest/api/).
