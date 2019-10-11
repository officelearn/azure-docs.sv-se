---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263317"
---
|Egenskap  |Standard | Beskrivning |
|---------|---------|---------| 
|routePrefix|api|Det väg-prefix som gäller för alla vägar. Använd en tom sträng för att ta bort standardprefixet. |
|maxOutstandingRequests|200<sup>*</sup>|Det maximala antalet väntande begär Anden som innehas vid en specifik tidpunkt. Den här gränsen omfattar begär Anden som har placerats i kö, men som inte har börjat köras, samt alla pågående körningar. Inkommande begär anden över den här gränsen avvisas med ett svar på 429 "för upptagen". Det gör det möjligt för anropare att använda tidsbaserade återförsöks strategier och hjälper dig också att kontrol lera maximal fördröjning för begäran. Detta styr endast köer som inträffar inom skript värdens körnings Sök väg. Andra köer, t. ex. ASP.NET, kommer fortfarande att gälla och påverkas inte av den här inställningen. <sup>*</sup> Standardvärdet för version 1. x är obundet (`-1`). Standardvärdet för version 2. x i en förbruknings plan är 200. Standardvärdet för version 2. x i en dedikerad plan är obundet (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Maximalt antal http-funktioner som ska köras parallellt. På så sätt kan du kontrol lera samtidighet, vilket kan hjälpa dig att hantera resursutnyttjande. Du kan till exempel ha en http-funktion som använder många system resurser (minne/processor/Sockets) så att den orsakar problem när samtidigheten är för hög. Eller så kanske du har en funktion som gör utgående begär anden till en tjänst från tredje part, och dessa anrop måste vara begränsade. I dessa fall kan det hjälpa att tillämpa en begränsning. <sup>*</sup> Standardvärdet för version 1. x är obundet (`-1`). Standardvärdet för version 2. x i en förbruknings plan är 100. Standardvärdet för version 2. x i en dedikerad plan är obundet (`-1`).|
|dynamicThrottlesEnabled|Sant<sup>*</sup>|När den här inställningen är aktive rad kommer pipeline för bearbetning av begär Anden att regelbundet kontrol lera system prestanda räknare som anslutningar/trådar/processer/minne/processor/osv. om någon av dessa räknare är över en inbyggd hög tröskel (80%), kommer begäran att avvisades med ett svar på 429 "upptagen" tills räknarna (erna) återgår till normala nivåer. <sup>*</sup> Standardvärdet för version 1. x är falskt. Standardvärdet för version 2. x i en förbruknings plan är sant. Standardvärdet för version 2. x i en dedikerad plan är falskt.|
