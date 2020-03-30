---
title: Vanliga frågor om program- och tjänsttillgänglighet
titleSuffix: Azure Cloud Services
description: I den här artikeln visas vanliga frågor om program- och tjänsttillgänglighet för Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c294d4583ba2690e1f4952441ffb43bff1459059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386926"
---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problem med program- och tjänsttillgänglighet för Azure Cloud Services: Vanliga frågor och svar (vanliga frågor)

Den här artikeln innehåller vanliga frågor och svar om problem med program- och tjänsttillgänglighet för [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Du kan också läsa [sidan VM-storlek](cloud-services-sizes-specs.md) för molntjänster för storleksinformation.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Min roll blev återvunnen. Har det uppdaterats för min molntjänst?
Ungefär en gång i månaden släpper Microsoft en ny gästoperativosversion för virtuella windows Azure PaaS-datorer.Guest OS är bara en sådan uppdatering på gång. En release kan påverkas av många andra faktorer. Dessutom körs Azure på hundratusentals datorer. Därför är det omöjligt att förutsäga exakt datum och tid när dina roller kommer att startas om. Vi uppdaterar RSS-flödet för gäst-OS-uppdatering med den senaste informationen vi har, men du bör anse att rapporterad tid är ett ungefärligt värde. Vi är medvetna om att detta är problematiskt för kunderna och arbetar på en plan för att begränsa eller exakt tid omstarter.

Fullständig information om de senaste uppdateringarna av gästoperativsystem finns i [Azure Guest OS-versioner och SDK-kompatibilitetsmatris](cloud-services-guestos-update-matrix.md).

Användbar information om omstarter och pekare till teknisk information om uppdateringar av gäst- och värdoperatoroper, finns i msdn-blogginläggets [rollinstans omstarter på grund av OS-uppgraderingar](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Varför tar den första begäran till min molntjänst efter att tjänsten har varit inaktiv under en längre tid än vanligt?
När webbservern tar emot den första begäran kompileras koden först om och bearbetas sedan. Det är därför den första begäran tar längre tid än de andra. Som standard stängs apppoolen av vid användarinaktivitet. Apppoolen återvinns också som standard var 1 740:e minut (29 timmar).

IIS-programpooler (Internet Information Services) kan regelbundet återvinnas för att undvika instabila tillstånd som kan leda till programkrascher, låser sig eller minnesläckor.

Följande dokument hjälper dig att förstå och minska problemet:
* [Åtgärda långsam initial belastning för IIS](https://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis)
* [IIS 7.5 webbprogram första begäran efter app-pool återvinna mycket långsam](https://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow)

Om du vill ändra standardbeteendet för IIS måste du använda startuppgifter, för om du manuellt tillämpar ändringar i webbrollinstanserna kommer ändringarna så småningom att gå förlorade.

Mer information finns i [Så här konfigurerar och kör du startuppgifter för en molntjänst](cloud-services-startup-tasks.md).
