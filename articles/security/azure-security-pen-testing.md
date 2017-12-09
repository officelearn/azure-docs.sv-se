---
title: Testa penna | Microsoft Docs
description: "Artikeln innehåller en översikt över intrång testa process (pentest) och hur utför pentest mot dina appar som körs i Azure-infrastrukturen."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 3ad22e78693c54c62f9230f7f52460e01e5e0022
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="pen-testing"></a>Penna testning
En av fördelarna med att använda Azure för att testa program och distribution är att du snabbt kan få miljöer som har skapats.  Du behöver inte bry dig om rekvisition, hämta, och ”förflyttning och stapling” lokal maskinvara.

Det är bra – men du måste se till att du utför vanliga säkerheten vederbörlig möda åt. En av de saker som du behöver göra är intrång testa program som du distribuerar i Azure.

Du kanske redan vet att Microsoft utför [intrång testning av våra Azure-miljön](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Det innebär att driva Azure förbättringar. 

Vi inte pennan testa ditt program för dig, men vi förstår att du vill ha och behöver utföra penna tester på dina program. Det beror användbart när du förbättrar säkerheten för dina program du gör hela Azure ekosystemet säkrare.

När du penna testar dina program, kan det ser ut som en attack till oss. Vi [kontinuerligt övervakar](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) för angreppsmönster och starta en process för incidenter om vi behöver. Det hjälper inte kan du och det hjälper inte oss om vi utlöser en incidenter på grund av dina egna på grund av fordringar penna tester.

Hur ska du göra?

När du är redo att penna testa ditt program för Azure som värd, har du möjlighet att [berätta för oss](https://portal.msrc.microsoft.com/en-us/engage/pentest). När ett meddelande, kommer Microsoft inte oavsiktligt Stäng du (till exempel blockerar IP-adressen som du testar från). Dina tester måste motsvara Azure pennan testa villkor och villkor som beskrivs i [Microsoft Cloud Unified intrång testning regler för Engagement](https://technet.microsoft.com/en-us/mt784683).

Standardtester som du kan utföra inkluderar:

* Tester på dina slutpunkter för att upptäcka de [öppna Web Application säkerhet projekt (OWASP) topp 10 säkerhetsrisker](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testa Fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) av dina slutpunkter
* [Port-scanning](https://en.wikipedia.org/wiki/Port_scanner) av dina slutpunkter

En typ av test som du inte kan utföra är någon typ av [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) attack. Detta innefattar initierar en DoS-attack eller utför relaterade testerna som kan avgöra, visa eller simulera någon typ av DoS-attacker.

## <a name="next-steps"></a>Nästa steg

- Är du redo att sätta igång med penna testa dina program finns i Microsoft Azure? Om så är fallet och sedan öppna på över den [intrång Test översikt](https://technet.microsoft.com/library/mt784683.aspx) (och klicka på knappen Skapa ett testning begära längst ned på sidan. 