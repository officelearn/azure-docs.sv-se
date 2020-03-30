---
title: Ta bort data från Azure Data Explorer
description: I den här artikeln beskrivs borttagningsscenarier i Azure Data Explorer, inklusive rensning, släppa omfattningar och bevarandebaserade borttagningar.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501413"
---
# <a name="delete-data-from-azure-data-explorer"></a>Ta bort data från Azure Data Explorer

Azure Data Explorer stöder olika borttagningsscenarier som beskrivs i den här artikeln. 

## <a name="delete-data-using-the-retention-policy"></a>Ta bort data med hjälp av bevarandeprincipen

Azure Data Explorer tar automatiskt bort data baserat på [bevarandeprincipen](/azure/kusto/management/retentionpolicy). Denna metod är det mest effektiva och problemfria sättet att ta bort data. Ange bevarandeprincipen på databas- eller tabellnivå.

Överväg en databas eller tabell som har angetts för 90 dagars kvarhållning. Om det bara behövs 60 dagars data tar du bort äldre data enligt följande:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Ta bort data genom att släppa omfattningar

[Omfattning (datashard)](/azure/kusto/management/extents-overview) är den interna strukturen där data lagras. Varje utsträckning kan innehålla upp till miljontals poster. Omfattningar kan tas bort individuellt eller som en grupp med hjälp av [kommandona drop extent(s).](/azure/kusto/management/extents-commands#drop-extents) 

### <a name="examples"></a>Exempel

Du kan ta bort alla rader i en tabell eller bara en viss utsträckning.

* Ta bort alla rader i en tabell:

    ```kusto
    .drop extents from TestTable
    ```

* Stryk en viss omfattning:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Ta bort enskilda rader med rensning

[Datarensning](/azure/kusto/concepts/data-purge) kan användas för att ta bort enskilda rader. Borttagning är inte omedelbar och kräver betydande systemresurser. Därför rekommenderas det bara för efterlevnadsscenarier.  

