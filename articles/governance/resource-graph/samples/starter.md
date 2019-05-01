---
title: Exempel på startfrågor
description: Använd Azure Resource Graph för att köra vissa startfrågor såsom att räkna resurser, ordna resurser eller efter en viss tagg.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 98b05f74f0d6f7d20b5aa7ed77047818f217f147
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691179"
---
# <a name="starter-resource-graph-queries"></a>Startfrågor för Azure Resource Graph

Det första steget mot att förstå frågor med Azure Resource Graph är en grundläggande förståelse för [frågespråket](../concepts/query-language.md). Om du inte redan är bekant med [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) rekommenderar vi att du går igenom grunderna så att du förstår hur man skapar begäranden för de resurser som du letar efter.

Vi går igenom följande startfrågor:

> [!div class="checklist"]
> - [Antal Azure-resurser](#count-resources)
> - [Lista över resurser sorterade efter namn](#list-resources)
> - [Visning av alla virtuella datorer sorterade efter namn i fallande ordning](#show-vms)
> - [Visning av de första fem virtuella datorerna efter namn och OS-typ](#show-sorted)
> - [Antal virtuella datorer efter OS-typ](#count-os)
> - [Visning av resurser med lagring](#show-storage)
> - [Lista över alla offentliga IP-adresser](#list-publicip)
> - [Antal resurser som har IP-adresser konfigurerade efter prenumeration](#count-resources-by-ip)
> - [Lista över resurser med ett specifikt tagg-värde](#list-tag)
> - [Lista över alla lagringskonton med ett specifikt taggvärde](#list-specific-tag)
> - [Visa alias för en virtuell datorresurs](#show-aliases)
> - [Visa distinkta värden för ett visst alias](#distinct-alias-values)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Stöd för språk

Azure CLI (via ett tillägg) och Azure PowerShell (via en modul) har stöd för Azure Resource Graph. Kontrollera att din miljö är redo innan du kör någon av nedanstående frågor. Se [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) för anvisningar om hur du installerar och validerar din valda gränssnittsmiljö.

## <a name="a-namecount-resourcescount-azure-resources"></a><a name="count-resources"/>Antal Azure-resurser

Den här frågan returnerar antalet Azure-resurser som finns i de prenumerationer som du har åtkomst till. Det är också en bra fråga för att verifiera att ditt gränssnittval har lämpliga Azure Resource Graph-komponenter installerade och fungerar korrekt.

```kusto
summarize count()
```

```azurecli-interactive
az graph query -q "summarize count()"
```

```azurepowershell-interactive
Search-AzGraph -Query "summarize count()"
```

## <a name="a-namelist-resourceslist-resources-sorted-by-name"></a><a name="list-resources"/>Lista resurser sorterade efter namn

Frågan returnerar alla typer av resurser men bara egenskaperna **name** (namn), **type** (typ) och **location** (plats). Den använder `order by` för att sortera egenskaperna efter egenskapen **name** (namn) i stigande (`asc`) ordning.

```kusto
project name, type, location
| order by name asc
```

```azurecli-interactive
az graph query -q "project name, type, location | order by name asc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, type, location | order by name asc"
```

## <a name="a-nameshow-vmsshow-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"/>Visa alla virtuella datorer ordnade efter namn i fallande ordning

För att bara lista virtuella datorer (som är typen `Microsoft.Compute/virtualMachines`) kan vi matcha egenskapen **type** (typ) i resultatet. Som för den föregående frågan, ändrar `desc` `order by` till att vara fallande. Tecknet `=~` i typmatchningen talar om för Resource Graph att Resource Graph ska vara skiftlägesokänsligt.

```kusto
project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

```azurecli-interactive
az graph query -q "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

## <a name="a-nameshow-sortedshow-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"/>Visa den första fem virtuella datorer efter namn och sina OS-typ

Den här frågan använder `limit` för att bara hämta fem matchande poster som sorteras efter namn. Typen av Azure-resurs är `Microsoft.Compute/virtualMachines`. `project` talar om Azure Resource Graph vilka egenskaper som ska inkluderas.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

## <a name="a-namecount-oscount-virtual-machines-by-os-type"></a><a name="count-os"/>Antal virtuella datorer enligt OS-typ

Vi bygger vidare på den föregående frågan och begränsar fortfarande efter Azure-resurstyp `Microsoft.Compute/virtualMachines` men inte längre efter antalet returnerade poster.
I stället använder vi `summarize` och `count()` för att definiera hur värdena ska grupperas och aggregeras efter egenskap, som i det här exemplet är `properties.storageProfile.osDisk.osType`. Ett exempel på hur denna sträng ser ut i det fullständiga objektet visas i [Utforska resurser – Identifiering av virtuell maskin](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

Ett annat sätt att skriva samma fråga är att `extend` en egenskap och ge den ett tillfälligt namn för användning i frågan, i det här fallet **os**. **os** används då av `summarize` och `count()` som i föregående exempel.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

> [!NOTE]
> Tänk på att medan `=~` tillåter skiftlägesokänslig matchning, kräver användning av egenskaper (som **properties.storageProfile.osDisk.osType**) i frågan att skiftläget är korrekt. Om egenskapen är i fel skiftläge kan den fortfarande returnera ett värde men grupperingen eller sammanfattningen blir felaktig.

## <a name="a-nameshow-storageshow-resources-that-contain-storage"></a><a name="show-storage"/>Visa resurser med storage

I stället för att explicit definiera den typ som ska matchas, hittar den här exempelfrågan alla Azure-resurser som `contains` ordet **storage** (lagring).

```kusto
where type contains 'storage' | distinct type
```

```azurecli-interactive
az graph query -q "where type contains 'storage' | distinct type"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'storage' | distinct type"
```

## <a name="a-namelist-publiciplist-all-public-ip-addresses"></a><a name="list-publicip"/>Lista över alla offentliga IP-adresser

Hittar på ett liknande sätt som för den föregående frågan allt som är en typ med ordet **publicIPAddresses**.
Den här frågan kan utökas med mönstret som bara inkluderar resultat där **properties.ipAddress**
`isnotempty`, för att endast returnera de **properties.ipAddress**, och `limit` den resultaten efter upp
100. Du kan behöva hoppa över citattecknen beroende på valt gränssnitt.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

## <a name="a-namecount-resources-by-ipcount-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"/>Antal resurser som har IP-adresser som konfigurerats av prenumeration

Om vi använder den föregående exempelfrågan och lägger till `summarize` och `count()`, kan vi få en lista efter prenumeration på resurser med konfigurerade IP-adresser.

```kusto
where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

```azurecli-interactive
az graph query -q "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

## <a name="a-namelist-taglist-resources-with-a-specific-tag-value"></a><a name="list-tag"/>Lista resurser med en specifik tagg-värde

Vi kan begränsa resultaten med andra egenskaper än Azure-resurstyp, till exempel en tagg. I det här exemplet filtrerar vi för Azure-resurser med ett taggnamn innehållande **environment** som har värdet **internal**.

```kusto
where tags.environment=~'internal'
| project name
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name"
```

Om du även vill ange vilka taggar som resursen har och deras värden lägger du egenskapen **taggs** (taggar) i `project`-nyckelordet.

```kusto
where tags.environment=~'internal'
| project name, tags
```

```azurecli-interactive
az graph query -q "where tags.environment=~'internal' | project name, tags"
```

```azurepowershell-interactive
Search-AzGraph -Query "where tags.environment=~'internal' | project name, tags"
```

## <a name="a-namelist-specific-taglist-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"/>Lista alla lagringskonton med specifika Taggvärde

Kombinera filterfunktionerna för exemplet ovan och filtrera Azure-resurstyp efter egenskapen **type** (typ). Den här frågan begränsar även sökningen för specifika typer av Azure-resurser med ett visst taggnamn och -värde.

```kusto
where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

> [!NOTE]
> I det här exemplet används `==` för matchning istället för villkorliga `=~`. `==` är en skiftlägeskänslig matchning.

## <a name="a-nameshow-aliasesshow-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases"/>Visa alias för en virtuell datorresurs

[Azure Policy-alias](../../policy/concepts/definition-structure.md#aliases) används av Azure Policy för att hantera kompatibiliteten för resursen. Azure Resource-diagram kan returnera de _alias_ för en resurstyp. Dessa värden används för att jämföra det aktuella värdet för alias när du skapar en anpassad principdefinition. Den _alias_ matrisen inte tillhandahålls som standard i en frågas resultat. Använd `project aliases` uttryckligen lägga till den i resultaten.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

## <a name="a-namedistinct-alias-valuesshow-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values"/>Visa distinkta värden för ett visst alias

Visa värdet för alias på en enskild resurs är bra, men den visar inte värdet true för att använda Azure Resource-diagram till frågan i alla prenumerationer. Det här exemplet tittar på alla värden i ett visst alias och returnerar de distinkta värdena.

```kusto
where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)"
```

```azurecli-interactive
az graph query -q "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågespråket](../concepts/query-language.md)
- Lär dig att [utforska resurser](../concepts/explore-resources.md)
- Se exempel på [avancerade frågor](advanced.md)