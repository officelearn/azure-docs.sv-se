---
title: "Tagga Azure-resurser för logisk struktur | Microsoft Docs"
description: "Visar hur du lägger till taggar för att organisera Azure-resurser för fakturering och hantering."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 7ad53c7cfc49958abbe6200a892ba4e0c24c434c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Använd taggar för att organisera Azure-resurser

[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

## <a name="powershell"></a>PowerShell

Exemplen i den här artikeln kräver version 3.0 eller senare av Azure PowerShell. Om du inte har version 3.0 eller senare, [uppdatera din version](/powershell/azureps-cmdlets-docs/) med hjälp av PowerShell-galleriet eller installationsprogram för webbplattform.

Om du vill visa de befintliga taggarna för en *resursgrupp* använder du:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Skriptet returnerar följande format:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Och om du vill visa de befintliga taggarna för en *resurs som har ett angivet namn och en angiven resursgrupp* använder du:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Om du vill hämta *resursgrupper som har en specifik tagg* använder du:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name
```

Om du vill hämta *resurser som har en specifik tagg* använder du:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har befintliga taggar.

Om du vill lägga till taggar till en *resursgrupp utan befintliga taggar* använder du:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Om du vill lägga till taggar till en *resursgrupp som har befintliga taggar* hämtar du de befintliga taggarna, lägger till den nya taggen och tillämpar taggarna igen:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Om du vill lägga till taggar till en *resurs utan befintliga taggar* använder du:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Om du vill lägga till taggar till en *resurs som har befintliga taggar* använder du:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser *utan att behålla någon av de befintliga taggarna för resurserna* kan du använda följande skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och *behålla de befintliga taggarna på resurser som inte är dubbletter* kan du använda följande skript:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Om du vill ta bort alla taggar skickar du en tom hash-tabell:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```


## <a name="azure-cli"></a>Azure CLI

Om du vill visa de befintliga taggarna för en *resursgrupp* använder du:

```azurecli
az group show -n examplegroup --query tags
```

Skriptet returnerar följande format:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Eller så finns de befintliga taggarna för en *resurs som har en viss grupp namn, typ och resursen*, Använd:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

När slingor via en mängd resurser, kanske du vill visa resursen av resurs-ID. En komplett exempel visas nedan. Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```azurecli
az resource show --id <resource-id> --query tags
```

För att få resursgrupper som har en specifik tagg, Använd `az group list`:

```azurecli
az group list --tag Dept=IT
```

Alla resurser som har en viss tagg och värdet får använda `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har befintliga taggar.

Om du vill lägga till taggar till en *resursgrupp utan befintliga taggar* använder du:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Om du vill lägga till taggar till en *resurs utan befintliga taggar* använder du:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Hämta befintliga taggar om du vill lägga till taggar till en resurs som redan har taggar, formatera om värdet och återanvända befintliga och nya taggar: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser *utan att behålla någon av de befintliga taggarna för resurserna* kan du använda följande skript:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Tillämpa alla taggar från en resursgrupp till dess resurser och *behåller befintliga taggar på resurser*, använder du följande skript:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Mallar

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Azure portal och PowerShell använder båda de [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) i bakgrunden. Om du behöver integrera taggning till en annan miljö, kan du få taggar med hjälp av **hämta** på resurs-ID och uppdatera en uppsättning taggar med hjälp av en **korrigering** anropa.

## <a name="tags-and-billing"></a>Taggar och fakturering

Du kan använda taggar för att gruppera dina faktureringsinformation. Till exempel om du kör flera virtuella datorer i olika organisationer använda taggar om du vill gruppera användning av kostnadsställe. Du kan också använda taggar för att kategorisera kostnader av körningsmiljön, till exempel fakturering användning för virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure Resursanvändning och RateCard APIs](../billing/billing-usage-rate-card-overview.md) eller filen användning fil med kommaavgränsade värden (CSV). Du hämta filen från användning av [Azure kontoportalen](https://account.windowsazure.com/) eller [EA portal](https://ea.azure.com). Mer information om programmatisk åtkomst till faktureringsinformationen finns [få insikter om dina Microsoft Azure-resursförbrukning](../billing/billing-usage-rate-card-overview.md). REST-API: et finns [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

När du hämtar användning CSV för tjänster som stöder taggar med fakturering taggarna visas i den **taggar** kolumn. Mer information finns i [förstå fakturan för Microsoft Azure](../billing/billing-understand-your-bill.md).

![Se taggar i fakturering](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Nästa steg

* Du kan tillämpa begränsningar och konventioner över din prenumeration med hjälp av anpassade principer. En princip som du definierar kan kräva att alla resurser som har ett värde för en viss tagg. Mer information finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md).
* En introduktion till med hjälp av Azure PowerShell när du distribuerar resurser, se [med hjälp av Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
* En introduktion till med hjälp av Azure CLI när du distribuerar resurser, se [med hjälp av Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* En introduktion till med hjälp av portalen finns [hantera Azure-resurser med hjälp av Azure portal](resource-group-portal.md).  
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
