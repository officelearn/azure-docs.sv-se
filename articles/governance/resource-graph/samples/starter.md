---
title: Exempel på startfrågor
description: Använd Azure Resource Graph för att köra vissa startfrågor såsom att räkna resurser, ordna resurser eller efter en viss tagg.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: 431c2d5066421efdfa4725d39fc40169b80d9cb2
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431523"
---
# <a name="starter-resource-graph-queries"></a>Startfrågor för Azure Resource Graph

Det första steget mot att förstå frågor med Azure Resource Graph är en grundläggande förståelse för [frågespråket](../concepts/query-language.md). Om du inte redan är bekant med [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) rekommenderar vi att du går igenom grunderna så att du förstår hur man skapar begäranden för de resurser som du letar efter.

Vi går igenom följande startfrågor:

> [!div class="checklist"]
> - [Antal Azure-resurser](#count-resources)
> - [Räkna nyckel valvs resurser](#count-keyvaults)
> - [Lista över resurser sorterade efter namn](#list-resources)
> - [Visning av alla virtuella datorer sorterade efter namn i fallande ordning](#show-vms)
> - [Visning av de första fem virtuella datorerna efter namn och OS-typ](#show-sorted)
> - [Antal virtuella datorer efter OS-typ](#count-os)
> - [Visning av resurser med lagring](#show-storage)
> - [Lista över alla offentliga IP-adresser](#list-publicip)
> - [Antal resurser som har IP-adresser konfigurerade efter prenumeration](#count-resources-by-ip)
> - [Lista över resurser med ett specifikt tagg-värde](#list-tag)
> - [Lista över alla lagringskonton med ett specifikt taggvärde](#list-specific-tag)
> - [Visa alias för en virtuell dator resurs](#show-aliases)
> - [Visa distinkta värden för ett visst alias](#distinct-alias-values)
> - [Visa associerade nätverks säkerhets grupper](#unassociated-nsgs)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="language-support"></a>Språkstöd

Azure CLI (via ett tillägg) och Azure PowerShell (via en modul) har stöd för Azure Resource Graph. Kontrollera att din miljö är redo innan du kör någon av nedanstående frågor. Se [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) för anvisningar om hur du installerar och validerar din valda gränssnittsmiljö.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Count Azure-resurser

Den här frågan returnerar antalet Azure-resurser som finns i de prenumerationer som du har åtkomst till. Det är också en bra fråga för att verifiera att ditt gränssnittval har lämpliga Azure Resource Graph-komponenter installerade och fungerar korrekt.

```kusto
Resources
| summarize count()
```

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

## <a name="a-namecount-keyvaultscount-key-vault-resources"></a><a name="count-keyvaults">Count Key Vault-resurser

Den här frågan använder `count` i stället för `summarize` för att räkna antalet returnerade poster. Endast nyckel valv ingår i antalet.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| count
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | count"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | count"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>List resurser sorterade efter namn

Frågan returnerar alla typer av resurser men bara egenskaperna **name** (namn), **type** (typ) och **location** (plats). Den använder `order by` för att sortera egenskaperna efter egenskapen **name** (namn) i stigande (`asc`) ordning.

```kusto
Resources
| project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Show alla virtuella datorer ordnade efter namn i fallande ordning

För att bara lista virtuella datorer (som är typen `Microsoft.Compute/virtualMachines`) kan vi matcha egenskapen **type** (typ) i resultatet. Som för den föregående frågan, ändrar `desc` `order by` till att vara fallande. Tecknet `=~` i typmatchningen talar om för Resource Graph att Resource Graph ska vara skiftlägesokänsligt.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Show de första fem virtuella datorerna efter namn och deras OS-typ

Den här frågan använder `top` för att bara hämta fem matchande poster som sorteras efter namn. Typen av Azure-resurs är `Microsoft.Compute/virtualMachines`. `project` talar om Azure Resource Graph vilka egenskaper som ska inkluderas.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Count virtuella datorer efter OS-typ

Vi bygger vidare på den föregående frågan och begränsar fortfarande efter Azure-resurstyp `Microsoft.Compute/virtualMachines` men inte längre efter antalet returnerade poster.
I stället använder vi `summarize` och `count()` för att definiera hur värdena ska grupperas och aggregeras efter egenskap, som i det här exemplet är `properties.storageProfile.osDisk.osType`. Ett exempel på hur denna sträng ser ut i det fullständiga objektet visas i [Utforska resurser – Identifiering av virtuell maskin](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Ett annat sätt att skriva samma fråga är att `extend` en egenskap och ge den ett tillfälligt namn för användning i frågan, i det här fallet **os**. **os** används då av `summarize` och `count()` som i föregående exempel.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Tänk på att medan `=~` tillåter skiftlägesokänslig matchning, kräver användning av egenskaper (som **properties.storageProfile.osDisk.osType**) i frågan att skiftläget är korrekt. Om egenskapen är i fel skiftläge kan den fortfarande returnera ett värde men grupperingen eller sammanfattningen blir felaktig.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Show resurser som innehåller lagring

I stället för att explicit definiera den typ som ska matchas, hittar den här exempelfrågan alla Azure-resurser som `contains` ordet **storage** (lagring).

```kusto
Resources
| where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>List alla offentliga IP-adresser

Hittar på ett liknande sätt som för den föregående frågan allt som är en typ med ordet **publicIPAddresses**.
Den här frågan expanderar på mönstret så att endast resultat visas där **Properties. ipaddress**
 @ no__t-2, för att endast returnera **egenskaperna. IPAddress**och för att `limit` resultaten överst
100. Du kan behöva hoppa över citattecknen beroende på valt gränssnitt.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Count resurser som har IP-adresser som kon figurer ATS av prenumerationen

Om vi använder den föregående exempelfrågan och lägger till `summarize` och `count()`, kan vi få en lista efter prenumeration på resurser med konfigurerade IP-adresser.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>List resurser med ett visst taggvärde

Vi kan begränsa resultaten med andra egenskaper än Azure-resurstyp, till exempel en tagg. I det här exemplet filtrerar vi för Azure-resurser med ett taggnamn innehållande **environment** som har värdet **internal**.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

Om du även vill ange vilka taggar som resursen har och deras värden lägger du egenskapen **taggs** (taggar) i `project`-nyckelordet.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>List alla lagrings konton med ett visst tagg värde

Kombinera filterfunktionerna för exemplet ovan och filtrera Azure-resurstyp efter egenskapen **type** (typ). Den här frågan begränsar även sökningen för specifika typer av Azure-resurser med ett visst taggnamn och -värde.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> I det här exemplet används `==` för matchning istället för villkorliga `=~`. `==` är en skiftlägeskänslig matchning.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Show alias för en virtuell dator resurs

[Azure policy alias](../../policy/concepts/definition-structure.md#aliases) används av Azure policy för att hantera resursernas efterlevnad. Azure Resource Graph kan returnera _alias_ för en resurs typ. Dessa värden är användbara för att jämföra det aktuella värdet för alias när du skapar en anpassad princip definition. Matrisen _alias_ anges inte som standard i resultatet av en fråga. Använd `project aliases` om du vill lägga till den explicit i resultatet.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a>@no__t distinkta värden för ett visst alias

Att se värdet för alias på en enskild resurs är användbart, men det visar inte det sanna värdet för att använda Azure Resource Graph för att fråga mellan prenumerationer. Det här exemplet tittar på alla värden i ett visst alias och returnerar de distinkta värdena.

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="a-nameunassociated-nsgsshow-unassociated-network-security-groups"></a><a name="unassociated-nsgs"/>Show nätverks säkerhets grupper som inte associerats

Den här frågan returnerar nätverks säkerhets grupper (NSG: er) som inte är kopplade till ett nätverks gränssnitt eller undernät.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [frågespråket](../concepts/query-language.md)
- Lär dig att [utforska resurser](../concepts/explore-resources.md)
- Se exempel på [avancerade frågor](advanced.md)