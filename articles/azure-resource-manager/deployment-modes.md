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
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: c8c6c5499e1cea04bc5bdffbb5c07b53b96182e2
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054356"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager-distributionslägen
När du distribuerar dina resurser kan ange du att distributionen är en inkrementell uppdatering eller en fullständig uppdatering.  Den viktigaste skillnaden mellan dessa två lägena är hur Resource Manager hanterar befintliga resurser i resursgruppen som inte är i mallen.
Standardläget är inkrementell.

## <a name="incremental-and-complete-deployments"></a>Inkrementella och fullständiga distributioner
När du distribuerar resurser:

* I Resource Manager-fullständig läge **tar bort** resurser som finns i resursgruppen men inte anges i mallen. 
* I Resource Manager-inkrementella läge **lämnar oförändrade** resurser som finns i resursgruppen men inte anges i mallen.

Resource Manager försöker etablera alla resurser som angetts i mallen för båda lägena. Om resursen finns redan i resursgruppen och dess inställningar har inte ändrats, resulterar åtgärden i ingen ändring. Om du ändrar inställningarna för en resurs måste har resursen tillhandahållits med de nya inställningarna. Om du försöker uppdatera plats eller typ av en befintlig resurs, misslyckas distributionen med ett fel. I stället distribuera en ny resurs med platsen eller ange att du behöver.

## <a name="example-result"></a>Exempelresultat

För att visa skillnaden mellan inkrementell och fullständig lägen kan du studera följande scenario.

**Befintlig resursgrupp** innehåller:

* Resurs A
* Resurs B
* Resurs C

**Mallen** definierar:

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

```powershell
New-AzureRmResourceGroupDeployment `
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

När du använder en [länkade eller kapslad mall](resource-group-linked-templates.md), måste du ställa in den `mode` egenskap `Incremental`. Endast på rotnivå mallar stöder fullständig Distributionsläge.

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

