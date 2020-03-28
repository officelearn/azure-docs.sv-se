---
title: Exempel på avancerade frågor
description: Använd Azure Resource Graph för att köra några avancerade frågor, inklusive att arbeta med kolumner, lista taggar som används och matcha resurser med reguljära uttryck.
ms.date: 03/20/2020
ms.topic: sample
ms.openlocfilehash: 98e80d0977ec23cba8b8c497373b6e89b52d430e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130514"
---
# <a name="advanced-resource-graph-query-samples"></a>Exempel på avancerad resursdiagram

Det första steget mot att förstå frågor med Azure Resource Graph är en grundläggande förståelse för [frågespråket](../concepts/query-language.md). Om du inte redan är bekant med [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) rekommenderar vi att du går igenom grunderna så att du förstår hur man skapar begäranden för de resurser som du letar efter.

Vi går igenom följande avancerade frågor:

> [!div class="checklist"]
> - [Visa API-version för varje resurstyp](#apiversion)
> - [Hämta kapacitet och storlek för den virtuella datorns skaluppsättning](#vmss-capacity)
> - [Ta bort kolumner från resultat](#remove-column)
> - [Lista alla taggnamn](#list-all-tags)
> - [Virtuella datorer matchade av regex](#vm-regex)
> - [Lista Cosmos DB med specifika skrivplatser](#mvexpand-cosmosdb)
> - [Nyckelvalv med prenumerationsnamn](#join)
> - [Lista SQL-databaser och deras elastiska pooler](#join-sql)
> - [Lista virtuella datorer med sitt nätverksgränssnitt och offentliga IP](#join-vmpip)
> - [Hitta lagringskonton med en specifik tagg i resursgruppen](#join-findstoragetag)
> - [Kombinera resultat från två frågor till ett enda resultat](#unionresults)
> - [Inkludera klient- och prenumerationsnamnen med DisplayNames](#displaynames)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="language-support"></a>Stöd för språk

Azure CLI (via ett tillägg) och Azure PowerShell (via en modul) har stöd för Azure Resource Graph. Kontrollera att din miljö är redo innan du kör någon av nedanstående frågor. Se [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) för anvisningar om hur du installerar och validerar din valda gränssnittsmiljö.

## <a name="show-resource-types-and-api-versions"></a><a name="apiversion" />Visa resurstyper och API-versioner

Resource Graph använder i första hand den senaste versionen `GET` av resursproviderns API som inte är förhandsgranskning till resursegenskaper under en uppdatering. I vissa fall har API-versionen som används åsidosatts för att tillhandahålla mer aktuella eller allmänt använda egenskaper i resultaten. Följande fråga beskriver API-versionen som används för att samla in egenskaper för varje resurstyp:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity" />Hämta kapacitet och storlek för skaluppsättning för virtuell dator

Den här frågan söker efter resurser för VM-skalningsuppsättningsresurser och hämtar olika typer av information om t.ex. den virtuella datorns storlek och skalningsuppsättningens kapacitet. Den här frågan använder `toint()`-funktionen för att konvertera kapaciteten till ett tal, så att den kan sorteras. Slutligen ändras kolumnernas namn till anpassade namngivna egenskaper.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="remove-columns-from-results"></a><a name="remove-column" />Ta bort kolumner från resultat

Följande fråga `summarize` används för att `join` räkna resurser efter prenumeration, för att kombinera `project-away` den med prenumerationsinformation från _resurscontainers-tabellen_ och sedan ta bort några av kolumnerna.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="list-all-tag-names"></a><a name="list-all-tags" />Lista alla taggnamn

Den här frågan börjar med taggen och skapar ett JSON-objekt med alla unika taggnamn och deras motsvarande typer.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="virtual-machines-matched-by-regex"></a><a name="vm-regex" />Virtuella datorer matchade av regex

Den här frågan söker efter virtuella datorer som matchar ett [reguljärt uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference) (även kallat _regex_). Matcherna **regex \@ ** tillåter oss att definiera regex `^Contoso(.*)[0-9]+$`att matcha, vilket är .
Den regex-definitionen förklaras så här:

- `^` – Matchningen måste börja i början av strängen.
- `Contoso` – Skiftlägeskänslig sträng.
- `(.*)` – En matchning av underuttryck:
  - `.` – Matchar varje enskilt tecken (förutom ny rad).
  - `*` – Matchar föregående element noll eller flera gånger.
- `[0-9]` – Teckengruppsmatchning för siffrorna 0-9.
- `+` – Matchar föregående element en eller flera gånger.
- `$` – Matchning av föregående element måste ske i slutet av strängen.

När matchningen efter namn är klar projicerar frågan namnet och sorterar efter namn, i stigande ordning.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="list-cosmos-db-with-specific-write-locations"></a><a name="mvexpand-cosmosdb" />Lista Cosmos DB med specifika skrivplatser

Följande frågegränser till Cosmos DB-resurser används `mv-expand` för att expandera egenskapspåsen för **properties.writeLocations**, sedan projektspecifika fält och begränsa resultaten ytterligare till **properties.writeLocations.locationName-värden** som matchar antingen "Östra USA" eller "Västra USA".

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="key-vault-with-subscription-name"></a><a name="join" />Nyckelvalv med prenumerationsnamn

Följande fråga visar en `join`komplex användning av . Frågan begränsar den kopplade tabellen till `project` prenumerationsresurser och med att endast inkludera den ursprungliga _fältprenumerationenId_ och _namnfältet_ som bytt namn till _Undernamn_. Fältet byter namn `join` undviker att lägga till det som _namn1_ eftersom fältet redan finns i _resurser_. Den ursprungliga tabellen filtreras `where` med `project` och följande innehåller kolumner från båda tabellerna. Frågeresultatet är ett enda nyckelvalv som visar typ, namnet på nyckelvalvet och namnet på prenumerationen det finns i.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="list-sql-databases-and-their-elastic-pools"></a><a name="join-sql" />Lista SQL-databaser och deras elastiska pooler

Följande fråga använder **leftouter** `join` för att samla SQL Database-resurser och deras relaterade elastiska pooler, om de har några.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="list-virtual-machines-with-their-network-interface-and-public-ip"></a><a name="join-vmpip" />Lista virtuella datorer med sitt nätverksgränssnitt och offentliga IP

Den här frågan använder två **leftouter-kommandon** `join` för att samla virtuella datorer, deras relaterade nätverksgränssnitt och alla offentliga IP-adresser som är relaterade till dessa nätverksgränssnitt.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mv-expand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mv-expand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mv-expand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mv-expand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mv-expand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mv-expand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a><a name="join-findstoragetag" />Hitta lagringskonton med en specifik tagg i resursgruppen

Följande fråga använder en **inre** `join` för att ansluta lagringskonton med resursgrupper som har ett angivet skiftlägeskänsligt taggnamn och taggvärde.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['Key1'] =~ 'Value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['Key1'] =~ 'Value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'Key1'%5D%20%3D~%20'Value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

Om det är nödvändigt att leta efter ett fall okänsligt `mv-expand` taggnamn och taggvärde använder du med parametern **bagexpansion.** Den här frågan använder mer kvot `mv-expand` än föregående fråga, så använd bara om det behövs.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | mv-expand bagexpansion=array tags
    | where isnotempty(tags)
    | where tags[0] =~ 'key1' and tags[1] =~ 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | mv-expand bagexpansion=array tags | where isnotempty(tags) | where tags[0] =~ 'key1' and tags[1] =~ 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20%27microsoft.storage%2Fstorageaccounts%27%20%7C%20join%20kind%3Dinner%20%28%20ResourceContainers%20%7C%20where%20type%20%3D~%20%27microsoft.resources%2Fsubscriptions%2Fresourcegroups%27%20%7C%20mv-expand%20bagexpansion%3Darray%20tags%20%7C%20where%20isnotempty%28tags%29%20%7C%20where%20tags%5B0%5D%20%3D~%20%27key1%27%20and%20tags%5B1%5D%20%3D~%20%27value1%27%20%7C%20project%20subscriptionId%2C%20resourceGroup%29%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="combine-results-from-two-queries-into-a-single-result"></a><a name="unionresults" />Kombinera resultat från två frågor till ett enda resultat

Följande fråga `union` används för att hämta resultat från tabellen _ResourceContainers_ och lägga till dem i resultat från tabellen _Resurser._

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Ikon för Utforskaren för Resursdiagram](../media/resource-graph-small.png) Prova den här frågan i Utforskaren för Azure Resource Graph:

- Azure-portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.com</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure Government-portal: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.us</a> ![Öppna länken i den nya fönsterikonen](../../media/new-window.png)
- Azure China portal: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.cn</a> ![Öppna länken i nya fönsterikonen](../../media/new-window.png)

---

## <a name="include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames" />Inkludera klient- och prenumerationsnamnen med DisplayNames

Den här frågan använder den nya **inkludera** parametern med alternativet _DisplayNames_ för att lägga till **subscriptionDisplayName** och **tenantDisplayName** i resultatet. Den här parametern är endast tillgänglig för Azure CLI och Azure PowerShell.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

Ett alternativ till att hämta prenumerationsnamnet är att använda operatorn `join` och ansluta till tabellen **ResourceContainers** och `Microsoft.Resources/subscriptions` typen. `join`fungerar i Azure CLI, Azure PowerShell, portal och alla SDK som stöds. Ett exempel finns i [Exempel - Nyckelvalv med prenumerationsnamn](#join).

> [!NOTE]
> Om frågan inte använder **projektet** för att ange de returnerade egenskaperna inkluderas **prenumerationdisplayName** och **tenantDisplayName** automatiskt i resultatet.
> Om frågan använder **projektet**måste vart och ett av _DisplayName-fälten_ uttryckligen inkluderas i **projektet** eller så returneras de inte i resultaten, även när parametern **Inkludera** används. Parametern **Inkludera** fungerar inte med [tabeller](../concepts/query-language.md#resource-graph-tables).

## <a name="next-steps"></a>Nästa steg

- Se exempel på [startfrågor](starter.md).
- Läs mer om [frågespråket](../concepts/query-language.md).
- Läs mer om hur du [utforskar resurser](../concepts/explore-resources.md).