---
title: Exempel på avancerade frågor
description: Använd Azure Resource Graph för att köra vissa avancerade frågor, inklusive VMSS-kapacitet, visa en lista med alla taggar som används och matcha virtuella datorer med reguljära uttryck.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 9a243dd236a8c499602a9070a7dd61e69541d58d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256829"
---
# <a name="advanced-resource-graph-queries"></a>Avancerade frågor för Resource Graph

Det första steget mot att förstå frågor med Azure Resource Graph är en grundläggande förståelse för [frågespråket](../concepts/query-language.md). Om du inte redan är bekant med [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md) rekommenderar vi att du går igenom grunderna så att du förstår hur man skapar begäranden för de resurser som du letar efter.

Vi går igenom följande avancerade frågor:

> [!div class="checklist"]
> - [Hämta VMSS-kapacitet och storlek](#vmss-capacity)
> - [Lista alla taggnamn](#list-all-tags)
> - [Virtuella datorer matchade av regex](#vm-regex)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="language-support"></a>Språkstöd

Azure CLI (via ett tillägg) och Azure PowerShell (via en modul) har stöd för Azure Resource Graph. Kontrollera att din miljö är redo innan du kör någon av nedanstående frågor. Se [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) och [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) för anvisningar om hur du installerar och validerar din valda gränssnittsmiljö.

## <a name="vmss-capacity"></a>Hämta kapacitet och storlek för skaluppsättning för virtuell dator

Den här frågan söker efter resurser för VM-skalningsuppsättningsresurser och hämtar olika typer av information om t.ex. den virtuella datorns storlek och skalningsuppsättningens kapacitet. Den här frågan använder `toint()`-funktionen för att konvertera kapaciteten till ett tal, så att den kan sorteras. Slutligen ändras kolumnernas namn till anpassade namngivna egenskaper.

```Query
where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

```azurecli-interactive
az graph query -q "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
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

```azurepowershell-interactive
Search-AzGraph -Query "project tags | summarize buildschema(tags)"
```

## <a name="vm-regex"></a>Virtuella datorer matchade av regex

Den här frågan söker efter virtuella datorer som matchar ett [reguljärt uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference) (även kallat _regex_).
Den **matchar regex \@**  ger oss möjlighet att definiera regex så att de matchar, vilket är `^Contoso(.*)[0-9]+$`. Den regex-definitionen förklaras så här:

- `^` – Matchningen måste börja i början av strängen.
- `Contoso` – Skiftlägeskänslig sträng.
- `(.*)` – En matchning av underuttryck:
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

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

## <a name="next-steps"></a>Nästa steg

- Se exempel på [startfrågor](starter.md)
- Läs mer om [frågespråket](../concepts/query-language.md)
- Lär dig att [utforska resurser](../concepts/explore-resources.md)