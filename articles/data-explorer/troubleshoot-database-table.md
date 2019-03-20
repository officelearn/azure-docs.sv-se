---
title: Det gick inte att skapa eller ta bort en databas eller tabell i Azure Data Explorer
description: Den här artikeln beskrivs de felsökningssteg för att skapa och ta bort databaser och tabeller i Datautforskaren i Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0bd9f5ed00193aa9f872287b6afaf9e2b28c24dc
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188347"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>Felsökning: Det gick inte att skapa eller ta bort en databas eller tabell i Azure Data Explorer

I Azure Data Explorer arbeta du regelbundet med databaser och tabeller. Den här artikeln innehåller felsökningssteg för problem som kan fungera.

## <a name="creating-a-database"></a>Skapa en databas

1. Se till att du har tillräcklig behörighet. Om du vill skapa en databas, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

1. Se till att det finns inga namn valideringsfel för databasens namn. Namnet måste vara alfanumeriskt, med högst 260 tecken.

1. Kontrollera att databasen kvarhållning och cachelagring av värden finns i tillåtna områden. Kvarhållning måste vara mellan 1 och 36500 dagar (100 år). Cachelagring måste vara mellan 1 och det maximala värdet för kvarhållning.

## <a name="deleting-or-renaming-a-database"></a>Ta bort eller byta namn på en databas

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en databas, måste du vara medlem i den *deltagare* eller *ägare* rollen för Azure-prenumerationen. Om nödvändigt, fungerar med din administratör för prenumerationen så att de kan lägga till dig i rätt roll.

## <a name="creating-a-table"></a>Skapa en tabell

1. Se till att du har tillräcklig behörighet. Om du vill skapa en tabell, måste du vara medlem i den *databasen admin* eller *databasanvändare* roll i databasen eller *deltagare* eller *ägare* roll för Azure-prenumerationen. Om det behövs kan du arbeta med din prenumeration eller Klusteradministratören så att de kan lägga till dig i rätt roll.

    Mer information om behörigheter finns i [hantera databasbehörigheter](manage-database-permissions.md).

1. Se till att en tabell med samma namn redan inte finns. Om den finns, kan du: Skapa en tabell med ett annat namn. Byt namn på den befintliga tabellen (kräver *tabell admin* rollen); eller ta bort den befintliga tabellen (kräver *databasen admin* roll). Använd följande kommandon.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>Ta bort eller byta namn på en tabell

Se till att du har tillräcklig behörighet. Om du vill ta bort eller byta namn på en tabell, måste du vara medlem i den *databasen admin* eller *tabell admin* roll i databasen. Om det behövs kan du arbeta med din prenumeration eller Klusteradministratören så att de kan lägga till dig i rätt roll.

Mer information om behörigheter finns i [hantera databasbehörigheter](manage-database-permissions.md).

## <a name="general-guidance"></a>Allmän vägledning

1. Kontrollera den [hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/). Leta efter status för Azure Data Explorer i den region där du vill arbeta med en databas eller tabell.

    Om statusen inte är **bra** (grön bock), försök igen efter status förbättrar.

1. Om du fortfarande behöver hjälp med att lösa problemet kan du öppna en supportbegäran i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Nästa steg

[Kontrollera klusterhälsa](check-cluster-health.md)