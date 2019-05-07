---
title: Använd Azure Storage analytics för att samla in loggar och mått data | Microsoft Docs
description: Storage Analytics kan du spåra mätvärden för alla lagringstjänster och samla in loggar för Blob, Queue och Table storage.
services: storage
author: normesta
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: b588ebe577e61014c6c2bbeaae751b2783dd6f80
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153925"
---
# <a name="storage-analytics"></a>Lagringsanalys

Med Azure Storage Analytics kan du logga och skapa statistik för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot.

Om du vill använda Storage Analytics, måste du aktivera det individuellt för varje tjänst som du vill övervaka. Du kan aktivera det från den [Azure-portalen](https://portal.azure.com). Mer information finns i [övervaka ett lagringskonto i Azure-portalen](storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd den [ange egenskaper för Blob Service](/rest/api/storageservices/set-blob-service-properties), [ange egenskaper för kötjänst](/rest/api/storageservices/set-queue-service-properties), [ange egenskaper för tabellen](/rest/api/storageservices/set-table-service-properties), och [ange egenskaper](/rest/api/storageservices/Get-File-Service-Properties)åtgärder för att aktivera Storage Analytics för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkänd tabeller (för mått), som kan nås med hjälp av Blob- och Table service API: er.

Lagringsanalys har en 20 TB gräns för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Läs mer om lagringskontogränser [skalbarhet för lagring av Azure- och prestandamål](storage-scalability-targets.md).

Utförliga instruktioner om hur du använder Storage Analytics och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem, se [övervaka, diagnostisera och Felsök Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Faktureringen för Storage Analytics

Alla mätvärden skrivs av tjänster i ett lagringskonto. Därmed kan är varje skrivåtgärd som utförs av Storage Analytics fakturerbara. Dessutom är mängden lagringsutrymme som används av mätvärden också faktureringsbara.

Följande åtgärder utförs av Storage Analytics är fakturerbara:

* Begäranden om att skapa blobar för loggning.
* Begäranden om att skapa tabellentiteter för mått.

Om du har konfigurerat en databevarandeprincip, debiteras du inte för ta borttagningstransaktioner när Lagringsanalys tar bort gamla data för loggning och mått. Ta borttagningstransaktioner från en klient är dock faktureringsbara. Läs mer om principer för kvarhållning [ställa in en bevarandeprincip för Storage Analytics Data](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Förstå debiterbara begäranden

Varje begäran som görs till ett konto lagringstjänst är fakturerbara eller icke-fakturerbara. Lagringsanalys loggar varje enskild begäran till en tjänst, inklusive ett statusmeddelande som anger hur begäran behandlades. På samma sätt lagrar Storage Analytics mätvärden för både en tjänst och API-åtgärder för tjänsten, inklusive procenttal och antalet vissa statusmeddelanden. Tillsammans de här funktionerna kan hjälpa dig att analysera din debiterbara begäranden, gör förbättringar för ditt program och diagnostisera problem med förfrågningar till dina tjänster. Läs mer om fakturering, [förstå Azure Storage-fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

När du granskar data i Storage Analytics kan du använda tabellerna i den [Storage Analytics loggade åtgärder och statusmeddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) avsnittet för att avgöra vilka begäranden som fakturerbara. Du kan sedan jämföra dina loggar och mätdata till statusmeddelanden för att se om du debiterades för en viss begäran. Du kan också använda tabellerna i det föregående avsnittet för att undersöka tillgänglighet för en lagringstjänst eller för enskilda API-åtgärden.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagringskonto i Azure portal](storage-monitor-storage-account.md)
* [Mätvärden i Storage Analytics](storage-analytics-metrics.md)
* [Loggningen i Storage Analytics](storage-analytics-logging.md)
