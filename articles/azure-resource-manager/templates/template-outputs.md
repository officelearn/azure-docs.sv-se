---
title: Utdata i mallar
description: Beskriver hur du definierar utdata i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460032"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Utdata i Azure Resource Manager mall

I den här artikeln beskrivs hur du definierar utdataparametrar i Azure Resource Manager-mallen. Du använder utdata när du behöver returnera värden från de distribuerade resurserna.

## <a name="define-output-values"></a>Definiera värden för utdata

I följande exempel visas hur du returnerar resurs-ID för en offentlig IP-adress:

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

## <a name="dynamic-number-of-outputs"></a>Dynamiskt antal utdata

I vissa fall vet du inte hur många instanser av ett värde du behöver returnera när du skapar mallen. Du kan returnera ett variabelt antal värden med hjälp av **kopierings** elementet.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Mer information finns i [utdata iteration i Azure Resource Manager mallar](copy-outputs.md).

## <a name="linked-templates"></a>Länkade mallar

Om du vill hämta värdet för utdata från en länkad mall använder du funktionen [Reference](template-functions-resource.md#reference) i den överordnade mallen. Syntaxen i den överordnade mallen är:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

När du hämtar en output-egenskap från en länkad mall får egenskaps namnet inte innehålla något bindestreck.

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte använda `reference` funktionen i avsnittet utdata i en [kapslad mall](linked-templates.md#nested-template). Om du vill returnera värdena för en distribuerad resurs i en kapslad mall konverterar du den kapslade mallen till en länkad mall.

## <a name="get-output-values"></a>Hämta utgående värden

När distributionen lyckas returneras värdena för utdata automatiskt i resultatet av distributionen.

Du kan använda skript för att hämta utdata från distributions historiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
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
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID: t. |
|[Belastningsutjämnare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för utdata finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
