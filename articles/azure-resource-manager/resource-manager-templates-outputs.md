---
title: Azure Resource Manager-mall matar ut | Microsoft Docs
description: Beskriver hur du definierar utdata för en Azure Resource Manager-mallar med deklarativ JSON-syntax.
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
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: e3c5a581b02f1dd7b7415ebd93de0e425ac2f8ae
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358373"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Utdata-avsnittet i Azure Resource Manager-mallar
I avsnittet utdata anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en resurs som är distribuerad.

## <a name="define-and-use-output-values"></a>Definiera och använder utdatavärden

I följande exempel visas hur du returnerar resurs-ID för en offentlig IP-adress:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Efter distributionen kan hämta du värdet med skript. Om du använder PowerShell använder du:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Om du använder Azure CLI använder du:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Du kan hämta värdet från en länkad mall med hjälp av den [referens](resource-group-template-functions-resource.md#reference) funktion. Hämta egenskapens värde med syntax som om du vill ha ett utdatavärde från en länkad mall: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Du kan exempelvis ange IP-adressen för en belastningsutjämnare genom att hämta ett värde från en länkad mall.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Du kan inte använda den `reference` funktion i avsnittet utdata i en [kapslade mallen](resource-group-linked-templates.md#link-or-nest-a-template). Konvertera kapslade mallen till en länkad mall för att returnera värden för en distribuerad resurs i en kapslad mall.

## <a name="available-properties"></a>Tillgängliga egenskaper

I följande exempel visar strukturen för en definition av utdata:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| outputName |Ja |Namnet på värdet. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöd för samma datatyper som mall indataparametrar. |
| värde |Ja |Mallspråksuttrycket som utvärderas och returneras som utdata. |

## <a name="recommendations"></a>Rekommendationer

Om du använder en mall för att skapa offentliga IP-adresser kan innehålla ett avsnitt för utdata som returnerar information om IP-adressen och det fullständigt kvalificerade domännamnet (FQDN). Du kan använda utdatavärden för att lätt kunna hämta information om offentlig IP-adresser och FQDN efter distributionen.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Exempel mallar


|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inte några resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Belastningsutjämnare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mallen. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner som du kan använda från i en mall finns [Azure Resource Manager mallen Functions](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas mellan flera resursgrupper. Mer information finns i [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).