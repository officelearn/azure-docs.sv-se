---
title: Det gick inte att skapa eller ta bort DB eller tabell i Azure Data Explorer
description: I den här artikeln beskrivs felsökningssteg för att skapa och ta bort databaser och tabeller i Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562402"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Felsöka: Det gick inte att skapa eller ta bort en databas eller tabell i Azure Data Explorer

I Azure Data Explorer arbetar du regelbundet med databaser och tabeller. Den här artikeln innehåller felsökningssteg för problem som kan komma upp.

## <a name="creating-a-database"></a>Skapa en databas

1. Se till att du har tillräcklig behörighet. Om du vill skapa en databas måste du vara medlem i rollen *Deltagare* eller *Ägare* för Azure-prenumerationen. Om det behövs arbetar du med prenumerationsadministratören så att de kan lägga till dig i rätt roll.

1. Kontrollera att det inte finns några namnverifieringsfel för databasnamnet. Namnet måste vara alfanumeriskt med en maximal längd på 260 tecken.

1. Kontrollera att databaslagrings- och cachelagringsvärdena ligger inom tillåtna intervall. Kvarhållningen måste vara mellan 1 och 36500 dagar (100 år). Cachelagring måste vara mellan 1 och det högsta värde som ställts in för kvarhållning.

## <a name="deleting-or-renaming-a-database"></a>Ta bort eller byta namn på en databas

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en databas måste du vara medlem i rollen *Deltagare* eller *Ägare* för Azure-prenumerationen. Om det behövs arbetar du med prenumerationsadministratören så att de kan lägga till dig i rätt roll.

## <a name="creating-a-table"></a>Skapa en tabell

1. Se till att du har tillräcklig behörighet. Om du vill skapa en tabell måste du vara medlem i *databasadministratören* eller *databasanvändarrollen* i databasen eller *rollen Deltagare* eller *Ägare* för Azure-prenumerationen. Om det behövs arbetar du med din prenumeration eller klusteradministratör så att de kan lägga till dig i rätt roll.

    Mer information om behörigheter finns i [Hantera databasbehörigheter](manage-database-permissions.md).

1. Kontrollera att det inte redan finns en tabell med samma namn. Om den finns kan du: Skapa en tabell med ett annat namn; byta namn på den befintliga tabellen (kräver *rollen för tabelladministratören).* eller släpp den befintliga tabellen (kräver *databasadministratörsroll).* Använd följande kommandon.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Ta bort eller byta namn på en tabell

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en tabell måste du vara medlem i *databasadministratörs-* eller *tabelladministratörsrollen* i databasen. Om det behövs arbetar du med din prenumeration eller klusteradministratör så att de kan lägga till dig i rätt roll.

Mer information om behörigheter finns i [Hantera databasbehörigheter](manage-database-permissions.md).

## <a name="general-guidance"></a>Allmän vägledning

1. Titta på [instrumentpanelen för Azure-tjänsthälsa](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du försöker arbeta med en databas eller tabell.

    Om statusen inte är **bra** (grön bock) försöker du igen när statusen har förbättras.

1. Om du fortfarande behöver hjälp med att lösa problemet öppnar du en supportbegäran i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Nästa steg

[Kontrollera klusterhälsa](check-cluster-health.md)