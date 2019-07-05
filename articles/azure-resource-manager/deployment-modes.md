---
title: Azure Resource Manager-distributionslägen | Microsoft Docs
description: Beskriver hur du anger om du vill använda en fullständig eller inkrementell distributionsläget med Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: tomfitz
ms.openlocfilehash: 8a53ed1eea66c976c46a21378a9c48a1ad5ce902
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508216"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager-distributionslägen

När du distribuerar dina resurser kan ange du att distributionen är en inkrementell uppdatering eller en fullständig uppdatering.  Den viktigaste skillnaden mellan dessa två lägena är hur Resource Manager hanterar befintliga resurser i resursgruppen som inte är i mallen. Standardläget är inkrementell.

Resource Manager försöker skapa alla resurser som angetts i mallen för båda lägena. Om resursen finns redan i resursgruppen och dess inställningar har inte ändrats, tas ingen åtgärd för den resursen. Om du ändrar egenskapsvärden för en resurs uppdateras resursen med de nya värdena. Om du försöker uppdatera plats eller typ av en befintlig resurs misslyckas distributionen med ett fel. I stället distribuera en ny resurs med platsen eller ange att du behöver.

## <a name="complete-mode"></a>Fullständig läge

I Resource Manager-fullständig läge **tar bort** resurser som finns i resursgruppen men inte anges i mallen. Resurser som har angetts i mallen, men inte distribueras eftersom en [villkor](resource-group-authoring-templates.md#condition) utvärderas till false, tas inte bort.

Var försiktig med fullständig läge med [kopiera slingor](resource-group-create-multiple.md). Alla resurser som inte anges i mallen när du har löst kopiera loop tas bort.

Det finns vissa skillnader i hur resurstyper hantera fullständiga läge borttagningar. Överordnad resurser tas bort automatiskt när inte i en mall som har distribuerats i fullständiga läge. Vissa underordnade resurser bort inte automatiskt när inte i mallen. Men tas dessa underordnade resurser bort om den överordnade resursen tas bort. 

Om resursgruppen innehåller en DNS-zon (Microsoft.Network/dnsZones resurstyp) och en CNAME-post (Microsoft.Network/dnsZones/CNAME resurstyp), är DNS-zonen på den överordnade resursen för CNAME-post. Om du distribuerar med fullständig läge och omfattar inte DNS-zonen i mallen, DNS-zonen och CNAME-posten tas både bort. Om du inkluderar DNS-zonen i mallen men omfattar inte CNAME-posten CNAME inte tas bort. 

En lista över hur resurstyper hanterar borttagning, se [borttagning av Azure-resurser för distributioner av fullständig](complete-mode-deletion.md).

Om resursgruppen är [låst](resource-group-lock-resources.md), fullständig läge inte ta bort resurserna.

> [!NOTE]
> Endast på rotnivå mallar stöder fullständig Distributionsläge. För [länkad eller kapslade mallar](resource-group-linked-templates.md), måste du använda inkrementella läge. 
>
> [Prenumeration på distributioner](deploy-to-subscription.md) stöder inte fullständig läge.
>
> Portalen stöder för närvarande inte fullständig läge.
>

## <a name="incremental-mode"></a>Inkrementell läge

I Resource Manager-inkrementella läge **lämnar oförändrade** resurser som finns i resursgruppen men inte anges i mallen.

Men när du distribuerar om en befintlig resurs i inkrementella läge, är resultatet en annan. Ange alla egenskaper för resursen, inte bara de som du uppdaterar. Ett vanligt missförstånd är att tänka egenskaper som inte har angetts är lämnas oförändrade. Om du inte anger vissa egenskaper, tolkar uppdateringen som skriver över dessa värden i Resource Manager.

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
