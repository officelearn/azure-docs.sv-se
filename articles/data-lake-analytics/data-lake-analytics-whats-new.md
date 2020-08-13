---
title: Data Lake Analytics senaste ändringarna
description: Den här artikeln innehåller en kontinuerlig lista över de senaste ändringarna som görs i Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 0afd37a9f80fd2778f2b7206f58cec6ff423763e
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88184966"
---
# <a name="whats-new-in-data-lake-analytics"></a>Nyheter i Data Lake Analytics

Azure Data Lake Analytics uppdateras baserat på aperiodic för vissa komponenter. För att hålla uppdaterad med den senaste uppdateringen ger den här artikeln information om:

- Meddelande om för hands versionen av Key Component beta
- Information om den viktiga komponent versionen, t. ex.: listan över tillgängliga versioner av komponenten, den aktuella standard versionen och så vidare.


## <a name="notification-of-key-component-beta-preview"></a>Meddelande om för hands versionen av Key Component beta

Det finns ingen viktig komponent beta version tillgänglig för för hands versionen. 

## <a name="u-sql-runtime"></a>U-SQL-körning

Azure Data Lake U-SQL-körningsmiljön, inklusive kompilatorn, optimeringen och jobb hanteraren, är det som bearbetar din U-SQL-kod.

När du skickar Azure Data Lake Analytics-jobbet från valfria verktyg använder ditt jobb den aktuella standard körningen i produktions miljön. 

Körnings versionen kommer att uppdateras aperiodically. Och den tidigare körningen kommer att finnas tillgänglig under en viss tid. När en ny beta version är klar för förhands granskning kommer den också att vara tillgänglig där.

Följande är de versioner som är tillgängliga för närvarande.

- Release-20200124live_adl_16283022_2--> **den aktuella standard versionen**
- release_20200124live_adl_16283022
- release_20200124_adl_14480125
- release_20190904_adl_10236248_1
- release_20190904_adl_10236248
- release_20190904_adl_9225818

Information om hur du felsöker U-SQL runtime-fel finns i [Felsöka u-SQL runtime-fel](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics använder nu **.NET Framework v-4.7.2**. 

Om din Azure Data Lake Analytics U-SQL-skriptfil använder anpassade sammansättningar och dessa anpassade sammansättningar använder .NET-bibliotek, verifierar du koden för att kontrol lera om det finns några avbrott.

För att få hjälp med att felsöka en .NET-uppgradering med hjälp [av Felsök en .net-uppgradering](runtime-troubleshoot.md).

## <a name="release-note"></a>Versions meddelande

Information om den senaste uppdateringen finns i [Azure Data Lake Analytics release-anteckningen](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Nästa steg

* Kom igång med data Lake Analytics att använda [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md)

