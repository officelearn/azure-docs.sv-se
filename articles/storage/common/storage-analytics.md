---
title: Använd Azure Storage-analys för att samla in loggar och måttdata | Microsoft-dokument
description: Med Storage Analytics kan du spåra måttdata för alla lagringstjänster och samla in loggar för Blob-, kö- och Tabelllagring.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 4ad9f13bcdf36b67400adb62d58ee260ff256bb3
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637149"
---
# <a name="storage-analytics"></a>Lagringsanalys

Azure Storage Analytics utför loggning och tillhandahåller måttdata för ett lagringskonto. Du kan använda dessa data för att spåra begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto.

Om du vill använda Storage Analytics måste du aktivera den individuellt för varje tjänst som du vill övervaka. Du kan aktivera den från [Azure-portalen](https://portal.azure.com). Mer information finns [i Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd [egenskaperna Ange Blob-tjänst,](/rest/api/storageservices/set-blob-service-properties) [Ange egenskaper för kötjänst,](/rest/api/storageservices/set-queue-service-properties) [Egenskaper för ange tabelltjänst](/rest/api/storageservices/set-table-service-properties)och Ange egenskaper för [filtjänst](/rest/api/storageservices/Get-File-Service-Properties) för att aktivera Lagringsanalys för varje tjänst.

Aggregerade data lagras i en välkänd blob (för loggning) och i välkända tabeller (för mått), som kan nås med hjälp av Blob-tjänsten och tabelltjänst-API:er.

Storage Analytics har en gräns på 20 TB för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Mer information om begränsningar för lagringskonton finns i [Skalbarhets- och prestandamål för standardlagringskonton](scalability-targets-standard-account.md).

En detaljerad guide om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Problem med Azure Storage finns i [Övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Fakturering för lagringsanalys

Alla måttdata skrivs av tjänsterna för ett lagringskonto. Därför kan varje skrivåtgärd som utförs av Storage Analytics faktureras. Dessutom är mängden lagringsutrymme som används av måttdata också fakturerbar.

Följande åtgärder som utförs av Storage Analytics kan faktureras:

* Begäranden om att skapa blobbar för loggning.
* Begäranden om att skapa tabellentiteter för mått.

Om du har konfigurerat en datalagringsprincip debiteras du inte för att ta bort transaktioner när Lagringsanalys tar bort gamla loggnings- och måttdata. Ta bort transaktioner från en klient kan dock faktureras. Mer information om bevarandeprinciper finns i [Ange en lagringsanalysdatalagringsprincip](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Förstå fakturerbara begäranden

Varje begäran som görs till ett kontos lagringstjänst kan antingen faktureras eller inte kan faktureras. Storage Analytics loggar varje enskild begäran till en tjänst, inklusive ett statusmeddelande som anger hur begäran hanterades. På samma sätt lagrar Storage Analytics mått för både en tjänst och API-åtgärder för den tjänsten, inklusive procentandelar och antal av vissa statusmeddelanden. Tillsammans kan dessa funktioner hjälpa dig att analysera dina fakturerbara begäranden, göra förbättringar i ditt program och diagnostisera problem med förfrågningar till dina tjänster. Mer information om fakturering finns i [Förstå Azure Storage Billing - Bandbredd, Transaktioner och Kapacitet](https://docs.microsoft.com/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

När du tittar på Storage Analytics-data kan du använda tabellerna i avsnittet [Lagringsanalysloggade operationer och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) för att avgöra vilka begäranden som kan faktureras. Sedan kan du jämföra dina loggar och måttdata med statusmeddelanden för att se om du debiterades för en viss begäran. Du kan också använda tabellerna i föregående ämne för att undersöka tillgänglighet för en lagringstjänst eller enskild API-åtgärd.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md)
* [Mätvärden för lagringsanalys](storage-analytics-metrics.md)
* [Loggning för lagringsanalys](storage-analytics-logging.md)
