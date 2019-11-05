---
title: App Service plan översikt – Azure | Microsoft Docs
description: Lär dig hur App Service planerar Azure App Service arbete och hur de förväntar dig din hanterings upplevelse.
keywords: App Service, Azure App Service, skalning, skalbarhet, skalbarhet, App Service-plan, App Service-kostnad
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 78440185b4a26bccc8ffb0258416a19aa929af6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470251"
---
# <a name="azure-app-service-plan-overview"></a>Översikt över Azure App Services plan

I App Service körs en app i en _App Service-plan_. En App Service-plan definierar en uppsättning beräkningsresurser som en webbapp ska köra. Dessa beräknings resurser är likvärdiga med [_Server gruppen_](https://wikipedia.org/wiki/Server_farm) i konventionella webb värdar. En eller flera appar kan konfigureras för att köras på samma dator resurser (eller i samma App Service plan).

När du skapar en App Service plan i en viss region (till exempel Västeuropa) skapas en uppsättning beräknings resurser för den planen i den regionen. De appar du infogar i den här App Service plan köra på de här beräknings resurserna som definieras av din App Service plan. Varje App Service plan definierar:

- Region (västra USA, östra USA osv.)
- Antal VM-instanser
- Storlek på virtuella dator instanser (liten, medel, stor)
- Pris nivå (kostnads fri, delad, Basic, standard, Premium, PremiumV2, isolerad)

_Pris nivån_ för en app service plan avgör vilka App Service funktioner du får och hur mycket du betalar för planen. Det finns några kategorier av pris nivåer:

- **Delad beräkning**: **kostnads fri** och **delad**, de två bas nivåerna kör en app på samma virtuella Azure-dator som andra App Service appar, inklusive appar från andra kunder. Dessa nivåer allokerar processor kvoter till varje app som körs på delade resurser och resurserna kan inte skala ut.
- **Dedikerad beräkning**: nivåerna **Basic**, **standard**, **Premium**och **PremiumV2** kör appar på dedikerade virtuella Azure-datorer. Endast appar i samma App Service plan delar samma beräknings resurser. Ju högre nivå, desto fler VM-instanser kan du skala ut.
- **Isolerad**: den här nivån kör dedikerade virtuella Azure-datorer på dedikerade virtuella Azure-nätverk. Den ger nätverks isolering ovanpå beräknings isoleringen för dina appar. Det ger de maximala skalbara funktionerna.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Varje nivå tillhandahåller också en viss delmängd av App Service funktioner. Dessa funktioner omfattar anpassade domäner och SSL-certifikat, automatisk skalning, distributions platser, säkerhets kopiering, Traffic Manager integrering med mera. Ju högre nivå, desto fler funktioner är tillgängliga. Information om vilka funktioner som stöds på varje pris nivå finns i [App Service plan information](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Den nya pris nivån för **PremiumV2** tillhandahåller [virtuella datorer i Dv2-serien](../virtual-machines/windows/sizes-general.md#dv2-series) med snabbare processorer, SSD-lagring och dubbelt minne-till-kärn-förhållande jämfört med **standard** nivån. **PremiumV2** har också stöd för högre skala via ökade instans antal samtidigt som alla avancerade funktioner som finns i standard prenumerationen fortfarande tillhandahålls. Alla funktioner som är tillgängliga i den befintliga **Premium** -nivån ingår i **PremiumV2**.
>
> På samma sätt som andra dedikerade nivåer är tre VM-storlekar tillgängliga för den här nivån:
>
> - Liten (en processor kärna, 3,5 GiB minne) 
> - Medel (två processor kärnor, 7 GiB minne) 
> - Stor (fyra processor kärnor, 14 GiB minne)  
>
> Information om **PremiumV2** -priser finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).
>
> För att komma igång med den nya pris nivån för **PremiumV2** , se [Konfigurera PremiumV2-nivå för App Service](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hur körs och skalar appen?

På nivåerna **kostnads fri** och **delad** tar en app emot CPU-minuter på en delad VM-instans och kan inte skala ut. På andra nivåer körs och skalas en app enligt följande.

När du skapar en app i App Service placeras den i ett App Service plan. När appen körs körs den på alla virtuella dator instanser som kon figurer ATS i App Service plan. Om flera appar finns i samma App Service plan, delar alla samma VM-instanser. Om du har flera distributions platser för en app körs alla distributions platser även på samma virtuella dator instanser. Om du aktiverar diagnostikloggar, utför säkerhets kopieringar eller kör WebJobs, använder de även CPU-cykler och minne på dessa VM-instanser.

På det här sättet är App Service plan skalnings enhet för App Service appar. Om planen har kon figurer ATS för att köra fem VM-instanser körs alla appar i planen på alla fem instanser. Om planen har kon figurer ATS för automatisk skalning skalas alla appar i planen ut tillsammans baserat på inställningarna för automatisk skalning.

Information om hur du skalar ut en app finns i [skala antalet instanser manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Hur mycket kostar mitt App Service plan?

I det här avsnittet beskrivs hur App Service-appar faktureras. Detaljerad information om landsspecifika priser finns i [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/).

Förutom för den **kostnads fria** nivån debiteras en app service plan per timme för de beräknings resurser som används.

- På den **delade** nivån får varje app en kvot på CPU minuter, så _varje app_ debiteras per timme för processor kvoten.
- I dedikerade beräknings nivåer (**Basic**, **standard**, **Premium**, **PremiumV2**) definierar App Service plan antalet virtuella dator instanser som APPARna skalas till, så _varje VM-instans_ i App Service plan har en timkostnad per timme. Dessa VM-instanser debiteras på samma sätt oavsett hur många appar som körs på dem. Information om hur du undviker oväntade kostnader finns i [Rensa en app service plan](app-service-plan-manage.md#delete).
- På den **isolerade** nivån definierar App Service-miljön antalet isolerade arbetare som kör dina appar, och _varje arbets tagare_ debiteras per timme. Det finns dessutom en grund avgift per timme för att köra själva App Service-miljön.

Du debiteras inte för att använda de App Service funktioner som är tillgängliga för dig (Konfigurera anpassade domäner, SSL-certifikat, distributions platser, säkerhets kopior osv.). Undantagen är:

- App Service domäner – du betalar när du köper ett i Azure och när du förnyar det varje år.
- App Service certifikat – du betalar när du köper ett i Azure och när du förnyar det varje år.
- IP-baserade SSL-anslutningar – det finns en timkostnad per timme för varje IP-baserad SSL-anslutning, men vissa **standard** nivåer eller ovan ger dig en IP-baserad SSL-anslutning utan kostnad. SNI-baserade SSL-anslutningar är kostnads fria.

> [!NOTE]
> Om du integrerar App Service med en annan Azure-tjänst kan du behöva fundera på debitering från dessa andra tjänster. Om du till exempel använder Azure Traffic Manager för att skala din app geografiskt, debiterar Azure Traffic Manager också dig utifrån din användning. Se [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/)för att beräkna dina kostnader för dina globala tjänster i Azure. 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Vad händer om min app behöver fler funktioner eller funktioner?

Din App Service plan kan skalas upp och ned när som helst. Det är lika enkelt som att ändra planens pris nivå. Du kan välja en lägre pris nivå genom att först och skala upp senare när du behöver fler App Service funktioner.

Du kan till exempel börja testa din webbapp i ett **kostnads fritt** App Service plan och betala ingenting. När du vill lägga till ett [anpassat DNS-namn](app-service-web-tutorial-custom-domain.md) till webbappen kan du skala upp det till **delad** nivå. När du senare vill [skapa en SSL-bindning](configure-ssl-bindings.md)måste du skala upp till **Basic** -nivån. Om du vill ha [mellanlagrings miljöer](deploy-staging-slots.md), skala upp till **standard** nivån. När du behöver fler kärnor, minne eller lagring skalar du upp till en större VM-storlek på samma nivå.

Samma fungerar i omvänd ordning. När du tycker att du inte längre behöver de funktioner som finns i en högre nivå kan du skala ned till en lägre nivå, vilket sparar pengar.

Information om hur du skalar upp App Service plan finns i [skala upp en app i Azure](manage-scale-up.md).

Om din app finns i samma App Service plan med andra appar, kanske du vill förbättra appens prestanda genom att isolera beräknings resurserna. Du kan göra det genom att flytta appen till en separat App Service plan. Mer information finns i [flytta en app till en annan app service plan](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Bör jag använda en app i en ny plan eller ett befintligt abonnemang?

Eftersom du betalar för de beräknings resurser som App Service plan allokerar (se [hur mycket kostar min app service plan?](#cost)) kan du spara pengar genom att placera flera appar i en app service plan. Du kan fortsätta att lägga till appar i en befintlig plan så länge planen har tillräckligt med resurser för att hantera belastningen. Tänk dock på att appar i samma App Service plan alla delar samma beräknings resurser. För att avgöra om den nya appen har de nödvändiga resurserna måste du förstå kapaciteten hos den befintliga App Service plan och den förväntade belastningen för den nya appen. Överlagring av en App Service plan kan eventuellt orsaka stillestånds tid för dina nya och befintliga appar.

Isolera din app i en ny App Service plan när:

- Appen är resurs intensiv.
- Du vill skala appen oberoende av de andra apparna i den befintliga planen.
- Appen behöver en resurs i en annan geografisk region.

På så sätt kan du allokera en ny uppsättning resurser för din app och få bättre kontroll över dina appar.

## <a name="manage-an-app-service-plan"></a>Hantera en App Service plan

> [!div class="nextstepaction"]
> [Hantera en App Service plan](app-service-plan-manage.md)
