---
title: Schemaläggaren gränser och standardvärden
description: Schemaläggaren gränser och standardvärden
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
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867817"
---
# <a name="scheduler-limits-and-defaults"></a>Schemaläggaren gränser och standardvärden
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Schemaläggaren kvoter, gränser, standarder och begränsningar
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Huvudet x-ms-begäran-id
Alla begäranden som görs mot Schemaläggaren returnerar ett svarshuvud med namnet**x-ms-begäran-id**. Det här sidhuvudet innehåller ett täckande värde som unikt identifierar begäran.

Om en begäran konsekvent misslyckas och du har verifierat att begäran är korrekt formulerade, kan du använda det här värdet rapportera felet till Microsoft. I rapporten, inkluderar du värdet för x-ms-begäran-id, den ungefärliga tid som begäran gjordes, identifierare för prenumerationen, jobbsamlingen, och/eller jobb och typ av åtgärd som misslyckades med begäran.

## <a name="see-also"></a>Se även
 [Vad är Scheduler?](scheduler-intro.md)

 [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)

 [Komma igång med Scheduler på Azure-portalen](scheduler-get-started-portal.md)

 [Prenumerationer och fakturering i Azure Scheduler](scheduler-plans-billing.md)

 [Referens för REST-API:et för Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)

 [Hög tillgänglighet och tillförlitlighet i Azure Scheduler](scheduler-high-availability-reliability.md)

 [Utgående autentisering i Azure Scheduler](scheduler-outbound-authentication.md)

