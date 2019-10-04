---
title: Utdata i Azure Resource Manager mall
description: Beskriver hur du definierar utdata i en Azure Resource Manager-mall.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 0ff1e3cb71bd1bf5ee947eb5204839d48103628b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827930"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Utdata i Azure Resource Manager mall

I den här artikeln beskrivs hur du definierar utdataparametrar i Azure Resource Manager-mallen. Du använder utdata när du behöver returnera värden från de distribuerade resurserna.

## <a name="define-output-values"></a>Definiera värden för utdata

I följande exempel visas hur du skickar tillbaka resurs-ID för en offentlig IP-adress:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Villkorliga utdata

I avsnittet utdata kan du villkorligt returnera ett värde. Normalt använder du villkor i utdata när du har [villkorligt distribuerat](conditional-resource-deployment.md) en resurs. I följande exempel visas hur du villkorligt returnerar resurs-ID: t för en offentlig IP-adress baserat på om en ny har distribuerats:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Ett enkelt exempel på villkorliga utdata finns i [mallen för villkorsstyrda utdata](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="linked-templates"></a>Länkade mallar

Om du vill hämta värdet för utdata från en länkad mall använder du funktionen [Reference](resource-group-template-functions-resource.md#reference) i den överordnade mallen. Syntaxen i den överordnade mallen är:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

När du hämtar en egenskap för utdata från en länkad mall, får inte egenskapsnamnet innehålla ett bindestreck.

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte använda den `reference` funktion i avsnittet utdata i en [kapslade mallen](resource-group-linked-templates.md#nested-template). Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

## <a name="get-output-values"></a>Hämta utgående värden

När distributionen lyckas returneras värdena för utdata automatiskt i resultatet av distributionen.

Du kan använda skript för att hämta utdata från distributions historiken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar scenarier för att använda utdata.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inte några resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Lastbalanserare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för utdata finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Rekommendationer om hur du skapar utdata finns i [metod tips – utdata](template-best-practices.md#outputs).
