---
title: Tagga resurser, resurs grupper och prenumerationer för logisk organisation
description: Visar hur du använder taggar för att organisera Azure-resurser för fakturering och hantering.
ms.topic: conceptual
ms.date: 07/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ffcb4a0f2f5dc64b165fcdec03f7c3ced258cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086767"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Använd taggar för att ordna dina Azure-resurser och-hanterings hierarki

Du kan använda taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

Rekommendationer för hur du implementerar en taggnings strategi finns i avsnittet [Resource Naming and taggnings besluts guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> Taggnamn är inte Skift läges känsliga för åtgärder. En tagg med ett taggnamn, oavsett Skift läge, uppdateras eller hämtas. Resurs leverantören kan dock behålla det Skift läge som du anger för taggnamn. Du ser detta Skift läge i kostnads rapporter.
> 
> Tagg värden är Skift läges känsliga.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Nödvändig åtkomst

Om du vill lägga till taggar till en resurs måste du ha Skriv behörighet till resurs typen **Microsoft. Resources/Tagss** . Med rollen [tag Contributor](../../role-based-access-control/built-in-roles.md#tag-contributor) kan du tillämpa taggar på en entitet utan att ha åtkomst till själva entiteten. För närvarande kan inte rollen tag Contributor använda taggar för resurser eller resurs grupper via portalen. Den kan använda taggar för prenumerationer via portalen. Den har stöd för alla åtgärder för Taggar via PowerShell och REST API.  

[Deltagar](../../role-based-access-control/built-in-roles.md#contributor) rollen beviljar även den behörighet som krävs för att tillämpa taggar på alla entiteter. Använd deltagar rollen för den resursen om du bara vill använda taggar för en resurs typ. Om du till exempel vill använda taggar för virtuella datorer använder du den [virtuella dator deltagaren](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Använd Taggar

Azure PowerShell erbjuder två kommandon för att tillämpa Taggar- [New-AzTag](/powershell/module/az.resources/new-aztag) och [Update-AzTag](/powershell/module/az.resources/update-aztag). Du måste ha modulen AZ. Resources 1.12.0 eller senare. Du kan kontrol lera din version med `Get-Module Az.Resources` . Du kan installera modulen eller [installera Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 eller senare.

**New-AzTag** ersätter alla Taggar i resursen, resurs gruppen eller prenumerationen. När du anropar kommandot skickar du resurs-ID för den entitet som du vill tagga.

I följande exempel används en uppsättning taggar för ett lagrings konto:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

När kommandot har slutförts ser du till att resursen har två taggar.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Observera att de tidigare taggarna tas bort om du kör kommandot igen men den här gången med olika taggar.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Om du vill lägga till taggar till en resurs som redan har taggar, använder du **Update-AzTag**. Ange parametern **-operation** som ska **slås samman**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Observera att de två nya taggarna har lagts till i de två befintliga taggarna.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Varje taggnamn får bara ha ett värde. Om du anger ett nytt värde för en tagg ersätts det gamla värdet även om du använder sammanslagnings åtgärden. I följande exempel ändras status tag gen från normal till grön.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

När du anger parametern **-operation** **som ska ersättas ersätts**de befintliga taggarna med den nya uppsättningen taggar.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Endast de nya taggarna är kvar på resursen.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Samma kommandon fungerar också med resurs grupper eller prenumerationer. Du skickar in identifieraren för den resurs grupp eller prenumeration som du vill tagga.

Om du vill lägga till en ny uppsättning taggar i en resurs grupp använder du:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Om du vill uppdatera taggarna för en resurs grupp använder du:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Om du vill lägga till en ny uppsättning taggar i en prenumeration använder du:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Om du vill uppdatera taggarna för en prenumeration använder du:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Du kan ha mer än en resurs med samma namn i en resurs grupp. I så fall kan du ange varje resurs med följande kommandon:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Visa en lista över taggar

Om du vill hämta taggar för en resurs, resurs grupp eller prenumeration använder du kommandot [Get-AzTag](/powershell/module/az.resources/get-aztag) och skickar resurs-ID: t för entiteten.

Om du vill se taggarna för en resurs använder du:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Om du vill se taggarna för en resurs grupp använder du:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Om du vill se taggarna för en prenumeration använder du:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista efter tagg

Använd följande för att hämta resurser som har ett visst taggnamn och värde:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Använd följande om du vill hämta resurser som har ett visst taggnamn med valfritt tagg-värde:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Använd följande om du vill hämta resurs grupper som har ett visst taggnamn och värde:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Ta bort taggar

Om du vill ta bort vissa Taggar använder du **Update-AzTag** och set **-operation** för att **ta bort**. Skicka in de taggar som du vill ta bort.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

De angivna taggarna tas bort.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Om du vill ta bort alla Taggar använder du kommandot [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Använd Taggar

När du lägger till taggar till en resurs grupp eller resurs kan du antingen skriva över befintliga taggar eller lägga till nya taggar i befintliga taggar.

Om du vill skriva över taggarna på en resurs använder du:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Om du vill lägga till en tagg till de befintliga taggarna på en resurs använder du:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Om du vill skriva över de befintliga taggarna i en resurs grupp använder du:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Om du vill lägga till en tagg till de befintliga taggarna i en resurs grupp använder du:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

För närvarande har Azure CLI inget kommando för att tillämpa taggar på prenumerationer. Du kan dock använda CLI för att distribuera en ARM-mall som använder taggarna för en prenumeration. Se [använda taggar för resurs grupper eller prenumerationer](#apply-tags-to-resource-groups-or-subscriptions).

### <a name="list-tags"></a>Visa en lista över taggar

Om du vill se de befintliga taggarna för en resurs använder du:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Om du vill visa de befintliga taggarna för en resursgrupp använder du:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Skriptet returnerar följande format:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>Lista efter tagg

Om du vill hämta alla resurser som har en viss tagg och värde använder du `az resource list` :

```azurecli-interactive
az resource list --tag Dept=Finance
```

Om du vill hämta resurs grupper som har en angiven tagg använder du `az group list` :

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Hantera utrymmen

Om taggnamn eller värden innehåller blank steg måste du ta några extra steg. 

`--tags`Parametrarna i Azure CLI kan användas för att acceptera en sträng som består av en sträng mat ris. I följande exempel skrivs taggarna i en resurs grupp över och taggarna innehåller blank steg och bindestreck: 

```azurecli-interactive
TAGS=("Cost Center=Finance-1222" "Location=West US")
az group update --name examplegroup --tags "${TAGS[@]}"
```

Du kan använda samma syntax när du skapar eller uppdaterar en resurs grupp eller resurser med hjälp av- `--tags` parametern.

Om du vill uppdatera taggarna med hjälp av `--set` parametern måste du skicka nyckeln och värdet som en sträng. I följande exempel läggs en enskild tagg till i en resurs grupp:

```azurecli-interactive
TAG="Cost Center='Account-56'"
az group update --name examplegroup --set tags."$TAG"
```

I det här fallet är taggvärde markerat med enkla citat tecken eftersom värdet har ett bindestreck.

Du kan också behöva använda taggar för många resurser. I följande exempel används alla Taggar från en resurs grupp till dess resurser när taggarna kan innehålla blank steg:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Mallar

Du kan tagga resurser, resurs grupper och prenumerationer under distributionen med en Resource Manager-mall.

### <a name="apply-values"></a>Använd värden

I följande exempel distribueras ett lagrings konto med tre taggar. Två av taggarna ( `Dept` och `Environment` ) är inställda på litterala värden. En tagg ( `LastDeployed` ) har angetts till en parameter som används som standard för det aktuella datumet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Använd ett objekt

Du kan definiera en objektparameter som lagrar flera taggar, och använda det objektet för taggelementet. Den här metoden ger större flexibilitet än föregående exempel eftersom objektet kan ha olika egenskaper. Varje egenskap i objektet blir en separat tagg för resursen. I följande exempel finns en parameter med namnet `tagValues` som används för taggelementet.

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

### <a name="apply-a-json-string"></a>Använda en JSON-sträng

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

Använd funktionen [resourceGroup ()](../templates/template-functions-resource.md#resourcegroup) om du vill använda taggar från en resurs grupp till en resurs. När du hämtar taggvärde använder du `tags[tag-name]` syntaxen i stället för `tags.tag-name` syntaxen, eftersom vissa tecken inte tolkas korrekt i punkt notationen.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Använd taggar för resurs grupper eller prenumerationer

Du kan lägga till taggar till en resurs grupp eller prenumeration genom att distribuera resurs typen **Microsoft. Resources/Tags** . Taggarna tillämpas på mål resurs gruppen eller prenumerationen för distributionen. Varje gången du distribuerar mallen ersätter du de taggar som du har använt tidigare.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Använd antingen PowerShell eller Azure CLI om du vill använda taggarna i en resurs grupp. Distribuera till den resurs grupp som du vill tagga.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Använd antingen PowerShell eller Azure CLI om du vill använda taggarna för en prenumeration. Distribuera till den prenumeration som du vill tagga.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Mer information om distribution av prenumerationer finns i [skapa resurs grupper och resurser på prenumerations nivå](../templates/deploy-to-subscription.md).

Följande mall lägger till taggar från ett objekt till antingen en resurs grupp eller en prenumeration.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portalen

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST-API

Om du vill arbeta med Taggar via Azure-REST API använder du:

* [Taggar – skapa eller uppdatera i omfång](/rest/api/resources/tags/createorupdateatscope) (placerings åtgärd)
* [Taggar – uppdatera i omfattning](/rest/api/resources/tags/updateatscope) (korrigerings åtgärd)
* [Taggar – Hämta i omfång](/rest/api/resources/tags/getatscope) (Get-åtgärd)
* [Taggar – ta bort vid omfång](/rest/api/resources/tags/deleteatscope) (borttagnings åtgärd)

## <a name="inherit-tags"></a>Ärv Taggar

Taggar som används för resurs gruppen eller prenumerationen ärvs inte av resurserna. Information om hur du använder taggar från en prenumeration eller resurs grupp för resurserna finns i [Azure policies-Taggar](tag-policies.md).

## <a name="tags-and-billing"></a>Taggar och fakturering

Du kan till exempel använda taggar för att gruppera faktureringsinformation. Om du till exempel har flera virtuella datorer för olika organisationer kan du använda taggar för att gruppera användningen efter kostnadsställe. Du kan också använda taggar för att kategorisera kostnader efter körningsmiljö, till exempel användningen (som faktureras) för virtuella datorer som körs i produktionsmiljö.

Du kan hämta information om Taggar via [API: er för Azure-resursanvändning och Rate-kort](../../cost-management-billing/manage/usage-rate-card-overview.md) eller filen med kommaavgränsade värden (CSV). Du hämtar användnings filen från Azure Portal. Mer information finns i [Hämta eller Visa din fakturerings faktura för Azure och användnings data per dag](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). När du laddar ned användnings filen från Azure-kontocenter väljer du **version 2**. För tjänster som stöder taggar med fakturering visas taggarna i kolumnen **taggar** .

REST API åtgärder finns i [referens för Azure-fakturerings REST API](/rest/api/billing/).

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för taggar:

* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
* Varje resurs, resurs grupp och prenumeration kan innehålla högst 50 taggnamn/värdepar. Om du behöver använda fler taggar än det högsta tillåtna antalet använder du en JSON-sträng för värdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. En resurs grupp eller prenumeration kan innehålla många resurser som var och en har 50 tagg namn/värde-par.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Det går inte att använda taggar för klassiska resurser som Cloud Services.
* Tagg namn får inte innehålla följande tecken:,,,,, `<` `>` `%` `&` `\` `?``/`

   > [!NOTE]
   > För närvarande är Azure DNS zoner och Traffic Manager-tjänster inte heller att använda blank steg i taggen.
   >
   > Azures front dörr stöder inte användning av `#` i taggnamn.
   >
   > Azure Automation och Azure CDN bara stödja 15 taggar på resurser.

## <a name="next-steps"></a>Nästa steg

* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
* Rekommendationer för hur du implementerar en taggnings strategi finns i avsnittet [Resource Naming and taggnings besluts guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
