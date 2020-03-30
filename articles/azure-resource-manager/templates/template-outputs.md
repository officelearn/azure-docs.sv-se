---
title: Utdata i mallar
description: Beskriver hur du definierar utdatavärden i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460032"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Utdata i Azure Resource Manager-mall

I den här artikeln beskrivs hur du definierar utdatavärden i mallen Azure Resource Manager. Du använder utdata när du behöver returnera värden från de distribuerade resurserna.

## <a name="define-output-values"></a>Definiera utdatavärden

I följande exempel visas hur du returnerar resurs-ID:t för en offentlig IP-adress:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Villkorad utdata

I avsnittet utdata kan du villkorligt returnera ett värde. Vanligtvis använder du villkor i utdata när du villkorligt har [distribuerat](conditional-resource-deployment.md) en resurs. I följande exempel visas hur du villkorligt returnerar resurs-ID:t för en offentlig IP-adress baserat på om en ny har distribuerats:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Ett enkelt exempel på villkorsstyrd utdata finns i [villkorsutdatamall .](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)

## <a name="dynamic-number-of-outputs"></a>Dynamiskt antal utgångar

I vissa fall vet du inte hur många instanser av ett värde som du behöver returnera när du skapar mallen. Du kan returnera ett variabelt **copy** antal värden med hjälp av kopieringselementet.

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

Mer information finns [i Utdataiterationer i Azure Resource Manager-mallar](copy-outputs.md).

## <a name="linked-templates"></a>Länkade mallar

Om du vill hämta utdatavärdet från en länkad mall använder du [referensfunktionen](template-functions-resource.md#reference) i den överordnade mallen. Syntaxen i den överordnade mallen är:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

När du hämtar en utdataegenskap från en länkad mall kan egenskapsnamnet inte innehålla ett streck.

I följande exempel visas hur du ställer in IP-adressen på en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte `reference` använda funktionen i utdataavsnittet i en [kapslad mall](linked-templates.md#nested-template). Om du vill returnera värdena för en distribuerad resurs i en kapslad mall konverterar du den kapslade mallen till en länkad mall.

## <a name="get-output-values"></a>Hämta utdatavärden

När distributionen lyckas returneras utdatavärdena automatiskt i resultatet av distributionen.

Om du vill hämta utdatavärden från distributionshistoriken kan du använda skript.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

Följande exempel visar scenarier för användning av utdata.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inga resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Lastbalanserare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID:n i utdata när belastningsutjämnaren skapas. |

## <a name="next-steps"></a>Nästa steg

* Mer information om tillgängliga egenskaper för utdata finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
