---
title: Distributionsmodeller
description: Beskriver hur du anger om du vill använda ett fullständigt eller inkrementellt distributionsläge med Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460253"
---
# <a name="azure-resource-manager-deployment-modes"></a>Distributionslägen för Azure Resource Manager

När du distribuerar dina resurser anger du att distributionen antingen är en inkrementell uppdatering eller en fullständig uppdatering. Skillnaden mellan dessa två lägen är hur Resource Manager hanterar befintliga resurser i resursgruppen som inte finns i mallen.

För båda lägena försöker Resource Manager skapa alla resurser som anges i mallen. Om resursen redan finns i resursgruppen och dess inställningar är oförändrade, vidtas ingen åtgärd för den resursen. Om du ändrar egenskapsvärdena för en resurs uppdateras resursen med dessa nya värden. Om du försöker uppdatera platsen eller typen för en befintlig resurs misslyckas distributionen med ett fel. Distribuera i stället en ny resurs med den plats eller typ som du behöver.

Standardläget är inkrementellt.

## <a name="complete-mode"></a>Fullständigt läge

I fullständigt läge **tar** Resource Manager bort resurser som finns i resursgruppen men som inte anges i mallen.

Om mallen innehåller en resurs som inte distribueras på grund av [att villkoret](conditional-resource-deployment.md) utvärderas till false beror resultatet på vilken REST API-version du använder för att distribuera mallen. Om du använder en version tidigare än 2019-05-10 **tas resursen inte bort**. Med 2019-05-10 eller senare tas resursen **bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen.

Var försiktig med att använda komplett läge med [kopieringsöglor](copy-resources.md). Alla resurser som inte anges i mallen när du har löst kopieringsloopen tas bort.

Om du distribuerar till [mer än en resursgrupp i en mall](cross-resource-group-deployment.md)kan resurser i resursgruppen som anges i distributionsåtgärden tas bort. Resurser i de sekundära resursgrupperna tas inte bort.

Det finns vissa skillnader i hur resurstyper hanterar fullständiga lägesborttagningar. Överordnade resurser tas automatiskt bort när de inte finns i en mall som distribueras i fullständigt läge. Vissa underordnade resurser tas inte bort automatiskt när de inte finns i mallen. Dessa underordnade resurser tas dock bort om den överordnade resursen tas bort.

Om resursgruppen till exempel innehåller en DNS-zon (Microsoft.Network/dnsZones-resurstyp) och en CNAME-post (Microsoft.Network/dnsZones/CNAME-resurstyp), är DNS-zonen den överordnade resursen för CNAME-posten. Om du distribuerar med fullständigt läge och inte inkluderar DNS-zonen i mallen tas dns-zonen och CNAME-posten bort. Om du inkluderar DNS-zonen i mallen men inte tar med CNAME-posten tas CNAME-objektet inte bort.

En lista över hur resurstyper hanterar borttagning finns i [Borttagning av Azure-resurser för fullständig lägesdistribution .](complete-mode-deletion.md)

Om resursgruppen är [låst](../management/lock-resources.md)tar inte resurserna bort i fullständigt läge.

> [!NOTE]
> Endast mallar på rotnivå stöder hela distributionsläget. För [länkade eller kapslade mallar](linked-templates.md)måste du använda inkrementellt läge.
>
> [Distributioner på prenumerationsnivå](deploy-to-subscription.md) stöder inte fullständigt läge.
>
> För närvarande stöder portalen inte fullständigt läge.
>

## <a name="incremental-mode"></a>Inkrementellt läge

I inkrementellt läge lämnar Resource Manager **oförändrade** resurser som finns i resursgruppen men som inte anges i mallen. Resurser i mallen **läggs** till i resursgruppen.

> [!NOTE]
> När du distribuerar om en befintlig resurs i inkrementellt läge tillämpas alla egenskaper på nytt. **Egenskaperna läggs inte stegvis till**. Ett vanligt missförstånd är att tro att egenskaper som inte anges i mallen lämnas oförändrade. Om du inte anger vissa egenskaper tolkar Resource Manager distributionen som att skriva över dessa värden. Egenskaper som inte ingår i mallen återställs till standardvärdena. Ange alla värden som inte är standard för resursen, inte bara de som du uppdaterar. Resursdefinitionen i mallen innehåller alltid resursens slutliga tillstånd. Det kan inte representera en partiell uppdatering till en befintlig resurs.

## <a name="example-result"></a>Exempel på resultat

För att illustrera skillnaden mellan inkrementella och fullständiga lägen bör du tänka på följande scenario.

**Resursgruppen** innehåller:

* Resurs A
* Resurs B
* Resurs C

**Mallen** innehåller:

* Resurs A
* Resurs B
* Resurs D

När resursgruppen distribueras **i inkrementellt** läge har den:

* Resurs A
* Resurs B
* Resurs C
* Resurs D

När resurs C distribueras i **fullständigt** läge tas den bort. Resursgruppen har:

* Resurs A
* Resurs B
* Resurs D

## <a name="set-deployment-mode"></a>Ange distributionsläge

Om du vill ställa in distributionsläget `Mode` när du distribuerar med PowerShell använder du parametern.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Om du vill ange distributionsläge när `mode` du distribuerar med Azure CLI använder du parametern.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

I följande exempel visas en länkad mall inställd på inkrementellt distributionsläge:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar Resource Manager-mallar finns i [Skapa Azure Resource Manager-mallar](template-syntax.md).
* Mer information om hur du distribuerar resurser finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).
* Information om hur du visar åtgärderna för en resursprovider finns i [Azure REST API](/rest/api/).
