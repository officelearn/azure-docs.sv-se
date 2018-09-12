---
title: Scheduler-begränsningar och standardvärden
description: Scheduler-begränsningar och standardvärden
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4be0695402b66fdb2a027bfbada0e0b26e02d36f
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378728"
---
# <a name="scheduler-limits-and-defaults"></a>Scheduler-begränsningar och standardvärden
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Scheduler-kvoter, gränser, standardinställningar och begränsningar
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Rubriken x-ms-request-id
Varje begäran som görs mot tjänsten Scheduler returnerar ett svarshuvud med namnet **x-ms-request-id**. Den här rubriken innehåller ett täckande värde som unikt identifierar begäran.

Om en begäran misslyckas konsekvent och du har verifierat att begäran korrekt formulerat, kan du använda det här värdet och rapportera felet till Microsoft. Inkludera i rapporten, värdet för x-ms-request-id, den ungefärliga tid som begäran gjordes, ID för prenumerationen, jobb-samling och/eller jobbet och vilken typ av åtgärd som begäran gjordes ett försök.

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

