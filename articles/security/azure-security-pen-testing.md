---
title: Testa penna | Microsoft Docs
description: Artikeln innehåller en översikt över intrång testa process (pentest) och hur utför pentest mot dina appar som körs i Azure-infrastrukturen.
services: security
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: a64316eda25bd02f89b5afdd7b98c0193381d023
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33893375"
---
# <a name="pen-testing"></a>Penna testning
En av fördelarna med att använda Azure för att testa program och distribution är att du snabbt kan få miljöer som har skapats.  Du behöver inte bry dig om rekvisition, hämta, och ”förflyttning och stapling” lokal maskinvara.

Det är bra – men du måste se till att du utför vanliga säkerheten vederbörlig möda åt. En av de saker som du behöver göra är intrång testa program som du distribuerar i Azure.

Du kanske redan vet att Microsoft utför [intrång testning av våra Azure-miljön](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Det innebär att driva Azure förbättringar. 

Vi inte pennan testa ditt program för dig, men vi förstår att du vill ha och behöver utföra penna tester på dina program. Det beror användbart när du förbättrar säkerheten för dina program du gör hela Azure ekosystemet säkrare.

Hur ska du göra?

Från och med den 15 juni 2017 Microsoft längre kräver godkännande på förhand att genomföra en intrång testerna mot Azure-resurser. Kunder som vill formellt dokument kommande intrång testa Användarsegmentet mot Microsoft Azure uppmuntras att fylla i den [Azure Service intrång testning Notification formuläret](https://portal.msrc.microsoft.com/en-us/engage/pentest). Den här processen är bara relaterad till Microsoft Azure och gäller inte andra Microsoft-Molntjänsten. 

>[!IMPORTANT] 
>Medan meddela Microsoft pennan testning aktiviteter krävs inte längre kunder måste fortfarande vara kompatibel med den [Microsoft Cloud Unified intrång testning regler för Engagement](https://technet.microsoft.com/mt784683). 

Standardtester som du kan utföra inkluderar:

* Tester på dina slutpunkter för att upptäcka de [öppna Web Application säkerhet projekt (OWASP) topp 10 säkerhetsrisker](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testa Fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) av dina slutpunkter
* [Port-scanning](https://en.wikipedia.org/wiki/Port_scanner) av dina slutpunkter

En typ av test som du inte kan utföra är någon typ av [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) attack. Detta innefattar initierar en DoS-attack eller utför relaterade testerna som kan avgöra, visa eller simulera någon typ av DoS-attacker.

## <a name="next-steps"></a>Nästa steg

- Är du redo att sätta igång med penna testa dina program finns i Microsoft Azure? Om så är fallet och sedan öppna på över den [intrång Test översikt](https://technet.microsoft.com/library/mt784683.aspx) (och klicka på knappen Skapa ett testning begära längst ned på sidan. 
