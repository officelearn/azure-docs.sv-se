---
title: App Service-plan översikt – Azure | Microsoft Docs
description: Lär dig hur App Service-planer för Azure App Service och hur de kan hjälpa din hanteringsmiljö.
keywords: App service, azure app service, skala, skalbar, skalbarhet, app service-plan, app service-kostnad
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60839019"
---
# <a name="azure-app-service-plan-overview"></a>Översikt av Azure App Service-plan

I App Service körs en app i en _App Service-plan_. En App Service-plan definierar en uppsättning beräkningsresurser som en webbapp ska köra. Dessa beräkningsalternativ resurser kan jämföras med den [ _servergruppen_ ](https://wikipedia.org/wiki/Server_farm) i konventionella webbvärdtjänster. En eller flera appar kan konfigureras för att köras på samma datorresurser (eller i samma App Service-plan).

När du skapar en App Service plan inom en viss region (till exempel Europa, västra), skapas en uppsättning beräkningsresurser för den här planen i den regionen. Alla appar som du placerar i App Service-planen körs på de beräkningsresurser som definieras av App Service-planen. Varje App Service-plan definierar:

- Region (USA, västra, USA, östra osv.)
- Antalet Virtuella datorinstanser
- Storleken på VM-instanser (liten, medel, stor)
- Prisnivå (kostnadsfri, delad, Basic, Standard, Premium, PremiumV2, isolerad, förbrukning)

Den _prisnivån_ för en App Service plan avgör vilka App Service-funktioner som du får och hur mycket du betala för abonnemanget. Det finns ett antal kategorier prisnivåer:

- **Delade compute**: **Kostnadsfria** och **delad**, två grundläggande nivåer, kör en app på samma Azure-VM som andra App Service-appar, inklusive appar från andra kunder. Dessa nivåer allokera CPU kvoter till varje app som körs på delade resurser och resurserna som inte skala ut.
- **Dedikerad beräkning**: Den **grundläggande**, **Standard**, **Premium**, och **PremiumV2** nivåer köra appar på dedikerade virtuella Azure-datorer. Endast appar i samma App Service-plan delar samma beräkningsresurser. Ju högre nivå, flera VM-instanser är tillgängliga för skalbar.
- **Isolerade**: Den här nivån körs dedikerade virtuella Azure-datorer på dedikerade virtuella Azure-nätverk, vilket ger isolering av nätverk ovanpå beräkning isolering till dina appar. Det ger högsta skalbarhetsfunktionerna.
- **Förbrukning**: Den här nivån är endast tillgänglig för [funktionsappar](../azure-functions/functions-overview.md). Skalningen av funktionerna dynamiskt beroende på arbetsbelastningen. Mer information finns i [Azure Functions jämförelse av värdplaner](../azure-functions/functions-scale.md).

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Varje nivå innehåller också en delmängd av App Service-funktioner. Dessa funktioner inkluderar anpassade domäner och SSL-certifikat, automatisk skalning, distributionsplatser, säkerhetskopieringar, Traffic Manager-integrering med mera. Ju högre nivå, fler funktioner är tillgängliga. Om du vill veta vilka funktioner som stöds i varje prisnivå [information om App Service-plan](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Den nya **PremiumV2** prisnivån ger [dv2-datorer](../virtual-machines/windows/sizes-general.md#dv2-series) med snabbare processorer, SSD-lagring och dubbel minne-till-kärna-kvot jämfört med **Standard** nivå. **PremiumV2** stöder också högre skala tack vare ökad instansantal samtidigt som alla avancerade funktioner som ingår i standardprenumerationen. Alla funktioner som är tillgängliga i den befintliga **Premium** nivå som ingår i **PremiumV2**.
>
> Precis som andra dedikerade nivåer, tre storlekar som är tillgängliga för den här nivån:
>
> - Liten (en CPU-kärna, 3,5 GiB minne) 
> - Medel (två CPU-kärnor, 7 GiB minne) 
> - Stor (fyra CPU-kärnor, 14 GiB minne)  
>
> För **PremiumV2** prisinformationen hittar du i [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).
>
> Du kommer igång med den nya **PremiumV2** prisnivå, se [konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hur sker min app köra och skala?

I den **kostnadsfri** och **delad** nivåer, en app tar emot CPU-minuter på en delad VM-instans och det går inte att skala ut. I andra nivåer, en app körs och skalar på följande sätt.

När du skapar en app i App Service kan placeras den i en App Service plan. När appen körs som den körs på alla VM-instanser som konfigurerats i App Service-planen. Om flera appar finns i samma App Service-planen måste delar alla samma VM-instanser. Om du har flera distributionsplatser för en app kan alla distributionsplatser också att köra på samma VM-instanser. Om du aktiverar diagnostikloggar, säkerhetskopiera eller köra WebJobs använda de också CPU-cykler och minne på dessa VM-instanser.

På så sätt kan är skalningsenhet för App Service-appar i App Service-planen. Om planen som är konfigurerad för att köra fem VM-instanser, kör alla appar i planen på alla fem instanser. Om planen som är konfigurerad för automatisk skalning, och sedan alla appar i planen skalas ut tillsammans baserat på inställningarna för automatisk skalning.

Information om att skala ut en app finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Hur mycket kostar min App Service-plan?

Det här avsnittet beskrivs hur App Service-appar faktureras. Mer detaljerad, regionspecifika prisinformation finns i [priser för Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).

Undantag för **kostnadsfri** nivå, en App Service plan bedriver timme beräkningsresurser som används.

- I den **delad** nivå, varje app som tar emot en kvot på CPU-minuter så _varje app_ debiteras per timme för CPU-kvot.
- I den särskilda compute nivåer (**grundläggande**, **Standard**, **Premium**, **PremiumV2**), App Service-planen definierar antalet virtuella datorer instanser som apparna som skalas till, så _varje virtuell datorinstans_ i App Service plan har timme. Dessa VM-instanser debiteras samma oavsett hur många appar körs på dem. Du kan undvika oväntade kostnader, se [rensa en App Service plan](app-service-plan-manage.md#delete).
- I den **isolerad** nivå, App Service Environment definierar antalet isolerad arbetare som kör dina appar och _varje worker_ debiteras per timme. Det finns dessutom en timvis grundavgift för löpande App Service Environment själva. 
- (Endast azure Functions) Den **förbrukning** nivån dynamiskt allokerar VM-instanser för att underhålla en funktionsapp arbetsbelastning och dynamiskt debiteras per sekund av Azure. Mer information finns i [prissättning för Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Du debiteras inte för att använda App Service-funktioner som är tillgängliga för dig (konfigurera anpassade domäner, SSL-certifikat, distributionsplatser, säkerhetskopior osv.). Undantagen är:

- App Service-domäner - betalar du när du köper en i Azure och när du förnyar det varje år.
- App Service-certifikat – du betalar när du köper en i Azure och när du förnyar det varje år.
- IP-baserad SSL-anslutningar – där är en timavgift för varje IP-baserad SSL-anslutning, men vissa **Standard** nivå eller senare får du en IP-baserad SSL anslutning utan kostnad. SNI-baserad SSL-anslutningar är kostnadsfria.

> [!NOTE]
> Om du integrerar App Service med en annan Azure-tjänst kan du behöva överväga avgifter från följande tjänster. Om du använder Azure Traffic Manager att skala din app geografiskt, Azure Traffic Manager även avgifter baserat på din användning. Om du vill beräkna dina kostnader för cross-tjänster i Azure måste du se [prisberäkning](https://azure.microsoft.com/pricing/calculator/). 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Vad händer om min app behöver fler funktioner eller funktioner?

App Service-planen kan skalas upp eller ned när som helst. Det är lika enkelt som att ändra prisnivån för planen. Du kan välja en lägre prisnivå först och skala upp senare när du behöver fler App Service-funktioner.

Du kan till exempel börja testa din webbapp i en **kostnadsfri** App Service plan och betala ingenting. När du vill lägga till din [anpassat DNS-namn](app-service-web-tutorial-custom-domain.md) till web-app bara skala din plan upp till **delad** nivå. Senare, när du vill lägga till en [anpassat SSL-certifikat](app-service-web-tutorial-custom-ssl.md), skala din plan upp till **grundläggande** nivå. När du vill ha [mellanlagringsmiljöer](deploy-staging-slots.md), skala upp till **Standard** nivå. När du behöver fler kärnor, minne eller lagring kan du skala upp till en större VM-storlek i samma nivå.

Samma fungerar i tvärtom. När du känner du inte längre behöver funktioner eller funktioner i en högre nivå, du kan skala till en lägre nivå, vilket sparar du pengar.

Information om att skala upp App Service-planen finns i [skala upp en app i Azure](web-sites-scale.md).

Om din app är i samma App Service-planen med andra appar kan du förbättra appens prestanda genom att isolera beräkningsresurserna. Du kan göra det genom att flytta appen till en separat App Service-plan. Mer information finns i [flytta en app till en annan App Service-plan](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Ska jag placera en app i en ny plan eller en befintlig plan?

Eftersom du betalar för beräkningsresurser App Service-planen allokerar (se [hur mycket kostar min App Service-plan?](#cost)), potentiellt kan du spara pengar genom att placera flera appar i en App Service-plan. Du kan fortsätta att lägga till appar i en befintlig plan så länge planen som har tillräckligt med resurser för att hantera belastningen. Tänk dock på att appar i samma App Service-planen som alla har samma beräkningsresurser. För att avgöra om den nya appen har resurserna som krävs, måste du förstå kapacitet för befintliga App Service-planen och den förväntade belastningen för den nya appen. Överbelastning en App Service plan kan eventuellt medföra driftstopp för dina nya och befintliga appar.

Isolera din app till en ny App Service-planen när:

- Appen är resurskrävande.
- Du vill skala appen oberoende av de andra apparna den befintliga planen.
- Appen måste resurs i en annan geografisk region.

På så sätt som du kan tilldela en ny uppsättning resurser för din app och få större kontroll över dina appar.

## <a name="manage-an-app-service-plan"></a>Hantera en App Service-plan

> [!div class="nextstepaction"]
> [Hantera en App Service-plan](app-service-plan-manage.md)
