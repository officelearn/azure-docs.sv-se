---
title: Azure Resource Manager-mall matar ut | Microsoft Docs
description: Beskriver hur du definierar utdata för en Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 92e5dd5190a76bd09e33ea4c40a5b5cc2d66bc7b
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301147"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Outputs-avsnittet i Azure Resource Manager-mallar

I Outputs-avsnittet anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en distribuerad resurs. Använd det valfria `condition` som anger om värdet returneras.

## <a name="define-and-use-output-values"></a>Definiera och Använd utdatavärden

I följande exempel visas hur du skickar tillbaka resurs-ID för en offentlig IP-adress:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

I nästa exempel visas hur du returnerar villkorligt resurs-ID för en offentlig IP-adress baserat på om en ny något har distribuerats:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Efter distributionen kan hämta du värdet med skript. Om du använder PowerShell använder du:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Om du använder Azure CLI använder du:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Du kan hämta värdet från en länkad mall med hjälp av den [referens](resource-group-template-functions-resource.md#reference) funktion. För att få utdatavärde från en länkad mall kan hämta egenskapens värde med syntax som: `"[reference('deploymentName').outputs.propertyName.value]"`.

När du hämtar en egenskap för utdata från en länkad mall, får inte egenskapsnamnet innehålla ett bindestreck.

Du kan till exempel ange IP-adressen för en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte använda den `reference` funktion i avsnittet utdata i en [kapslade mallen](resource-group-linked-templates.md#link-or-nest-a-template). Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

## <a name="available-properties"></a>Tillgängliga egenskaper

I följande exempel visar strukturen för en utdata-definition:

```json
"outputs": {
    "<outputName>" : {
        "condition": "<boolean-value-whether-to-output-value>",
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| outputName |Ja |Namnet på värdet. Måste vara en giltig JavaScript-identifierare. |
| villkor |Nej | Booleskt värde som anger om utdata detta värde returneras. När `true`, värdet ingår i utdata för distributionen. När `false`, hoppas över värdet för den här distributionen. Om inget värde anges är standardvärdet `true`. |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöder samma datatyper som mall indataparametrar. |
| värde |Ja |Mallspråksuttrycket som utvärderas och returneras som utdatavärde. |

Information om att lägga till kommentarer finns i [kommentarer i mallar](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Exempel på mallar

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inte några resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Lastbalanserare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Rekommendationer om hur du skapar mallar finns i [Metodtips för Azure Resource Manager-mall](template-best-practices.md).
