---
title: Azure Resource Manager distributions lägen | Microsoft Docs
description: Beskriver hur du anger om du vill använda ett fullständigt eller stegvis distributions läge med Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 762b0e74e8da20d1b48703385853765d5cc643af
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953236"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager distributions lägen

När du distribuerar dina resurser anger du att distributionen är antingen en stegvis uppdatering eller en fullständig uppdatering.  Skillnaden mellan dessa två lägen är hur resurs hanteraren hanterar befintliga resurser i resurs gruppen som inte finns i mallen. Standard läget är stegvist.

I båda lägena försöker Resource Manager att skapa alla resurser som anges i mallen. Om resursen redan finns i resurs gruppen och dess inställningar är oförändrade vidtas ingen åtgärd för resursen. Om du ändrar egenskaps värden för en resurs uppdateras resursen med de nya värdena. Om du försöker uppdatera platsen eller typen för en befintlig resurs, Miss lyckas distributionen med ett fel. Distribuera i stället en ny resurs med den plats eller typ som du behöver.

## <a name="complete-mode"></a>Fullständigt läge

I fullständigt läge tar Resource Manager **bort** resurser som finns i resurs gruppen men som inte har angetts i mallen.

Om din mall innehåller en resurs som inte har distribuerats eftersom [villkoret](conditional-resource-deployment.md) utvärderas till false beror resultatet på vilken REST API version som du använder för att distribuera mallen. Om du använder en tidigare version än 2019-05-10 **tas inte resursen bort**. Med 2019-05-10 eller senare **tas resursen bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen.

Var försiktig med att använda fullständigt läge med [kopierings slingor](resource-group-create-multiple.md). Alla resurser som inte är angivna i mallen när du har löst kopierings slingen tas bort.

Om du distribuerar till [fler än en resurs grupp i en mall](resource-manager-cross-resource-group-deployment.md)är resurserna i resurs gruppen som anges i distributions åtgärden tillgängliga för borttagning. Resurser i de sekundära resurs grupperna tas inte bort.

Det finns vissa skillnader i hur resurs typer hanterar fullständigt läge borttagningar. Överordnade resurser tas bort automatiskt när de inte finns i en mall som distribueras i fullständigt läge. Vissa underordnade resurser tas inte bort automatiskt när de inte finns i mallen. De underordnade resurserna tas dock bort om den överordnade resursen tas bort. 

Om din resurs grupp till exempel innehåller en DNS-zon (Microsoft. Network/dnsZones Resource Type) och en CNAME-post (Microsoft. Network/dnsZones/CNAME resurs typ), är DNS-zonen den överordnade resursen för CNAME-posten. Om du distribuerar med slutfört läge och inte inkluderar DNS-zonen i din mall, raderas DNS-zonen och CNAME-posten båda. Om du inkluderar DNS-zonen i mallen men inte tar med CNAME-posten, raderas inte CNAME. 

En lista över hur resurs typer hanterar borttagning finns i [ta bort Azure-resurser för fullständiga läges distributioner](complete-mode-deletion.md).

Om resurs gruppen är [låst](resource-group-lock-resources.md)tas inte resurserna bort om du slutför läget.

> [!NOTE]
> Endast mallar på rotnivå stöder hela distributions läget. För [länkade eller kapslade mallar](resource-group-linked-templates.md)måste du använda stegvist läge. 
>
> [Distributioner på prenumerations nivå](deploy-to-subscription.md) stöder inte slutfört läge.
>
> För närvarande stöder inte portalen slutfört läge.
>

## <a name="incremental-mode"></a>Stegvist läge

I stegvist läge lämnar Resource Manager **oförändrade** resurser som finns i resurs gruppen men som inte anges i mallen.

Men när du distribuerar om en befintlig resurs i stegvist läge är resultatet en annan. Ange alla egenskaper för resursen, inte bara de som du uppdaterar. En vanlig förståelse är att betrakta egenskaper som inte har angetts lämnas oförändrade. Om du inte anger vissa egenskaper tolkar Resource Manager uppdateringen som att skriva över dessa värden.

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

Använd parametern `Mode` om du vill ange distributions läge vid distribution med PowerShell.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Om du vill ange distributions läget när du distribuerar med Azure CLI använder du parametern `mode`.

```azurecli-interactive
az group deployment create \
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

* Information om hur du skapar Resource Manager-mallar finns i [redigera Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Mer information om hur du distribuerar resurser finns i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).
* Information om hur du visar åtgärder för en resurs leverantör finns i [Azure REST API](/rest/api/).
