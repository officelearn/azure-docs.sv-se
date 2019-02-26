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
ms.date: 11/20/2018
ms.author: tomfitz
ms.openlocfilehash: 87b6dcb007eaea140b9655c248fdfb356cbc9b5f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817328"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Använd taggar för att organisera Azure-resurser

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Om du vill lägga till taggar i resurser, måste användaren ha skrivbehörighet till den resurstypen. Om du vill lägga till taggar i alla typer av resurser, använder den [deltagare](../role-based-access-control/built-in-roles.md#contributor) roll. Om du vill lägga till taggar i endast en resurstyp, använder du deltagarrollen för den resursen. Till exempel om du vill lägga till taggar till virtuella datorer, använda den [virtuell Datordeltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Principer

Du kan använda [Azure Policy](../governance/policy/overview.md) framtvinga taggning regler och konventioner. Genom att skapa en princip kan undvika du scenariot resurser som distribueras till din prenumeration som inte är kompatibla med de förväntade taggarna för din organisation. I stället för att manuellt använda taggar eller söka efter resurser som inte är kompatibla kan skapa du en princip som använder automatiskt de nödvändiga taggarna under distributionen. Avsnittet nedan visar exempel på principer för taggar.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill visa de befintliga taggarna för en *resursgrupp* använder du:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Skriptet returnerar följande format:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Och om du vill visa de befintliga taggarna för en *resurs som har ett angivet namn och en angiven resursgrupp* använder du:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Om du vill hämta *resursgrupper som har en specifik tagg* använder du:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Om du vill hämta *resurser som har en specifik tagg* använder du:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Att hämta *resurser som har ett specifikt taggnamn*, Använd:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har befintliga taggar.

Om du vill lägga till taggar till en *resursgrupp utan befintliga taggar* använder du:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Om du vill lägga till taggar till en *resursgrupp som har befintliga taggar* hämtar du de befintliga taggarna, lägger till den nya taggen och tillämpar taggarna igen:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Om du vill lägga till taggar till en *resurs utan befintliga taggar* använder du:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Om du vill lägga till taggar till en *resurs som har befintliga taggar* använder du:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser *utan att behålla någon av de befintliga taggarna för resurserna* kan du använda följande skript:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och *behålla de befintliga taggarna på resurser som inte är dubbletter* kan du använda följande skript:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Om du vill ta bort alla taggar skickar du en tom hash-tabell:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
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

Du kan använda taggar för att gruppera faktureringsinformation. Till exempel om du använder flera virtuella datorer i olika organisationer kan använda taggar arbetsgrupper per kostnadsställe. Du kan också använda taggar för att kategorisera kostnader genom att runtime-miljö, till exempel den fakturering användningen av virtuella datorer som körs i produktionsmiljön.

Du kan hämta information om taggar via den [Azure användning och RateCard APIs](../billing/billing-usage-rate-card-overview.md) eller användningsfil för fil med kommaavgränsade värden (CSV). Du ladda ned användningsfilen från den [Azure Kontocenter](https://account.azure.com/Subscriptions) eller Azure-portalen. Mer information finns i [ladda ned eller visa din Azure-fakturering och daglig användningsdata](../billing/billing-download-azure-invoice-daily-usage-date.md). När du laddar ned användningsfilen från Azure Kontocenter, Välj **Version 2**. För tjänster som stöder taggar med fakturering, taggarna som visas i den **taggar** kolumn.

REST API-åtgärder, se [Azure Billing REST API-referens](/rest/api/billing/).

## <a name="next-steps"></a>Nästa steg

* Inte alla resurstyper stöder taggar. För att avgöra om du kan använda en tagg för en resurstyp, se [tagga stöd för Azure-resurser](tag-support.md).
* En introduktion till med hjälp av portalen finns i [med Azure portal för att hantera dina Azure-resurser](manage-resource-groups-portal.md).  
