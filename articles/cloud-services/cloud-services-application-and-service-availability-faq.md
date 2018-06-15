---
title: Program- och problem med tjänsters tillgänglighet för Microsoft Azure Cloud Services FAQ | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om program och tjänstetillgänglighet för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 49576aa99f6cd505648e33348b89e502bea9d5c4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34068121"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Program- och problem med tjänsters tillgänglighet för Azure Cloud Services: vanliga frågor (FAQ)

Den här artikeln innehåller vanliga frågor och svar om programmet och problem med tjänsters tillgänglighet för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också kontakta den [Cloud Services VM-storlek sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Min roll har återvunnits. Har det skett någon uppdatering distribuerat för tjänst i molnet?
Ungefär släpper en gång i månaden, Microsoft en ny gäst-OS-version för Windows Azure PaaS virtuella datorer. Gäst-OS är endast en sådan uppdatering i pipelinen. En version kan påverkas av flera faktorer. Dessutom körs Azure på hundratals tusentals datorer. Därför är det omöjligt att förutse exakt datum och tid när rollerna kommer att startas om. Vi uppdatera den gäst OS uppdatera RSS-Feed med den senaste informationen som vi har, men du bör som rapporterats tid att ungefärligt värde. Vi vet att det här är problematisk för kunderna och arbetar med en plan för att begränsa eller exakt tid omstarter.

Mer information om de senaste uppdateringarna för Gästoperativsystem, finns i [Azure gäst-OS-versioner och SDK-kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).

Användbar information om startas om och länkar till teknisk information om uppdateringar med gästen och Värdoperativsystem bloggposten MSDN [roll-instansen startas om på grund av OS-uppgraderingar](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Varför den första begäranden till min molntjänst när tjänsten har varit inaktiv under en viss tid tar det längre tid än vanligt?
När webbservern tar emot den första begäranden, först kompilerar koden och bearbetar begäran. Det är därför den första begäranden tar längre tid än den andra. Som standard hämtar programpoolen stängs av i fall av användaren inaktivitet. Programpoolen kommer också att återanvändas som standard var 1,740 minuter (29 timmar).

Internet Information Services (IIS)-programmet pooler återanvändas med jämna mellanrum att undvika instabilt tillstånd som kan leda till att programmet kraschar, låser sig, och minnesläckor.

Följande dokument hjälper dig att förstå och undvika det här problemet:
* [Åtgärda långsam första last för IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webb programmet första förfrågan efter-programpool återvinning långsamt](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Om du vill ändra standardbeteendet för IIS, behöver du använda Start uppgifter eftersom om du manuellt koppla ändringar till Webbroll instanser ändringarna slutligen försvinner.

Mer information finns i [hur du konfigurerar och kör Start-aktiviteter för en molntjänst](cloud-services-startup-tasks.md).
