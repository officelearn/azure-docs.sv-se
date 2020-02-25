---
title: Det gick inte att skapa eller ta bort databasen eller tabellen i Azure Datautforskaren
description: I den här artikeln beskrivs fel söknings steg för att skapa och ta bort databaser och tabeller i Azure Datautforskaren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 67e4c33498b05884fe667a7977ddb40e647ab4c8
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562402"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Fel sökning: det gick inte att skapa eller ta bort en databas eller tabell i Azure Datautforskaren

I Azure Datautforskaren arbetar du regelbundet med databaser och tabeller. Den här artikeln innehåller fel söknings steg för problem som kan uppstå.

## <a name="creating-a-database"></a>Skapa en databas

1. Se till att du har tillräcklig behörighet. Om du vill skapa en databas måste du vara medlem i rollen *deltagare* eller *ägare* för Azure-prenumerationen. Om det behövs kan du arbeta med prenumerations administratören så att de kan lägga till dig i rätt roll.

1. Se till att det inte finns några namn verifierings fel för databas namnet. Namnet måste vara alfanumeriskt, med en maximal längd på 260 tecken.

1. Kontrol lera att värdena för databas kvarhållning och cachelagring ligger inom tillåtna intervall. Kvarhållning måste vara mellan 1 och 36500 dagar (100 år). Cachelagring måste vara mellan 1 och det högsta värdet som anges för kvarhållning.

## <a name="deleting-or-renaming-a-database"></a>Ta bort eller byta namn på en databas

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en databas måste du vara medlem i rollen *deltagare* eller *ägare* för Azure-prenumerationen. Om det behövs kan du arbeta med prenumerations administratören så att de kan lägga till dig i rätt roll.

## <a name="creating-a-table"></a>Skapa en tabell

1. Se till att du har tillräcklig behörighet. Om du vill skapa en tabell måste du vara medlem i användar rollen *databas administratör* eller *databas* i databasen eller rollen *deltagare* eller *ägare* för Azure-prenumerationen. Om det behövs kan du arbeta med din prenumeration eller kluster administratör så att de kan lägga till dig till rätt roll.

    Mer information om behörigheter finns i [Hantera databasbehörigheter](manage-database-permissions.md).

1. Kontrol lera att det inte redan finns en tabell med samma namn. Om den finns kan du: skapa en tabell med ett annat namn. Byt namn på den befintliga tabellen (kräver *Table admin* -rollen). eller ta bort den befintliga tabellen (kräver rollen som *databas administratör* ). Använd följande kommandon.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Ta bort eller byta namn på en tabell

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en tabell måste du vara medlem i rollen *databas administratör* eller *tabell administratör* i-databasen. Om det behövs kan du arbeta med din prenumeration eller kluster administratör så att de kan lägga till dig till rätt roll.

Mer information om behörigheter finns i [Hantera databasbehörigheter](manage-database-permissions.md).

## <a name="general-guidance"></a>Allmän vägledning

1. Titta på [instrumentpanelen för Azure-tjänsthälsa](https://azure.microsoft.com/status/). Sök efter status för Azure Datautforskaren i den region där du försöker arbeta med en databas eller tabell.

    Om statusen inte är **felfri** (grön bock), försök igen när statusen har förbättrats.

1. Om du fortfarande behöver hjälp med att lösa problemet, måste du öppna en supportbegäran i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Nästa steg

[Kontrol lera kluster hälsa](check-cluster-health.md)