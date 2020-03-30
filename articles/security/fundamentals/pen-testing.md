---
title: Pennprovning | Microsoft-dokument
description: Artikeln innehåller en översikt över penetrationstestprocessen (pentest) och hur du utför pentest mot dina appar som körs i Azure-infrastruktur.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 3a2addce83862ef109089f1474330f3821daaed7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726707"
---
# <a name="penetration-testing"></a>Penetrationstestning
En av fördelarna med att använda Azure för programtestning och distribution är att du snabbt kan få miljöer skapade. Du behöver inte oroa dig för rekvisitionering, förvärv och "racking och stapling" din egen lokala hårdvara.

Detta är bra - men du måste fortfarande se till att du utför din normala säkerhet due diligence. En av de saker du förmodligen vill göra är penetrationstest de program som du distribuerar i Azure.

Du kanske redan vet att Microsoft utför [penetrationstester av vår Azure-miljö](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Detta hjälper till att driva Azure-förbättringar.

Vi behöver inte testa din ansökan för dig, men vi förstår att du vill och behöver utföra tester på dina egna applikationer. Det är bra, för när du ökar säkerheten för dina program hjälper du till att göra hela Azure-ekosystemet säkrare.

Från och med den 15 juni 2017 behöver Microsoft inte längre förhandsgodkännande för att genomföra ett penetrationstest mot Azure-resurser. Kunder som vill formellt dokumentera kommande penetrationstestningsåtaganden mot Microsoft Azure uppmanas att fylla i [formuläret för informationsmeddelanden](https://portal.msrc.microsoft.com/en-us/engage/pentest)för Azure Service Penetration Testing. Den här processen är endast relaterad till Microsoft Azure och gäller inte för någon annan Microsoft Cloud Service.

>[!IMPORTANT]
>När du meddelar Microsoft om penntestningsaktiviteter krävs inte längre att kunderna fortfarande måste följa [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://technet.microsoft.com/mt784683).

Standardtester som du kan utföra är:

* Tester på dina slutpunkter för att avslöja [OWASP:s 10-säkerhetsproblem (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzz-testning](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) av dina slutpunkter
* [Portskanning](https://en.wikipedia.org/wiki/Port_scanner) av slutpunkterna

En typ av test som du inte kan utföra är någon form av [Denial of Service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) attack. Detta inkluderar att initiera en DoS-attack själv, eller utföra relaterade tester som kan bestämma, demonstrera eller simulera någon typ av DoS-attack.

## <a name="next-steps"></a>Nästa steg

- Om du vill formellt dokumentera en kommande penetrationstestning mot dina program som finns i Microsoft Azure, gå vidare till [penetrationstestningsreglerna](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) för engagemang och fyll i testmeddelandeformuläret.
