---
title: Tagga resurser för logisk organisation
description: Visar hur du använder taggar för att organisera Azure-resurser för fakturering och hantering.
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: c0a34204b5eb7080c6444e69def9d82d0193783b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850609"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Använd taggar för att organisera Azure-resurser

Du kan använda taggar till dina Azure-resurser för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Taggarna gör att du kan hämta relaterade resurser från olika resursgrupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Din taxonomi bör överväga en strategi för att tagga en egen metadata utöver en strategi för automatisk märkning som minskar belastningen på användare och ökar noggrannheten.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för taggar:

* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
* Varje resurs eller resurs grupp får innehålla högst 50 taggnamn/värdepar. Om du behöver använda fler taggar än det högsta tillåtna antalet använder du en JSON-sträng för värdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. En resurs grupp kan innehålla många resurser som var och en har 50 tagg namn/värde-par.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Generaliserade virtuella datorer stöder inte taggar.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Det går inte att använda taggar för klassiska resurser som Cloud Services.
* Taggnamn får inte innehålla följande tecken: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > För närvarande Azure DNS zoner och Traffic Manager-tjänster inte heller att använda blank steg i taggen. 

## <a name="required-access"></a>Nödvändig åtkomst

Om du vill använda taggar för resurser måste användaren ha Skriv behörighet till den resurs typen. Använd [deltagar](../role-based-access-control/built-in-roles.md#contributor) rollen om du vill använda taggar för alla resurs typer. Använd deltagar rollen för den resursen om du bara vill använda taggar för en resurs typ. Om du till exempel vill använda taggar för virtuella datorer använder du den [virtuella dator deltagaren](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Policy

Du kan använda [Azure policy](../governance/policy/overview.md) för att tillämpa taggnings regler och konventioner. Genom att skapa en princip undviker du syftet med resurser som distribueras till din prenumeration som inte följer de förväntade taggarna för din organisation. I stället för att manuellt tillämpa taggar eller söka efter resurser som inte är kompatibla, kan du skapa en princip som automatiskt tillämpar de taggar som krävs under distributionen. Taggar kan nu också tillämpas på befintliga resurser med den nya [ändra](../governance/policy/concepts/effects.md#modify) -effekt och en [reparations uppgift](../governance/policy/how-to/remediate-resources.md). I följande avsnitt visas exempel principer för taggar.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

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

Om du vill hämta *resurser som har ett visst taggnamn*använder du:

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

Använd följande skript om du vill tillämpa alla Taggar från en resurs grupp på dess resurser och *inte behålla befintliga taggar på resurserna*:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Använd följande skript om du vill tillämpa alla Taggar från en resurs grupp på dess resurser och *behålla befintliga taggar för resurser som inte är dubbletter*:

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

Eller, om du vill visa de befintliga taggarna för en *resurs som har ett angivet namn, typ och resurs grupp*, använder du:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

När du går igenom en samling resurser kanske du vill visa resursen efter resurs-ID. Ett fullständigt exempel visas senare i den här artikeln. Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```azurecli
az resource show --id <resource-id> --query tags
```

Använd `az group list`för att hämta resurs grupper som har en angiven tagg:

```azurecli
az group list --tag Dept=IT
```

Använd `az resource list`för att hämta alla resurser som har en viss tagg och ett visst värde:

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

Om du vill lägga till taggar till en resurs som redan har taggar, hämtar du de befintliga taggarna, formaterar det värdet och tillämpar de befintliga och nya taggarna igen:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Använd följande skript om du vill tillämpa alla Taggar från en resurs grupp på dess resurser och *inte behålla befintliga taggar på resurserna*:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Använd följande skript om du vill tillämpa alla Taggar från en resurs grupp på dess resurser och *behålla befintliga taggar för resurser*:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags -o json)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Mallar

Om du vill tagga en resurs under distributionen lägger du till `tags`-elementet till den resurs som du distribuerar. Ange namn och värde för taggen.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Lägga till ett literalvärde till taggnamnet

I följande exempel visas ett lagringskonto med två taggar (`Dept` och `Environment`) som har angetts till literalvärden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Om du vill ange en tagg till ett datetime-värde använder du [funktionen utcNow](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Lägga till ett objekt till taggelementet

Du kan definiera en objektparameter som lagrar flera taggar, och använda det objektet för taggelementet. Varje egenskap i objektet blir en separat tagg för resursen. I följande exempel finns en parameter med namnet `tagValues` som används för taggelementet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Lägga till en JSON-sträng till taggnamnet

Du kan lagra flera värden i en enskild tagg genom att använda en JSON-sträng som representerar värdena. Hela JSON-strängen lagras som en tagg som inte får innehålla fler än 256 tecken. I följande exempel finns en enskild tagg med namnet `CostCenter` som innehåller flera värden från en JSON-sträng:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Använd taggar från resurs gruppen

Använd funktionen [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) för att lägga till taggar från en resurs grupp till en resurs. När du hämtar taggnamnet använder du `tags.[tag-name]` syntax i stället för `tags.tag-name` syntax, eftersom vissa tecken inte tolkas korrekt i punkt notationen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Azure Portal och PowerShell använder båda [Resource Manager-REST API](https://docs.microsoft.com/rest/api/resources/) i bakgrunden. Om du behöver integrera taggning i en annan miljö kan du hämta Taggar genom att använda **Hämta** i resurs-ID och uppdatera uppsättningen med Taggar med hjälp av ett **korrigerings** anrop.

## <a name="tags-and-billing"></a>Taggar och fakturering

Du kan till exempel använda taggar för att gruppera faktureringsinformation. Om du till exempel har flera virtuella datorer för olika organisationer kan du använda taggar för att gruppera användningen efter kostnadsställe. Du kan också använda taggar för att kategorisera kostnader efter körningsmiljö, till exempel användningen (som faktureras) för virtuella datorer som körs i produktionsmiljö.

Du kan hämta information om Taggar via [Azures resursanvändning och ratecard-API: er](../billing/billing-usage-rate-card-overview.md) eller CSV-fil (fil med kommaavgränsade värden). Du hämtar användnings filen från [Azure-kontocenter](https://account.azure.com/Subscriptions) eller Azure Portal. Mer information finns i [Hämta eller Visa din fakturerings faktura för Azure och användnings data per dag](../billing/billing-download-azure-invoice-daily-usage-date.md). När du laddar ned användnings filen från Azure-kontocenter väljer du **version 2**. För tjänster som stöder taggar med fakturering visas taggarna i kolumnen **taggar** .

REST API åtgärder finns i [referens för Azure-fakturerings REST API](/rest/api/billing/).

## <a name="next-steps"></a>Nästa steg

* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
* En introduktion till att använda portalen finns i [använda Azure Portal för att hantera dina Azure-resurser](manage-resource-groups-portal.md).  
