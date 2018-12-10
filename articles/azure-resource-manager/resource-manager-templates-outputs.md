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
ms.date: 12/07/2018
ms.author: tomfitz
ms.openlocfilehash: 85aab429fd59afd36cd026e6d8aef2b7e6f6e122
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140463"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Outputs-avsnittet i Azure Resource Manager-mallar
I Outputs-avsnittet anger du värden som returneras från distributionen. Du kan till exempel returnera URI: N för att komma åt en distribuerad resurs.

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

Efter distributionen kan hämta du värdet med skript. Om du använder PowerShell använder du:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
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
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| outputName |Ja |Namnet på värdet. Måste vara en giltig JavaScript-identifierare. |
| typ |Ja |Typ av utdatavärde. Utdatavärden stöder samma datatyper som mall indataparametrar. |
| värde |Ja |Mallspråksuttrycket som utvärderas och returneras som utdatavärde. |

## <a name="recommendations"></a>Rekommendationer

Om du använder en mall för att skapa offentliga IP-adresser kan innehålla en outputs-avsnittet som returnerar information om IP-adressen och det fullständigt kvalificerade domännamnet (FQDN). Du kan använda utdatavärden för att enkelt hämta information om offentliga IP-adresser och FQDN efter distributionen.

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

## <a name="example-templates"></a>Exempel på mallar


|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Skapar komplexa variabler och matar ut dessa värden. Distribuerar inte några resurser. |
|[Offentlig IP-adress](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Skapar en offentlig IP-adress och matar ut resurs-ID. |
|[Lastbalanserare](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Länkar till föregående mall. Använder resurs-ID i utdata när du skapar belastningsutjämnaren. |


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen, se [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas mellan flera resursgrupper. Mer information finns i den [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).