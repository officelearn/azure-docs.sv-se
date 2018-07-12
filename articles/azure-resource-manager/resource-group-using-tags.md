---
title: Tagga Azure-resurser för logisk struktur | Microsoft Docs
description: Lär dig mer om att lägga till taggar för att organisera Azure-resurser för fakturering och hantering.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: tomfitz
ms.openlocfilehash: 8c828bb49548adfdb02ed6fb1611eb405ebf4ff2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466268"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Använd taggar för att organisera Azure-resurser

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

Exemplen i den här artikeln kräver Azure PowerShell 6.0 eller senare. Om du inte har version 6.0 eller senare, [uppdatera din version](/powershell/azure/install-azurerm-ps).

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
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Och om du vill visa de befintliga taggarna för en *resurs som har ett angivet namn och en angiven resursgrupp* använder du:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Om du vill hämta *resursgrupper som har en specifik tagg* använder du:

```powershell
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Om du vill hämta *resurser som har en specifik tagg* använder du:

```powershell
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

Att hämta *resurser som har ett specifikt taggnamn*, Använd:

```powershell
(Get-AzureRmResource -TagName Dept).Name
```

Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har befintliga taggar.

Om du vill lägga till taggar till en *resursgrupp utan befintliga taggar* använder du:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Om du vill lägga till taggar till en *resursgrupp som har befintliga taggar* hämtar du de befintliga taggarna, lägger till den nya taggen och tillämpar taggarna igen:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
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
$r.Tags.Add("Status", "Approved") 
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser *utan att behålla någon av de befintliga taggarna för resurserna* kan du använda följande skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och *behålla de befintliga taggarna på resurser som inte är dubbletter* kan du använda följande skript:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
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

Eller, för att se de befintliga taggarna för en *resurs som har en viss grupp av namn, typ och resurs*, Använd:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

När slingor via en samling resurser, kanske du vill visa den resurs som resurs-ID. Ett komplett exempel visas nedan. Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```azurecli
az resource show --id <resource-id> --query tags
```

Hämta resursgrupper som har en specifik tagg med `az group list`:

```azurecli
az group list --tag Dept=IT
```

Hämta alla resurser som har en viss tagg och värdet `az resource list`:

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

Hämta de befintliga taggarna för att lägga till taggar till en resurs som redan har taggar, formatera om detta värde och återanvända befintliga och nya taggar: 

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

Tillämpa alla taggar från en resursgrupp på dess resurser och *behålla de befintliga taggarna på resurser*, Använd följande skript:

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

Azure-portalen och PowerShell som båda använder den [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) i bakgrunden. Om du vill integrera taggar till en annan miljö kan du få taggar med hjälp av **hämta** på resurs-ID och update-uppsättningen med taggar med hjälp av en **KORRIGERA** anropa.

## <a name="tags-and-billing"></a>Taggar och fakturering

Du kan använda taggar för att gruppera faktureringsinformation. Till exempel om du kör flera virtuella datorer i olika organisationer kan använda taggar arbetsgrupper per kostnadsställe. Du kan också använda taggar för att kategorisera kostnader genom att runtime-miljö, till exempel den fakturering användningen av virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure användning och RateCard APIs](../billing/billing-usage-rate-card-overview.md) eller användningsfil för fil med kommaavgränsade värden (CSV). Du ladda ned användningsfilen från den [Azure-kontoportalen](https://account.windowsazure.com/) eller [EA-portalen](https://ea.azure.com). Läs mer om programmatisk åtkomst till faktureringsinformation [insyn i din Microsoft Azure-resursförbrukning](../billing/billing-usage-rate-card-overview.md). REST API-åtgärder, se [Azure Billing REST API-referens](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

När du har hämtat användning CSV för tjänster som stöder taggar med fakturering, taggar visas i den **taggar** kolumn. Mer information finns i [förstå fakturan för Microsoft Azure](../billing/billing-understand-your-bill.md).

![Visa taggarna i fakturering](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Nästa steg

* Du kan använda begränsningar och konventioner på din prenumeration med hjälp av anpassade principer. En princip som du definierar kan kräva att alla resurser har ett värde för en viss tagg. Mer information finns i [vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)
* En introduktion till med hjälp av Azure PowerShell när du distribuerar resurser finns i [med hjälp av Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
* En introduktion till med hjälp av Azure CLI när du distribuerar resurser finns i [med Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).
* En introduktion till med hjälp av portalen finns i [med Azure portal för att hantera dina Azure-resurser](resource-group-portal.md).  
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
