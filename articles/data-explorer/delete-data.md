---
title: Ta bort data från Azure Datautforskaren
description: I den här artikeln beskrivs borttagnings scenarier i Azure Datautforskaren, inklusive rensning, släpp omfattningar och kvarhållning baserat på borttagningar.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204621"
---
# <a name="delete-data-from-azure-data-explorer"></a>Ta bort data från Azure Datautforskaren

Azure Datautforskaren stöder olika borttagnings scenarier som beskrivs i den här artikeln. 

## <a name="delete-data-using-the-retention-policy"></a>Ta bort data med hjälp av bevarande principen

Azure Datautforskaren tar automatiskt bort data baserat på [bevarande principen](/azure/kusto/management/retentionpolicy). Den här metoden är det effektivaste och problem fria sättet att ta bort data. Ange bevarande principen på databas-eller tabell nivå.

Överväg en databas eller tabell som har angetts för 90 dagars kvarhållning. Om endast 60 dagars data behövs tar du bort äldre data på följande sätt:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Ta bort data genom att släppa omfattningar

[Omfattning (data Shard)](/azure/kusto/management/extents-overview) är den interna strukturen där data lagras. Varje omfattning kan innehålla upp till miljon tals poster. Omfattningarna kan tas bort individuellt eller som en grupp med hjälp av [släpp omfattnings kommandon](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Exempel

Du kan ta bort alla rader i en tabell eller bara en bestämd omfattning.

* Ta bort alla rader i en tabell:

    ```kusto
    .drop extents from TestTable
    ```

* Ta bort en angiven omfattning:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Ta bort enskilda rader med Rensa

[Data rensning](/azure/kusto/management/data-purge) kan användas för att ta bort enskilda rader. Borttagningen är inte omedelbart och kräver betydande system resurser. Det här är ett exempel på hur du kan följa scenarier.  

