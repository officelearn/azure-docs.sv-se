---
title: Distributionsmodeller
description: Beskriver hur du anger om du vill använda ett fullständigt eller stegvis distributions läge med Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460253"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager distributions lägen

När du distribuerar dina resurser anger du att distributionen är antingen en stegvis uppdatering eller en fullständig uppdatering. Skillnaden mellan dessa två lägen är hur resurs hanteraren hanterar befintliga resurser i resurs gruppen som inte finns i mallen.

I båda lägena försöker Resource Manager att skapa alla resurser som anges i mallen. Om resursen redan finns i resurs gruppen och dess inställningar är oförändrade vidtas ingen åtgärd för resursen. Om du ändrar egenskaps värden för en resurs uppdateras resursen med de nya värdena. Om du försöker uppdatera platsen eller typen för en befintlig resurs, Miss lyckas distributionen med ett fel. Distribuera i stället en ny resurs med den plats eller typ som du behöver.

Standard läget är stegvist.

## <a name="complete-mode"></a>Fullständigt läge

I fullständigt läge tar Resource Manager **bort** resurser som finns i resurs gruppen men som inte har angetts i mallen.

Om din mall innehåller en resurs som inte har distribuerats eftersom [villkoret](conditional-resource-deployment.md) utvärderas till false beror resultatet på vilken REST API version som du använder för att distribuera mallen. Om du använder en tidigare version än 2019-05-10 **tas inte resursen bort**. Med 2019-05-10 eller senare **tas resursen bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen.

Var försiktig med att använda fullständigt läge med [kopierings slingor](copy-resources.md). Alla resurser som inte är angivna i mallen när du har löst kopierings slingen tas bort.

Om du distribuerar till [fler än en resurs grupp i en mall](cross-resource-group-deployment.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Det finns vissa skillnader i hur resurs typer hanterar fullständigt läge borttagningar. Överordnade resurser tas bort automatiskt när de inte finns i en mall som distribueras i fullständigt läge. Vissa underordnade resurser tas inte bort automatiskt när de inte finns i mallen. De underordnade resurserna tas dock bort om den överordnade resursen tas bort.

Om din resurs grupp till exempel innehåller en DNS-zon (Microsoft. Network/dnsZones Resource Type) och en CNAME-post (Microsoft. Network/dnsZones/CNAME resurs typ), är DNS-zonen den överordnade resursen för CNAME-posten. Om du distribuerar med slutfört läge och inte inkluderar DNS-zonen i din mall, raderas DNS-zonen och CNAME-posten båda. Om du inkluderar DNS-zonen i mallen men inte tar med CNAME-posten, raderas inte CNAME.

En lista över hur resurs typer hanterar borttagning finns i [ta bort Azure-resurser för fullständiga läges distributioner](complete-mode-deletion.md).

Om resurs gruppen är [låst](../management/lock-resources.md)tas inte resurserna bort om du slutför läget.

> [!NOTE]
> Endast mallar på rotnivå stöder hela distributions läget. För [länkade eller kapslade mallar](linked-templates.md)måste du använda stegvist läge.
>
> [Distributioner på prenumerations nivå](deploy-to-subscription.md) stöder inte slutfört läge.
>
> För närvarande stöder inte portalen slutfört läge.
>

## <a name="incremental-mode"></a>Stegvist läge

I stegvist läge lämnar Resource Manager **oförändrade** resurser som finns i resurs gruppen men som inte anges i mallen. Resurser i mallen **läggs** till i resurs gruppen.

> [!NOTE]
> När du omdistribuerar en befintlig resurs i stegvist läge tillämpas alla egenskaper igen. **Egenskaperna läggs inte till stegvis**. En vanlig förståelse är att betrakta egenskaper som inte är angivna i mallen oförändrade. Om du inte anger vissa egenskaper tolkar Resource Manager distributionen som att skriva över dessa värden. Egenskaper som inte ingår i mallen återställs till standardvärdena. Ange alla värden som inte är standardvärden för resursen, inte bara de som du uppdaterar. Resurs definitionen i mallen innehåller alltid slut tillstånd för resursen. Den kan inte representera en delvis uppdatering av en befintlig resurs.

## <a name="example-result"></a>Exempel resultat

För att illustrera skillnaden mellan stegvisa och fullständiga lägen, bör du tänka på följande scenario.

**Resurs gruppen** innehåller:

* Resurs A
* Resurs B
* Resurs C

**Mallen** innehåller:

* Resurs A
* Resurs B
* Resurs D

När den distribueras i **stegvist** läge har resurs gruppen:

* Resurs A
* Resurs B
* Resurs C
* Resurs D

När det distribueras i **fullständigt** läge tas resurs C bort. Resurs gruppen har:

* Resurs A
* Resurs B
* Resurs D

## <a name="set-deployment-mode"></a>Ange distributions läge

Använd `Mode` parametern för att ange distributions läget vid distribution med PowerShell.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Använd `mode` parametern för att ange distributions läget när du distribuerar med Azure CLI.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

I följande exempel visas en länkad mall som är inställd på stegvis distributions läge:

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

* Information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar](template-syntax.md).
* Mer information om hur du distribuerar resurser finns i [distribuera ett program med Azure Resource Manager-mall](deploy-powershell.md).
* Information om hur du visar åtgärder för en resurs leverantör finns i [Azure REST API](/rest/api/).
