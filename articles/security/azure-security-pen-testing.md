---
title: Penntestning | Microsoft Docs
description: Artikeln innehåller en översikt över penetrationstest (pentest) processen och hur du utför pentest mot program som körs i Azure-infrastrukturen.
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 4096cf3a44b7c32ed94fdd2ef5dcbad9db08a386
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42058479"
---
# <a name="pen-testing"></a>Penntestning
En av fördelarna med att använda Azure för Programtestning och distribution är att du snabbt miljöer som skapats.  Du behöver inte bekymra dig om rekvisition, hämtar, och ”del och stapling” en egen lokal maskinvara.

Det här är bra – men du måste se till att du utför vanliga säkerheten sistone. En av de saker som du behöver göra är att penetrationstester testa program som du distribuerar i Azure.

Du kanske redan vet att Microsoft utför [penetrationstester av vårt Azure-miljön](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Det här hjälper dig att hantera Azure-förbättringar.

Vi inte pen testa ditt program för dig, men vi förstår att du vill ha och behöver utföra penetrationstestning på dina egna program. Det är bra, eftersom när du ökar säkerheten för dina program genom att skydda hela Azure-ekosystemet.

Hur ska du göra?

Från och med den 15 juni 2017 Microsoft inte längre kräver ett förhandsgodkännande att genomföra en penetrationstester tester mot Azure-resurser. Kunder som vill formellt dokumentet kommande penetrationstest engagements mot Microsoft Azure uppmuntras att fylla i [Azure Penetrationstester testning tjänstmeddelande formuläret](https://portal.msrc.microsoft.com/en-us/engage/pentest). Den här processen är bara relaterad till Microsoft Azure och kan inte användas på andra Microsoft-Molntjänsten.

>[!IMPORTANT]
>Medan meddela Microsoft av penetrationstestning aktiviteter krävs inte längre kunder måste fortfarande vara kompatibel med den [Microsoft Cloud Unified Penetrationstester testning regler för Engagement](https://technet.microsoft.com/mt784683).

Standardtest som du kan utföra är:

* Tester på dina slutpunkter som avslöja den [OWASP Open Web Application Security Project () de främsta 10 sårbarheter](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzz testning](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) av dina slutpunkter
* [Skanna](https://en.wikipedia.org/wiki/Port_scanner) av dina slutpunkter

En typ av test som du inte kan utföra är någon typ av [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) attack. Detta inkluderar initierar en DoS-attack, eller utföra relaterade tester som kan fastställa, visar eller simulera någon typ av DoS-attack.

## <a name="next-steps"></a>Nästa steg

- Är du redo att sätta igång med penna testa dina program på Microsoft Azure? Om så är fallet och sedan gå på till de [Penetrationstester testning regler för Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) och Fyll i formuläret testning meddelande.
