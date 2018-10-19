---
title: Avancerade frågor för Azure Resource Graph
description: Använd Azure Resource Graph för att köra vissa avancerade frågor.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a9281bc333b3edd12501a6813f12b9e7ad1bf59f
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219275"
---
# <a name="advanced-resource-graph-queries"></a>Avancerade frågor för Resource Graph

Det första steget mot att förstå frågor med Azure Resource Graph är en grundläggande förståelse för [frågespråket](../concepts/query-language.md). Om du inte redan är bekant med [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) rekommenderar vi att du går igenom grunderna så att du förstår hur man skapar begäranden för de resurser som du letar efter.

Vi går igenom följande avancerade frågor:

> [!div class="checklist"]
> - [Hämta VMSS-kapacitet och storlek](#vmss-capacity)
> - [Lista alla taggnamn](#list-all-tags)
> - [Virtuella datorer matchade av regex](#vm-regex)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="language-support"></a>Stöd för språk

Azure CLI (via ett tillägg) och Azure PowerShell (via en modul) har stöd för Azure Resource Graph. Kontrollera att din miljö är redo innan du kör någon av nedanstående frågor. Se [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) för anvisningar om hur du installerar och validerar din valda gränssnittsmiljö.

## <a name="vmss-capacity"></a>Hämta VMSS-kapacitet och storlek

Den här frågan söker efter resurser för VM-skalningsuppsättningsresurser (VMSS) och hämtar olika typer av information om t.ex. den virtuella datorns storlek och skalningsuppsättningens kapacitet. Den här informationen använder `toint()`-funktionen för att konvertera kapaciteten till ett tal, så att den kan sorteras. Den byter även nämn på de värden som returneras till anpassade namngivna egenskaper.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```powershell
Search-AzureRmGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

## <a name="list-all-tags"></a>Lista alla taggnamn

Den här frågan börjar med taggen och skapar ett JSON-objekt med alla unika taggnamn och deras motsvarande typer.

```Query
project tags
| summarize buildschema(tags)
```

```azurecli-interactive
az graph query -q "project tags | summarize buildschema(tags)"
```

```powershell
Search-AzureRmGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Virtuella datorer matchade av regex

Den här frågan söker efter virtuella datorer som matchar ett [reguljärt uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference) (även kallat _regex_).
Med **matches regex @** kan vi definiera regex så att det matchar, vilket är **^Contoso(.*)[0-9]+$**. Den regex-definitionen förklaras så här:

- `^` – Matchningen måste börja i början av strängen.
- `Contoso` – Kärnsträngen som vi söker efter matchning för (skiftlägeskänsligt).
- `(.*)` – En underuttrycksmatchning:
  - `.` – Matchar varje enskilt tecken (förutom ny rad).
  - `*` – Matchar föregående element noll eller flera gånger.
- `[0-9]` – Teckengruppsmatchning för siffrorna 0-9.
- `+` – Matchar föregående element en eller flera gånger.
- `$` – Matchning av föregående element måste ske i slutet av strängen.

När matchningen efter namn är klar projicerar frågan namnet och sorterar efter namn, i stigande ordning.

```Query
where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

```azurecli-interactive
az graph query -q "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

```powershell
Search-AzureRmGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Nästa steg

- Se exempel på [startfrågor](starter.md)
- Läs mer om [frågespråket](../concepts/query-language.md)
- Lär dig att [utforska resurser](../concepts/explore-resources.md)