---
title: Ta bort data från Azure Data Explorer
description: Den här artikeln beskriver bulk delete scenarier i Azure Data utforska, inklusive rensa och kvarhållning av säkerhetskopior baserat tar bort.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9c1b21e119a38c6d306b9c564ab7958ba21a1c41
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049146"
---
# <a name="delete-data-from-azure-data-explorer"></a>Ta bort data från Azure Data Explorer

Azure Data Explorer stöder flera bulk delete metoder, vilket vi upp i den här artikeln. Det stöder inte per postborttagning i realtid, eftersom det är optimerat för snabb läsbehörighet.

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

Om du behöver hjälp med problem med borttagning, öppnar du en supportförfrågan i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
