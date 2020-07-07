---
title: Pen-testning | Microsoft Docs
description: Artikeln innehåller en översikt över processen för inträngande test (pentest) och hur du utför pentest mot dina appar som körs i Azure-infrastrukturen.
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
ms.author: terrylan
ms.openlocfilehash: 413ec8b121838a4ffac4119421ec3266e141618b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80549256"
---
# <a name="penetration-testing"></a>Testning av inträngning
En av fördelarna med att använda Azure för program testning och distribution är att du snabbt kan hämta miljöer som skapats. Du behöver inte bekymra dig om rekvisitioner, förvärv och "rackering och stapling" av din egen lokala maskin vara.

Detta är bra – men du behöver fortfarande se till att du utför din normala noggrannhet av säkerhet. Ett av de saker som du förmodligen vill göra är inträngande testa de program som du distribuerar i Azure.

Du kanske redan vet att Microsoft utför [inträngande tester av vår Azure-miljö](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Detta hjälper till att öka Azure-förbättringarna.

Vi kan inte tränga in ditt program åt dig, men vi förstår att du vill och behöver testa dina egna program. Det är bra eftersom när du förbättrar säkerheten för dina program, så att du kan göra hela Azure-eko systemet säkrare.

Från och med den 15 juni 2017 kräver Microsoft inte längre för hands godkännande för att genomföra ett inträngande test mot Azure-resurser. Kunder som vill dokumentera kommande inträngande test åtaganden mot Microsoft Azure uppmuntras att fylla i [formuläret för testning av Azure-tjänstens meddelande](https://portal.msrc.microsoft.com/en-us/engage/pentest). Den här processen är bara relaterad till Microsoft Azure och gäller inte för någon annan Microsoft Cloud tjänst.

>[!IMPORTANT]
>När du meddelar Microsoft om Penn testnings aktiviteter behöver kunderna fortfarande följa [Microsoft Cloud enhetligt inträngande test regler för engagemang](https://technet.microsoft.com/mt784683).

Standard test som du kan utföra är:

* Tester på dina slut punkter för att få fram de [10 viktigaste säkerhets problemen i det öppna webb programmet säkerhets projekt (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Fuzz-testning](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) av dina slut punkter
* [Port genomsökning](https://en.wikipedia.org/wiki/Port_scanner) av dina slut punkter

En typ av test som du inte kan utföra är någon typ av dos [-attack (Denial of Service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Detta innefattar att initiera en DoS-attack eller utföra relaterade tester som kan fastställa, demonstrera eller simulera alla typer av DoS-attacker.

## <a name="next-steps"></a>Nästa steg

- Om du vill dokumentera en kommande inträngande testning mot dina program som finns på Microsoft Azure, gå vidare till [inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) och fyll i formuläret för testning av meddelanden.
