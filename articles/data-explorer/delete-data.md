---
title: Ta bort data från Azure Data Explorer
description: Den här artikeln beskriver bulk delete scenarier i Azure Data utforska, inklusive rensa och kvarhållning av säkerhetskopior baserat tar bort.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048044"
---
# <a name="delete-data-from-azure-data-explorer"></a>Ta bort data från Azure Data Explorer

Azure Data Explorer stöder flera bulk delete metoder, vilket vi upp i den här artikeln. Det stöder inte per postborttagning i realtid, eftersom det är optimerat för snabb läsbehörighet.

* Om databasen är inte längre behövs kan du ta bort den med hjälp av kommandot drop database.

    ```Kusto
    .drop database <DatabaseName>
    ```

* Om en eller flera tabeller inte längre behövs, ta bort dem med hjälp av tabellen släpp eller släppa tabeller kommando.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Om gamla data inte längre behövs kan du ta bort den genom att ändra kvarhållningsperioden på nivån databas eller tabell.

    Överväg att en databas eller tabell som har angetts för 90 dagars kvarhållning. Företag behöver ändras, så nu behövs endast 60 dagars data. Ta bort äldre data i det här fallet på något av följande sätt.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Mer information finns i [bevarandeprincip](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

* Du kan ta bort enskilda poster med hjälp av den *Rensa* åtgärd, baserat på ett predikat liknande `where CustomerName == 'contoso'`. Det finns dock en rensning är en grupp tar du bort inte är avsedd för borttagning i realtid. I följande exempel visas en rensning.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Om du behöver hjälp med problem med borttagning, öppnar du en supportförfrågan i den [Azure-portalen](https://portal.azure.com).