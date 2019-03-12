---
title: Program- och problem med tjänsters tillgänglighet för vanliga frågor om Microsoft Azure Cloud Services | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om programmet och tjänstens tillgänglighet för Microsoft Azure Cloud Services.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fb4b5dde63d8c7c75419d3202d9848cd6fde8b8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547647"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Programmet och problem med tjänsters tillgänglighet för Azure Cloud Services: Vanliga frågor och svar (FAQ)

Den här artikeln innehåller vanliga frågor om programmet och problem med tjänsters tillgänglighet för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa den [VM-storlek för Cloud Services-sidan](cloud-services-sizes-specs.md) storlek information.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Min roll har återvunnits. Var det någon uppdateringen som lanseras för min molntjänst?
Ungefär släpper en gång i månaden, Microsoft en ny gäst-OS-version för Windows Azure PaaS-datorer. Gäst-OS är bara en sådan uppdatering i pipelinen. En version kan påverkas av många faktorer. Dessutom körs Azure på hundratals tusentals datorer. Därför är det omöjligt att förutsäga exakt datum och tid när dina roller startas om. Vi uppdaterar den Guest OS uppdatera RSS-Feed med den senaste informationen som vi har men du bör överväga som rapporterats tid att ungefärligt värde. Vi är medvetna om att detta är problematisk för kunderna och arbetar med en plan för att begränsa eller exakt tid omstarter.

Mer information om de senaste uppdateringarna för gäst-OS, finns i [Azures gäst-OS-versioner och SDK-kompatibilitetsöversikten](cloud-services-guestos-update-matrix.md).

Användbar information om omstarter och pekare till teknisk information av Gäst och värd-OS-uppdateringar finns i MSDN bloggposten [rollen instansen startas om på grund av OS-uppgraderingar](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Varför den första begäran till min molntjänst när tjänsten har varit inaktiv under en viss tid tar det längre tid än vanligt?
När servern tar emot den första begäran, först kompilerar koden och bearbetar begäran. Det är därför den första begäran tar längre tid än de andra. Som standard hämtar app pool stänga av i fall av användaren inaktivitet. App-poolen kommer också Papperskorgen som standard var 1,740 minut (29 timmar).

Internet Information Services (IIS)-programmet som pooler kan vara regelbundet återvinnas för att undvika instabilt tillstånd som kan leda till programmet kraschar, låser sig, och minnesläckor.

Följande dokument hjälper dig att förstå och lösa det här problemet:
* [Åtgärda långsam första last för IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webb programmet första förfrågan efter-programpool återvinning mycket långsamt](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Om du vill ändra standardbeteendet för IIS behöver du använda startåtgärder, eftersom om du manuellt tillämpar ändringarna till Web rollinstanser, ändringarna så småningom kommer att gå förlorade.

Mer information finns i [hur du konfigurerar och köra startåtgärder för en molntjänst](cloud-services-startup-tasks.md).
