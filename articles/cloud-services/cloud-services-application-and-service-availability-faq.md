---
title: Frågor om program-och tjänst tillgänglighet för Microsoft Azure Cloud Services vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller vanliga frågor om program-och tjänst tillgänglighet för Microsoft Azure Cloud Services.
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
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 0d70e52b77c7f34444ce12f84f17a5ed2d1afe46
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68941783"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problem med program-och tjänst tillgänglighet för Azure Cloud Services: Vanliga frågor och svar

Den här artikeln innehåller vanliga frågor om problem med program-och tjänst tillgänglighet för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också se storleks information på [sidan Cloud Services virtuell dator storlek](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Min roll har återvunnits. Fanns några uppdateringar för min moln tjänst?
Ungefär en gång i månaden släpper Microsoft en ny gäst operativ system version för virtuella Windows Azure PaaS-datorer. Gäst operativ systemet är bara en sådan uppdatering i pipelinen. En version kan påverkas av många andra faktorer. Dessutom körs Azure på hundratals tusentals datorer. Därför går det inte att förutsäga exakt det datum och den tid som dina roller kommer att starta om. Vi uppdaterar RSS-flödet för gäst operativ systemet med den senaste informationen som vi har, men du bör överväga att rapportera tid till ett ungefärligt värde. Vi är medvetna om att detta är problematiskt för kunder och arbetar på en plan för att begränsa eller precis tid för omstarter.

Fullständig information om de senaste uppdateringarna för gäst operativ systemet finns i [Azure gäst operativ system versioner och SDK-kompatibilitet](cloud-services-guestos-update-matrix.md).

Information om omstarter och pekare till teknisk information om gäst-och värd operativ system uppdateringar finns i MSDN blogg inlägg [roll instans startar om på grund av OS-uppgraderingar](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Varför tar det den första begäran till min moln tjänst när tjänsten har varit inaktiv under en viss tid längre tid än vanligt?
När webb servern tar emot den första begäran kompilerar den först om koden och bearbetar sedan begäran. Det är därför den första begäran tar längre tid än de andra. Som standard stängs app-poolen i händelse av inaktivitet. Programpoolen återanvänds också som standard var 1 740: e minut (29 timmar).

Internet Information Services (IIS) programpooler kan regelbundet återvinnas för att undvika instabila tillstånd som kan leda till att program kraschar, låser sig eller minnes läckor.

Följande dokument hjälper dig att förstå och minimera det här problemet:
* [Korrigera långsam inledande belastning för IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7,5-webbprogramets första begäran efter att app-poolens återvinning är mycket långsamt](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Om du vill ändra standard beteendet för IIS måste du använda Start åtgärder, eftersom om du manuellt tillämpar ändringarna på webb roll instanserna går ändringarna förlorade.

Mer information finns i [så här konfigurerar och kör du Start åtgärder för en moln tjänst](cloud-services-startup-tasks.md).
